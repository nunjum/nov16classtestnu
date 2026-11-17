# Specifications: Anticipatory Regulatory Scenario Simulator

Technical design for a single-file static web application modeling supply-chain compliance under enacted, pending, reported, and hypothetical regulations. Derived from SOURCES.md and CONCEPT-IDEA.md.

---

## 1. Architecture Overview

### 1.1 Single-File Static App (MVP)

**Tech stack:**
- HTML5 + TypeScript (compiled to ES2020)
- JSON for internal data model
- CSS3 for styling and accessibility (WCAG 2.1 AA)
- No backend or external API calls (initially)
- Single `.html` file with embedded CSS and compiled TypeScript
- LocalStorage for scenario persistence (up to 10 scenarios)

**Data flow:**
1. User imports CSV or JSON supply-chain data or manual entry via forms
2. App instantiates `RegulatoryScenario` with baseline regulatory items from SOURCES.md
3. User selects confidence-tier filters (show/hide HYPOTHETICAL, REPORTED_DRAFT, etc.)
4. Rule engine evaluates each entity and component against ordered rules (§3)
5. UI renders compliance status, triggeredBy list, cost/delay heuristics
6. User modifies assumptions and re-runs; app maintains audit trail
7. User exports results as JSON or CSV

**Offline limitations (MVP):**
- Cannot fetch live Sayari entity data (users paste manually or export from Sayari)
- Cannot update Covered List or 1260H list without rebuilding
- Cannot call external APIs for real-time regulatory changes

**Mitigations:**
- UI prominently displays "Last-verified: 2026-11-16" with link to SOURCES.md
- Instructions on how to update entity records (Markdown files in folder)
- Backend integration points documented (§7)

### 1.2 What a Backend Would Add

- Live Sayari API calls for current UBO, ownership %, subsidiaries, shipment records, HS codes
- Dynamic Covered List and 1260H list updates (refresh monthly or on-demand)
- User accounts and scenario sharing
- Audit logging and change tracking
- Procurement system integration (Coupa, Ariba, SAP)
- Webhook notifications if regulatory lists change

---

## 2. TypeScript/JSON Interfaces

All types defined in a single TypeScript file or module. JSON schemas can be generated.

### 2.1 Enums

```typescript
enum IntelligenceConfidenceTier {
  ENACTED_LAW = "ENACTED_LAW",           // FCC 26-50 (effective Oct 13, 2026)
  ENACTED_DESIGNATION = "ENACTED_DESIGNATION", // DoW 1260H (June 10, 2026)
  PENDING_BILL = "PENDING_BILL",         // S.5548 (introduced Sept 24, 2026)
  REPORTED_DRAFT = "REPORTED_DRAFT",     // Reuters FCC draft (unverified)
  ANALYST_ESTIMATE = "ANALYST_ESTIMATE", // Counterpoint/Cignal (unverified)
  HYPOTHETICAL = "HYPOTHETICAL"          // User-defined
}

enum VerificationStatus {
  VERIFIED = "VERIFIED",         // Primary source confirmed
  PARTIAL = "PARTIAL",           // Partially confirmed or current-to-known-date
  UNVERIFIED = "UNVERIFIED"      // Not confirmed; blocked by access or other limits
}

enum EntityDefinitionMode {
  INCORPORATION = "INCORPORATION",         // Entity domicile (e.g., China-incorporated)
  MANUFACTURING = "MANUFACTURING",       // Production location (e.g., China-manufactured)
  OWNERSHIP = "OWNERSHIP"                 // UBO/SASAC/MIIT affiliation (>= X% ownership)
}

enum ComplianceStatus {
  APPROVED = "APPROVED",                  // Passes all active regulations
  EXEMPT_US_BOM = "EXEMPT_US_BOM",        // Exempt via US-BOM threshold
  TRANSITION_WINDOW = "TRANSITION_WINDOW", // Grandfathered or in transition period
  BLOCKED = "BLOCKED",                    // Fails one or more regulations
  UNKNOWN = "UNKNOWN"                     // Insufficient data to evaluate
}

enum ListMembership {
  COVERED_LIST = "COVERED_LIST",           // FCC Covered List (ENACTED_LAW)
  LIST_1260H = "LIST_1260H",               // DoW Section 1260H (ENACTED_DESIGNATION)
  FOREIGN_ROUTERS = "FOREIGN_ROUTERS",     // Foreign-produced routers (ENACTED_LAW)
  S5548_NAMED = "S5548_NAMED",             // Named in S.5548 (PENDING_BILL)
  REUTERS_DRAFT = "REUTERS_DRAFT",         // Mentioned in Reuters draft (REPORTED_DRAFT)
  OTHER = "OTHER"
}

enum TransceiverGeneration {
  GEN_800G = "GEN_800G",    // 800G (grandfathered until year N)
  GEN_1600T = "GEN_1600T",  // 1.6T (grandfathered until year N)
  GEN_3200T = "GEN_3200T",  // 3.2T (phase-in year, default 2028)
  GEN_6400T = "GEN_6400T",  // 6.4T (phase-in year)
  LPO_CPO = "LPO_CPO"       // Coherent Optics (phase-in year)
}

enum ProcurementScope {
  FEDERAL_NSS = "FEDERAL_NSS",     // Federal national security systems
  COMMERCIAL = "COMMERCIAL",       // Commercial or international
  BOTH = "BOTH"
}
```

### 2.2 Core Data Types

```typescript
interface RegulatoryItem {
  id: string;                               // e.g., "FCC_26_50", "S5548"
  name: string;
  tier: IntelligenceConfidenceTier;
  status: VerificationStatus;               // VERIFIED, PARTIAL, UNVERIFIED
  sourceUrl?: string;                       // Link to primary source
  listMemberships?: ListMembership[];       // Which lists does this define?
  affectedEntities?: string[];              // Entity IDs explicitly named
  dateAdopted?: string;                     // ISO 8601, e.g., "2026-07-22"
  dateEffective?: string;                   // e.g., "2026-10-13"
  dateVerified: string;                     // Last-verified date, e.g., "2026-11-16"
  description: string;                      // Rule summary and scope
  ruleDefinition: RuleDefinition;           // Testable logic
}

interface RuleDefinition {
  ruleType: "COVERED_LIST_COMPONENT" | "DESIGNATION_ONLY" | "TRANSCEIVER_MODEL_BAN" | 
            "FEDERAL_PROCUREMENT" | "BOM_EXEMPTION" | "GRANDFATHERING" | "PENDING_APP_SAFE_HARBOR";
  
  // For COVERED_LIST_COMPONENT (FCC 26-50)
  logicBearingThreshold?: {
    pulsesPerSecond: number;      // >9,000
    rfEnergyForProcessing: boolean;
  };
  scope?: "NEW_AUTHORIZATIONS_ONLY";
  
  // For TRANSCEIVER_MODEL_BAN (Reuters draft)
  phasedInGeneration?: TransceiverGeneration;
  phaseInYear?: number;           // e.g., 2028, range 2027-2030
  
  // For BOM_EXEMPTION (Reuters draft)
  usSourcedBomThreshold?: number; // 0-100, default 65%
  
  // For FEDERAL_PROCUREMENT (S.5548)
  procurementScope?: ProcurementScope;
  criticalSystemsOnly?: boolean;
  transitionYears?: number;       // e.g., 5 years from enactment
  
  // For GRANDFATHERING
  grandfatheredBeforeDate?: string; // e.g., "2026-10-13"
  
  // For DESIGNATION_ONLY (DoW 1260H)
  isDesignationOnly: boolean;     // Does NOT auto-prohibit use
}

interface SayariEntityRecord {
  sayariEntityId: string;                   // e.g., "innolight", "cisco"
  name: string;
  country: string;                          // ISO 2-letter code, e.g., "CN", "US", "TW"
  ultimateBeneficialOwner?: string;         // e.g., "SASAC", "MIIT"
  ownershipPct?: number;                    // 0-100, default 100
  parentId?: string;                        // Parent company sayariEntityId (if subsidiary)
  subsidiaryDepth: number;                  // 0=parent, 1=direct subsidiary, etc.
  manufacturingLocations?: string[];        // ISO codes, e.g., ["CN", "TH"]
  hsCodes?: string[];                       // HS tariff codes
  recordSourceUrl?: string;
  retrievedAt: string;                      // ISO 8601 timestamp
  lists?: {
    coveredList?: boolean;
    list1260H?: boolean;
    foreignRouters?: boolean;
    s5548Named?: boolean;
    reutersDraft?: boolean;
    other?: { [key: string]: boolean };
  };
}

interface HardwareComponentBOM {
  partName: string;                         // e.g., "Optical Transceiver 800G QSFP"
  partNumber?: string;
  supplierEntityId: string;                 // SayariEntityRecord.sayariEntityId
  countryOfOrigin?: string;                 // Manufacturing location
  valueShare: number;                       // 0-100, % of BOM cost
  usSourceced: boolean;                     // Is supplier U.S.-based or component U.S.-made?
  logicBearing?: boolean;                   // Meets >9,000 pps or RF processing threshold?
}

interface TransceiverSku {
  skuId: string;                            // e.g., "cisco-sw-800g-v1"
  productName: string;
  makerEntityId: string;                    // OEM
  speedGen: TransceiverGeneration;
  buildLocation?: string;                   // Country of assembly
  assemblyLocation?: string;
  fccAuthorizationDate?: string;            // ISO 8601
  isFccAuthPendingBefore20261013?: boolean; // Safe harbor flag
  bomComponents: HardwareComponentBOM[];
  usSourcedBomPercentage?: number;          // Calculated from bomComponents
}

interface DatacenterImpactAssessment {
  skuId: string;
  scenarioId: string;
  complianceStatus: ComplianceStatus;
  delayMonths?: number;                     // ILLUSTRATIVE: estimated re-qualification delay
  bomCostDeltaPct?: number;                 // ILLUSTRATIVE: cost change if supplier swapped
  redesignAlerts?: string[];                // Human-readable issues
  triggeredBy?: string[];                   // List of RegulatoryItem.id causing status
  assumptions?: string[];                   // Scenario assumptions this depends on
  falsifiers?: string[];                    // Facts that would flip the status
  confidenceTierWorstCase?: IntelligenceConfidenceTier; // Weakest tier among triggeredBy
  details?: {
    logicBearingExplanation?: string;
    usBomExemptionApplies?: boolean;
    grandfatheredUntil?: string;
  };
}

interface RegulatoryScenario {
  id: string;
  name: string;
  createdAt: string;                        // ISO 8601
  updatedAt: string;
  entities: SayariEntityRecord[];
  products: TransceiverSku[];
  regulatoryItems: RegulatoryItem[];        // Which regulations are active?
  activeConfidenceTiers: IntelligenceConfidenceTier[]; // Tiers to include
  
  // User assumptions (editable)
  assumptions: {
    entityDefinitionMode: EntityDefinitionMode;
    ownershipThreshold?: number;            // For OWNERSHIP mode, default 50%
    transitionYearsFromEnactment?: number;  // For bills, default 5
    phaseInYear?: number;                   // For Reuters draft, default 2028
    usSourcedBomThreshold?: number;         // For BOM exemption, default 65%
    procurementScope?: ProcurementScope;    // FEDERAL_NSS, COMMERCIAL, or BOTH
  };
  
  // Outputs
  assessments: DatacenterImpactAssessment[];
  summaryMetrics?: {
    totalSkus: number;
    approvedSkus: number;
    blockedSkus: number;
    transitionWindowSkus: number;
    unknownSkus: number;
    worstCaseConfidenceTier: IntelligenceConfidenceTier;
  };
  
  // Audit trail
  auditLog?: Array<{
    timestamp: string;
    action: string;        // e.g., "Updated ownershipThreshold to 50%"
    actor?: string;        // "user", "system"
  }>;
}
```

---

## 3. Rule Engine: Ordered and Testable

Evaluates entities and SKUs against regulatory items in defined order with transparent reasoning.

### 3.1 Rule Ordering (Priority 1–9)

1. **Grandfathering** (safe harbor): Exempt pending applications before Oct 13, 2026
2. **Installed base**: Exempt equipment authorized before rule effective date
3. **Entity definition**: Determine which entities are "restricted" based on scenario mode
4. **Covered List component rule** (FCC 26-50): Logic-bearing + Covered List entity = blocked
5. **1260H designation only**: Does NOT block unless paired with procurement scope
6. **Federal procurement** (S.5548): Applies only to Federal NSS; check scope
7. **Transceiver model ban** (Reuters): Phase-in year logic
8. **BOM exemption** (Reuters): Apply US-sourced % threshold
9. **Transitive taint**: Display ownership chain for transparency

### 3.2 Key Rule: Entity Definition Switch

```
FUNCTION testEntityDefinitionMode(mode: EntityDefinitionMode, entity: SayariEntityRecord, threshold: number = 50): boolean {
  
  if (mode === INCORPORATION) {
    return entity.country === "CN";  // Incorporated in China
  }
  
  if (mode === MANUFACTURING) {
    return entity.manufacturingLocations?.includes("CN") ?? false;  // Built in China
  }
  
  if (mode === OWNERSHIP) {
    const isGovernmentAffiliated = ["SASAC", "MIIT", "State Grid"].includes(entity.ultimateBeneficialOwner || "");
    const ownershipAboveThreshold = (entity.ownershipPct ?? 100) >= threshold;
    return isGovernmentAffiliated || ownershipAboveThreshold;
  }
  
  return false;
}
```

**Example: Thai-built Innolight module**
- INCORPORATION mode: TRUE (Innolight incorporated in China)
- MANUFACTURING mode: FALSE (built in Thailand, not China)
- OWNERSHIP mode: TRUE (Innolight owned by SASAC)
- **Result:** Mode choice determines compliance status (illustrates OPEN QUESTION from SOURCES.md, item 4)

### 3.3 Covered List vs. 1260H Separation

- **Covered List (FCC 26-50):** Entity-based, triggers component prohibition
- **1260H (DoW):** Designation only; does NOT trigger prohibition unless paired with S.5548 or other procurement rule
- **Key fact from SOURCES.md:** InnoLight is on 1260H but NOT on Covered List (as of July 2026)

### 3.4 Generational Phase-In Logic

```
IF ruleType == TRANSCEIVER_MODEL_BAN:
  IF sku.speedGen >= regulatoryItem.phasedInGeneration:
    IF year(now()) >= scenario.assumptions.phaseInYear:
      status = BLOCKED
    ELSE:
      status = TRANSITION_WINDOW
      grandfatheredUntil = phaseInYear
```

**Default (Reuters draft):** 3.2T generation banned starting 2028, range 2027–2030. 800G and 1.6T grandfathered indefinitely.

### 3.5 US-BOM Exemption with Statuses

```
IF status == BLOCKED AND usSourcedBomPercentage >= usSourcedBomThreshold:
  status = EXEMPT_US_BOM
  assumptions.append("US-BOM % = " + usSourcedBomPercentage + "% >= threshold " + usSourcedBomThreshold + "%")
```

**Threshold:** Default 65%, editable 0–100%. Statuses: APPROVED | EXEMPT_US_BOM | TRANSITION_WINDOW | BLOCKED | UNKNOWN.

### 3.6 Probability and Confidence Handling

- **Never upgrade tier:** A REPORTED_DRAFT item cannot flip a SKU to APPROVED without explicit user intervention
- **Worst-case confidence:** `worstCaseConfidenceTier = weakestTier(triggeredBy[].tier)` — a SKU triggered by both ENACTED_LAW and REPORTED_DRAFT has worst-case REPORTED_DRAFT
- **Probability sliders:** Editable parameters (delay months, cost %) marked ILLUSTRATIVE; never affect logic

---

## 4. Import/Export Schemas

### 4.1 CSV Import Format

**entities.csv**
```
sayariEntityId,name,country,ultimateBeneficialOwner,ownershipPct,parentId,subsidiaryDepth,manufacturingLocations,hsCodes,retrievedAt
innolight,Zhongji Innolight Co. Ltd.,CN,SASAC,100,,0,"CN,TH","8517.6210,8517.6220",2026-11-16
cisco,Cisco Systems Inc.,US,,,0,"US,CN","8517.6210",2026-11-16
```

**products.csv**
```
skuId,productName,makerEntityId,speedGen,fccAuthorizationDate,isFccAuthPendingBefore20261013,usSourcedBomPercentage
cisco-sw-800g-v1,Catalyst 9000 (800G),cisco,GEN_800G,2026-05-01,false,75
cisco-sw-3200t-v1,Catalyst 9000 (3.2T),cisco,GEN_3200T,2026-11-15,false,45
```

**components.csv**
```
skuId,partName,supplierEntityId,countryOfOrigin,valueShare,usSourceced,logicBearing
cisco-sw-800g-v1,Optical Transceiver,innolight,CN,25,false,true
cisco-sw-800g-v1,Switch ASIC,cisco,US,30,true,true
```

**Validation:**
- Required columns must be present
- Enums (speedGen, country codes) must match valid values
- valueShare (components): sum per SKU should equal 100 (warn if not)
- Duplicate IDs: reject with explanation
- Orphaned parentId: warn but allow

### 4.2 JSON Export Format

```json
{
  "formatVersion": "1.0",
  "exportedAt": "2026-11-16T14:30:00Z",
  "sourcesVerifiedAt": "2026-11-16",
  "scenario": {
    "id": "scenario_fed_enacted_2026",
    "name": "Federal NSS - Enacted Only",
    "entities": [...],
    "products": [...],
    "regulatoryItems": [...],
    "activeConfidenceTiers": ["ENACTED_LAW", "ENACTED_DESIGNATION"],
    "assumptions": {
      "entityDefinitionMode": "INCORPORATION",
      "procurementScope": "FEDERAL_NSS"
    },
    "assessments": [
      {
        "skuId": "cisco-sw-3200t-v1",
        "complianceStatus": "BLOCKED",
        "triggeredBy": ["FCC_26_50"],
        "redesignAlerts": ["Logic-bearing optical transceiver from InnoLight (Covered List entity)"],
        "assumptions": ["InnoLight not on Covered List as of 2026-11-16 (live status UNVERIFIED)"],
        "falsifiers": ["If InnoLight is added to FCC Covered List, this product requires new authorization"]
      }
    ],
    "summaryMetrics": { "totalSkus": 10, "approvedSkus": 7, "blockedSkus": 2 }
  }
}
```

---

## 5. UI Layout, Controls, and Accessibility

### 5.1 Main Sections

1. **Filters & Assumptions** (collapsible)
   - Confidence tier checkboxes (ENACTED_LAW, ENACTED_DESIGNATION, PENDING_BILL, etc.)
   - Entity definition mode: [INCORPORATION ▼ | MANUFACTURING ▼ | OWNERSHIP ▼]
   - Procurement scope: [FEDERAL_NSS ▼ | COMMERCIAL ▼ | BOTH ▼]
   - Ownership threshold: [50 ▼] % (for OWNERSHIP mode)
   - Transition years: [5 ▼] (for bills)
   - 3.2T phase-in year: [2028 ▼] (range 2027–2030)
   - US-BOM threshold: [65 ▼] % (range 0–100)
   - [Update] [Reset to Defaults]

2. **Supply Chain Data** (tabs: Entities | Products | Components)
   - Import CSV/JSON buttons
   - Add entity/product forms
   - Sortable tables showing: name, country, UBO, lists, compliance

3. **Regulatory Items** (expandable list)
   - Checkbox to show/hide each rule
   - Tier badge + verification status badge
   - Rule summary and scope
   - "View Details" (sourceUrl, dateEffective, etc.)
   - "View Source" (link to SOURCES.md or primary URL)

4. **Results: Compliance & Impact**
   - Summary metrics: total SKUs, approved, blocked, transition window
   - Worst-case confidence tier
   - Sortable table per SKU:
     - SKU name | Compliance status | Delay | Cost Δ | Triggered by | Details

### 5.2 Color Scheme

| Tier | Color | Hex | Meaning |
|---|---|---|---|
| ENACTED_LAW | Green | #2d7f2d | Law in effect |
| ENACTED_DESIGNATION | Blue | #3366cc | Designated entity |
| PENDING_BILL | Yellow | #ffcc00 | Proposed legislation |
| REPORTED_DRAFT | Orange | #ff9900 | Reported but unconfirmed |
| ANALYST_ESTIMATE | Orange | #ff9900 | Adjustable input |
| HYPOTHETICAL | Red | #cc0000 | User-defined scenario |

**Accessibility:** Always use color + text labels. Ensure WCAG 2.1 AA contrast (≥4.5:1).

### 5.3 Keyboard Navigation

- **Tab**: Move between form fields and buttons
- **Enter**: Submit or toggle expand/collapse
- **Escape**: Close modal or cancel edit
- **Ctrl+S**: Save scenario
- **Ctrl+I**: Import data
- **Ctrl+E**: Export results
- **Alt+?**: Show keyboard shortcut help

---

## 6. Test Cases

All tests are automated unit tests (Jest or similar) in a single test file.

### 6.1 Test: Entity Definition Mode (3 modes × 1 entity)

```typescript
test("Thai-built Innolight: compliance under each entity definition", () => {
  const innolight = {
    country: "CN",                      // Incorporated in China
    manufacturingLocations: ["TH"],     // Built in Thailand
    ultimateBeneficialOwner: "SASAC",
    ownershipPct: 100
  };
  
  expect(testEntityDefinitionMode(INCORPORATION, innolight, 50)).toBe(true);  // CN
  expect(testEntityDefinitionMode(MANUFACTURING, innolight, 50)).toBe(false); // TH
  expect(testEntityDefinitionMode(OWNERSHIP, innolight, 50)).toBe(true);      // SASAC
});
```

### 6.2 Test: Installed Base Grandfathering

```typescript
test("Pre-Oct-13 auth grandfathers equipment despite Covered List component", () => {
  const sku = {
    fccAuthorizationDate: "2026-05-01",  // Before Oct 13
    bomComponents: [{ supplierEntityId: "innolight", logicBearing: true }]
  };
  
  const scenario = {
    regulatoryItems: [{ id: "FCC_26_50", dateEffective: "2026-10-13" }],
    entities: [{ sayariEntityId: "innolight", lists: { coveredList: true } }]
  };
  
  const assessment = evaluateSku(sku, scenario);
  expect(assessment.complianceStatus).toBe("TRANSITION_WINDOW");
  expect(assessment.grandfatheredUntil).toBe("indefinite (pre-Oct-13 auth)");
});
```

### 6.3 Test: Pending Application Safe Harbor

```typescript
test("Pending application before Oct-13 (no logic-bearing amendment) is exempt", () => {
  const sku = {
    isFccAuthPendingBefore20261013: true,
    bomComponents: [{ supplierEntityId: "innolight", logicBearing: true }]
  };
  
  const scenario = {
    regulatoryItems: [{ id: "FCC_26_50", ruleType: "PENDING_APP_SAFE_HARBOR" }],
    entities: [{ sayariEntityId: "innolight", lists: { coveredList: true } }]
  };
  
  const assessment = evaluateSku(sku, scenario);
  expect(assessment.complianceStatus).toBe("TRANSITION_WINDOW");
});
```

### 6.4 Test: 1260H-Only vs. Covered List Entity

```typescript
test("1260H designation alone does NOT block; Covered List entity DOES", () => {
  const scenario_1260h_only = {
    entities: [{ sayariEntityId: "innolight", lists: { list1260H: true, coveredList: false } }],
    regulatoryItems: [{ id: "FCC_26_50", ruleType: "COVERED_LIST_COMPONENT" }]
  };
  
  const scenario_covered_list = {
    entities: [{ sayariEntityId: "innolight", lists: { coveredList: true } }],
    regulatoryItems: [{ id: "FCC_26_50", ruleType: "COVERED_LIST_COMPONENT" }]
  };
  
  const sku = { bomComponents: [{ supplierEntityId: "innolight", logicBearing: true }] };
  
  expect(evaluateSku(sku, scenario_1260h_only).complianceStatus).toBe("APPROVED");
  expect(evaluateSku(sku, scenario_covered_list).complianceStatus).toBe("BLOCKED");
});
```

### 6.5 Test: US-BOM Exemption at 64%, 65%, 66%

```typescript
test("US-BOM exemption applies at or above threshold", () => {
  const sku = {
    bomComponents: [
      { valueShare: 25, usSourceced: true },   // US DSP
      { valueShare: 20, usSourceced: true },   // US PHY
      { valueShare: 25, usSourceced: false },  // China optics
      { valueShare: 30, usSourceced: false }   // Passive
    ]
  };
  
  // usSourcedBomPercentage = 45%
  const scenario_65 = { assumptions: { usSourcedBomThreshold: 65 } };
  expect(evaluateUsBomExemption(sku, 65, scenario_65)).toBe(false);  // 45% < 65%
  
  // Re-source passive to US: 75%
  sku.bomComponents[3].usSourceced = true;
  expect(evaluateUsBomExemption(sku, 65, scenario_65)).toBe(true);   // 75% >= 65%
});
```

### 6.6 Test: Federal vs. Commercial Procurement Scope

```typescript
test("S.5548 applies to Federal NSS but not Commercial", () => {
  const sku = { bomComponents: [{ supplierEntityId: "innolight", logicBearing: true }] };
  
  const scenario_federal = {
    assumptions: { procurementScope: "FEDERAL_NSS" },
    regulatoryItems: [{ id: "S5548", ruleType: "FEDERAL_PROCUREMENT" }],
    entities: [{ sayariEntityId: "innolight", lists: { s5548Named: true } }]
  };
  
  const scenario_commercial = { ...scenario_federal, assumptions: { procurementScope: "COMMERCIAL" } };
  
  const result_fed = evaluateSku(sku, scenario_federal);
  const result_com = evaluateSku(sku, scenario_commercial);
  
  expect(result_fed.complianceStatus).toBe("TRANSITION_WINDOW");
  expect(result_fed.triggeredBy).toContain("S5548");
  
  expect(result_com.complianceStatus).toBe("APPROVED");
  expect(result_com.triggeredBy).toEqual([]);
});
```

### 6.7 Test: Subsidiary Taint at Depth 3

```typescript
test("Transitive ownership taint through 3-level subsidiary chain", () => {
  const entities = [
    { sayariEntityId: "sasac", country: "CN", ownershipPct: 100, subsidiaryDepth: 0 },
    { sayariEntityId: "state_entity", country: "CN", ownershipPct: 100, parentId: "sasac", subsidiaryDepth: 1 },
    { sayariEntityId: "innolight_sub", country: "CN", ownershipPct: 100, parentId: "state_entity", subsidiaryDepth: 2 },
    { sayariEntityId: "innolight_mfg", country: "TH", ownershipPct: 100, parentId: "innolight_sub", subsidiaryDepth: 3 }
  };
  
  const result = computeOwnershipTaint("innolight_mfg", { entities }, { entityDefinitionMode: "OWNERSHIP" });
  
  expect(result.isTainted).toBe(true);
  expect(result.path).toEqual([
    "innolight_mfg (depth 3) → innolight_sub (depth 2, 100% owned)",
    "→ state_entity (depth 1, 100% owned)",
    "→ sasac (depth 0, 100% owned, government-affiliated)"
  ]);
});
```

---

## 7. Acceptance Criteria

### 7.1 Functional Requirements

| Requirement | Test Case | Status |
|---|---|---|
| CSV import (entities, products, components) without errors | Auto-detect delimiter; validate enums and data types | Must pass |
| JSON export with scenario metadata and audit trail | Scenario round-trips (export/re-import) | Must pass |
| Entity definition mode switch | Results change when mode changes (INCORPORATION vs. MANUFACTURING vs. OWNERSHIP) | Must pass |
| Covered List component rule | FCC 26-50 blocks logic-bearing components from Covered List entities ONLY | Must pass |
| 1260H separation | 1260H designation alone does NOT block unless paired with S.5548 or procurement scope | Must pass |
| Grandfathering (pre-Oct-13 auth) | Equipment with pre-Oct-13 authorizations passes despite logic-bearing components | Must pass |
| Pending application safe harbor | Applications pending before Oct 13 (not amended) are exempt | Must pass |
| Transitive ownership taint | Ownership chain displayed with percentages; subsidiary-depth honored | Must pass |
| US-BOM exemption | At threshold (default 65%), components may be exempted; threshold editable 0–100% | Must pass |
| Procurement scope filter | S.5548 applies only if scope is FEDERAL_NSS or BOTH; not to COMMERCIAL | Must pass |
| Confidence tier filter | UI shows/hides rules; worst-case tier updated when tier is toggled | Must pass |
| Compliance status per SKU | One of: APPROVED, EXEMPT_US_BOM, TRANSITION_WINDOW, BLOCKED, UNKNOWN | Must pass |
| Cost/delay heuristics | Labeled ILLUSTRATIVE; costs/delays editable parameters; never affect logic | Must pass |
| Scenario persistence | LocalStorage saves/loads scenarios; up to 10 scenarios | Must pass |
| Keyboard navigation | All controls accessible via Tab, Enter, Escape, Ctrl+S/I/E, Alt+? | Must pass |
| WCAG 2.1 AA compliance | Color + text labels; contrast ≥4.5:1; alt text on UI elements | Must pass |

### 7.2 Non-Functional Requirements

| Requirement | Test | Status |
|---|---|---|
| Single file | App is one `.html` file (or index.html + data files); no backend required | Must pass |
| Performance | Evaluation of 100 SKUs and 10 regulatory items completes in <2 seconds | Must pass |
| Data privacy | All data stored in LocalStorage; no external API calls (MVP) | Must pass |
| Last-verified transparency | SOURCES.md date (2026-11-16) prominently displayed; link to source | Must pass |
| Offline operation | App fully functional without internet after initial load | Must pass |
| Mobile responsive | UI usable on screens ≥375px wide (iPhone SE) | Must pass |
| Auditability | All assumptions logged in export; falsifiers documented per SKU | Must pass |

### 7.3 Business Rules

| Rule | Test | Status |
|---|---|---|
| Confidence tier ordering | Weakest tier among triggeredBy items becomes worstCaseConfidenceTier | Must pass |
| Status never strengthened | REPORTED_DRAFT item cannot flip status to APPROVED without user confirmation | Must pass |
| Assumption transparency | Each assumption has a corresponding falsifier; falsifiers drive monitoring | Must pass |
| No competitor claims | App never references or benchmarks against other tools (e.g., Sibilla) | Must pass |
| Analyst estimates labeled | ANALYST_ESTIMATE items noted as "adjustable inputs"; methodology gaps documented | Must pass |

---

## 8. Implementation Notes

- **Build tool:** TypeScript → ES2020 JavaScript; CSS minification
- **Testing:** Jest for unit tests; Puppeteer or Playwright for E2E UI tests
- **State management:** Plain TypeScript objects + LocalStorage (no Redux/Vuex needed for MVP)
- **Styling:** CSS Grid for layout; CSS variables for colors and theme
- **Accessibility:** Semantic HTML; ARIA labels; focus management; keyboard event handlers
- **Documentation:** README.md (how to use); SOURCES.md (regulatory facts); CONCEPT-IDEA.md (framework); SPECIFICATIONS.md (this file)

---

## 9. Future Backend Integration Points

If a backend is added:

1. **Sayari API:** Fetch entity data, ownership, shipments
2. **FCC Covered List API:** Refresh monthly or on-demand
3. **DoW 1260H List API:** Refresh monthly or on-demand
4. **User accounts:** Store scenarios, share with team, role-based access
5. **Webhook notifications:** Alert users if regulatory lists change
6. **Audit logging:** Track all scenario changes, compliance assessments
7. **Procurement system integration:** Pull supply chain (Coupa, Ariba, SAP APIs)
8. **Regulatory intelligence feed:** Auto-update with new bills, FCC notices, DoW designations

Backend design would add:
- REST API endpoints for scenario CRUD
- Database schema for users, scenarios, audit logs
- Authentication/authorization (OAuth2 or SSO)
- Rate limiting and caching for external API calls

---

## Conclusion

This specification defines a single-file static MVP with a testable rule engine, transparent confidence tiers, and full scenario persistence. The design prioritizes intellectual honesty: assumptions are logged, falsifiers are documented, and confidence tiers travel with every claim. The ordered rule engine ensures consistent, predictable behavior. Test cases cover the most complex decision points: entity definition mode, grandfathering, 1260H vs. Covered List separation, and US-BOM exemptions.

Future backend integration can add live data sources and team collaboration without changing the core rule engine or data model.

**Last updated:** 2026-11-16  
**Sources:** SOURCES.md, CONCEPT-IDEA.md  
**Status:** Complete specification; ready for implementation
