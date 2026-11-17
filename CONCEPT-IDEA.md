# Anticipatory Regulatory Simulation: Concept and Learning Goals

This document outlines a simulation framework for analyzing supply-chain and infrastructure impacts of potential and enacted regulations on technology procurement, with emphasis on how to reason about *unconfirmed* intelligence alongside confirmed law.

---

## 1. Product Vision

The simulator allows students and procurement analysts to:

1. **Model multiple confidence levels simultaneously.** Import a supply chain (entities, components, shipment routes, ownership structures) and run impact analysis against enacted law, pending bills, reported-but-unconfirmed regulatory drafts, and hypothetical scenarios in parallel.

2. **Distinguish confidence tiers visually.** Display ENACTED_LAW restrictions in solid green; ENACTED_DESIGNATION flags in blue; PENDING_BILL scenarios in yellow; REPORTED_DRAFT and ANALYST_ESTIMATE in orange; HYPOTHETICAL in red. Users see at a glance which restrictions are law today versus speculation about tomorrow.

3. **Integrate Sayari entity data.** For each named company in the supply chain, the simulator pulls ownership, UBO (ultimate beneficial owner), affiliate and subsidiary networks, shipment records, and HS code histories. It then applies regulatory definitions (e.g., "produced by," "subsidiary," "affiliate") to predict which entities fall under which rules.

4. **Surface assumption fragility.** When a supply chain passes regulation A but fails under unconfirmed scenario B, the simulator flags the scenario's confidence tier and the specific unverified facts it depends on (e.g., "REPORTED_DRAFT assumes 65% US BOM exemption, status UNVERIFIED"). Users learn to distinguish robust designs from those that break if one rumor becomes law.

5. **Avoid competitor claims.** The tool does not benchmark or speculate about competing products or services. It is purely a regulatory-scenario modeling engine for the analyst's own supply chain.

---

## 2. Evidence-Tier Framework

Regulations are classified into six confidence tiers, each with baseline items and citations from SOURCES.md. The framework separates FCC Covered List restrictions (ENACTED_LAW A) from Department of War designations (ENACTED_DESIGNATION B).

### A. ENACTED_LAW: FCC 26-50 Equipment Authorization Rule

**Reference:** SOURCES.md, section A. Status: VERIFIED as of July 22, 2026 (adopted), effective October 13, 2026.

**Rule:**
- **Scope:** Bars new equipment authorizations for devices with a "logic-bearing hardware component" produced by an entity on the FCC Covered List.
- **Definition (47 CFR 2.902):** Logic-bearing hardware = any device, system, module, sub-assembly, or integrated circuit generating and using timing signals at >9,000 pulses/second using digital techniques, or generating and using RF energy for data processing functions. Includes intentional radiators; excludes purely mechanical or structural parts, passive components without digital timing, power-only parts, bare motors.
- **Prospective only:** applies to new equipment authorizations filed on or after October 13, 2026. Previously authorized equipment may continue to be marketed, imported, and used under existing authorizations. Pending applications as of October 13 are exempt unless later amended to add, substitute, or change a logic-bearing component.
- **Covered List entity condition:** The prohibition applies only to logic-bearing components produced by entities on the FCC Covered List. Does NOT apply to entities listed only for production-location-based categories (routers, UAS, UAS critical components) unless that entity is separately identified on the Covered List.
- **Hardware only (adopted):** The Final Rule covers hardware components only. Software and firmware from Covered List entities are NOT yet prohibited under ENACTED_LAW. The FNPRM (pending, see section below) proposes extending this to software and firmware.
- **Subsidiary and affiliate treatment:** The FCC order does not define the ownership or control threshold for determining whether a subsidiary or affiliate is "produced by" a Covered List entity. This remains open.
- **Example:** FCC 26-50 ¶22 cites Hudson Institute commenters naming "optical transceivers" as a logic-bearing component example, but the rule does not name specific component types. Each transceiver must be analyzed against the definition.

**How it affects procurement:**
- Any new optical transceiver, switch, router, or server containing a Covered List–produced logic-bearing component requires a new FCC authorization (or modification of an existing one with full recertification). An equipment maker cannot use a component from a Covered List entity without seeking new authorization or modifying an existing one.
- Pre-October 13, 2026 authorizations are "grandfathered"; new orders under old SKUs may continue if the authorization is not amended.
- If a component is produced by a Covered List entity but only meets the threshold (>9,000 pulses/second or RF processing), the prohibition applies. If it does not meet the definition, it may be used freely.

**Covered List as of July 22, 2026 (SOURCES.md, section C):**
- As of March 23, 2026 (DA 26-278, Appendix A): Huawei, ZTE, Hytera, Hikvision, Dahua, Kaspersky, China Mobile International USA, China Telecom (Americas), Pacific Networks/ComNet, China Unicom (Americas), foreign-produced UAS, UAS critical components, and foreign-produced routers.
- **Four companies of interest NOT on the list (as of July 2026):** InnoLight, Eoptolink, Dongshan Precision, TFC.
- **Live Covered List status (November 2026):** UNVERIFIED. The page at https://www.fcc.gov/supplychain/coveredlist could not be accessed; whether these or other companies have been added is unknown.

---

### B. ENACTED_DESIGNATION: Department of War Section 1260H List (June 2026)

**Reference:** SOURCES.md, section B. Status: VERIFIED as of June 10, 2026 (publication), filed June 8, 2026.

**Rule:**
- **Statutory basis:** Section 1260H of the William M. (Mac) Thornberry National Defense Authorization Act for Fiscal Year 2021 (Pub. L. 116-283).
- **Scope:** The DoW identifies entities as "Chinese military companies." This is a separate designation from the FCC Covered List and is not automatically a prohibition on procurement or component use. Rather, it is a designation of entity status under Section 1260H.
- **Section 805 contracting restriction:** SOURCES.md notes that Section 805 of the FY2024 NDAA allegedly bars DoW from contracting directly with 1260H entities from June 30, 2026, but this statutory reference and date have not been verified against the FY2024 NDAA text. Status: UNVERIFIED.

**Companies of interest (June 10, 2026 notice):**
- **Zhongji Innolight Co., Ltd.:** ✓ Listed. Description: "InnoLight is indirectly owned by SASAC and directly affiliated with MIIT."
- **Eoptolink Technology Inc., Ltd.:** ✗ NOT listed.
- **Dongshan Precision Manufacturing Co., Ltd.:** ✗ NOT listed.
- **TFC:** ✗ NOT listed.

**Key distinction from FCC Covered List:** A Section 1260H designation does NOT automatically place a company on the FCC Covered List. InnoLight is on the 1260H list but is NOT on the FCC Covered List (as of July 2026). This matters: the FCC 26-50 component prohibition applies only to Covered List entities. InnoLight's 1260H status does not trigger the FCC 26-50 rule.

---

### C. ENACTED_LAW (reference): Foreign-Produced Routers (DA 26-278, March 2026)

**Reference:** SOURCES.md, section D. Status: VERIFIED.

**Rule:**
- Routers produced in foreign countries were added to the FCC Covered List on March 23, 2026, with exemption for routers receiving a Conditional Approval from DoW or DHS.
- Conditional Approvals are valid for up to 18 months (Annex A).

**How it affects procurement:**
- Any new foreign-produced router requires either (1) an existing FCC authorization that pre-dates October 13, 2026, or (2) a DoW/DHS Conditional Approval. Without one of these, new authorizations are barred.
- Router Conditional Approval expiry dates (2027–2028): UNVERIFIED. The notice does not list router approvals or expiry dates.

---

### D. PENDING_BILL: S.5548 Optical Transceiver Procurement Restriction

**Reference:** SOURCES.md, section E. Status: VERIFIED as introduced September 24, 2026; post-introduction status UNVERIFIED.

**Bill:**
- **Official title:** "To prohibit Federal procurement of certain optical transceivers, and for other purposes."
- **Short title:** "Securing National Security Systems from Chinese Optical Transceivers Act of 2026."
- **Introduced:** September 24, 2026; referred to Senate Committee on Homeland Security and Governmental Affairs.
- **Sponsors:** Sen. J.D. McCormick (R, Ohio), Sen. Mark Kelly or Gallego (cosponsor name in SOURCES.md indicates possible discrepancy), Sen. John Cornyn (R, Texas), Sen. John Fetterman (D, Pennsylvania).

**Mechanism:**
- Amends Section 5949 of the James M. Inhofe National Defense Authorization Act for Fiscal Year 2023 (Pub. L. 117-263) to extend a semiconductor procurement ban to covered optical transceivers.
- **Federal procurement restriction only:** applies only to U.S. federal procurement for national security systems; does NOT limit commercial or civilian procurement.
- **Transition period:** five years after enactment.
- **Scope:** Transceivers with firmware, software, or components from named or later-designated entities are covered.

**Named companies:**
- Zhongji Innolight Co., Ltd. (InnoLight)
- Eoptolink Technology Inc., Ltd. (Eoptolink)
- Includes subsidiaries, affiliates, and successors.
- Additional entities may be added if the Defense or Commerce Secretary determines ties to a country of concern and publishes in the Federal Register.

**How it affects procurement (if enacted):**
- Federal procurement (DoD, intel agencies, national labs, etc.) would be barred from buying optical transceivers from InnoLight, Eoptolink, their subsidiaries/affiliates, or any later-designated entity for systems classified as "critical systems."
- Five-year transition means the ban would take effect five years after enactment, not immediately.
- Commercial and international datacenter operators are not affected by this bill; it is federal-only.

**Post-introduction status:** UNVERIFIED. Whether the bill has passed committee, been amended, passed the Senate, or been enacted is not known.

---

### E. REPORTED_DRAFT: Reuters FCC Draft Transceiver Model Ban (August 2026)

**Reference:** SOURCES.md, section F. Status: UNVERIFIED. Source URL not located. Reuters.com is blocked from automated access.

**Reported claims (unconfirmed):**
- FCC is drafting restrictions on new Chinese optical transceiver models (not components, but whole products).
- Phase-in starting at the 3.2T generation, ramping ~2028–2029.
- Possible exemption if ≥65% of bill-of-materials (BOM) value is U.S.-sourced.
- InnoLight, Eoptolink, Dongshan Precision, and TFC are explicitly mentioned.
- Microsoft allegedly lobbying against the ban (November 2026 report, also UNVERIFIED).

**Why it is unverified:**
- No FCC public document (order, notice, FNPRM, or bureau announcement) confirms a draft transceiver-model ban. FCC 26-50 does not propose one; the FNPRM proposes extending the component rule to software/firmware, not introducing a model ban.
- Reuters source URL not found.
- No primary source confirms the claims.

**How it affects modeling:**
- If this draft becomes law, the impact would be much broader than FCC 26-50: instead of barring new authorizations for logic-bearing components, it would bar new models of entire transceiver products from named companies, with a potential US-BOM workaround.
- Students should model this scenario but flag it as REPORTED_DRAFT: "If true, XYZ; if false, ABC."
- The 65% US-BOM threshold is UNVERIFIED and may differ from the 65% thresholds in other contexts.

---

### F. ANALYST_ESTIMATE: Market Share and Production Data

**Reference:** SOURCES.md, section G. Status: UNVERIFIED. Sources require subscription access.

**Counterpoint Research (unverified):**
- InnoLight: ~27% of datacom transceiver market revenue (global).
- InnoLight and Eoptolink combined: ~60% of NVIDIA 800G optical transceiver volumes.
- DSPs sourced from Broadcom/Marvell.
- Thailand manufacturing lines may fall outside point-of-manufacture rules.

**Cignal AI (unverified):**
- InnoLight: $2.6B of $7.7B in global optical transceiver revenue (1Q26), approximately 34%.

**Methodology note:** Counterpoint (27%) and Cignal (34%) estimates diverge significantly. The difference may reflect definitional choices (datacom only vs. all transceivers, revenue vs. units, different time periods). These are adjustable inputs.

**How it affects modeling:**
- If InnoLight controls 27–34% of the global transceiver market, adding restrictions on new InnoLight products would create supply constraints and price pressure.
- If InnoLight and Eoptolink together serve ~60% of NVIDIA 800G volumes, restricting both would affect high-end datacenter deployments significantly.
- Students should note the divergence and model both scenarios (27% vs. 34%) to test sensitivity.

---

## 3. Student/Analyst Workflow

### Step-by-Step Process

**Step 1: Choose a target entity in Sayari**
- Student selects a company (e.g., Innolight, Eoptolink, or a Western equipment maker).
- Sayari provides: legal name, incorporation jurisdiction, D&B number, and links to upstream and downstream entities.

**Step 2: Map ownership and UBO**
- Student traces the ownership chain: who owns this company? Is there a parent company? A foreign government entity or state-owned asset? Affiliations with other entities (SASAC, MIIT, state enterprises)?
- Sayari provides: shareholder names, ownership percentages, and flags for entities with public records indicating government affiliation.
- Example: "Zhongji Innolight Co., Ltd. — indirectly owned by SASAC, directly affiliated with MIIT." (From SOURCES.md, item B.)
- Student records: "If ownership % of SASAC is >50%, then likely subject to Section 1260H designation."

**Step 3: Identify subsidiaries and affiliates**
- Student reviews Sayari's subsidiary and affiliate network. For each subsidiary, note:
  - Legal name, incorporation jurisdiction.
  - Ownership percentage of parent.
  - Whether the subsidiary is separately listed on DoW 1260H, FCC Covered List, or Commerce Entity List.
  - Manufacturing locations (this matters for production-location-based Covered List entries like routers).

**Step 4: Shipment records and HS codes**
- Student queries Sayari for shipment records to/from the target entity:
  - HS codes for optical transceivers, switches, routers, server modules.
  - Destination countries and customers.
  - Any pattern indicating which HS codes map to "logic-bearing hardware components."
- Student records: "InnoLight ships [X] units of product code Y to customer Z in the U.S."

**Step 5: OEM-to-supplier links**
- Student traces: which Western OEMs use this supplier? (E.g., Cisco uses Innolight optical transceiver modules.)
- For each OEM link, student notes:
  - Current product SKU and FCC authorization status.
  - Whether the authorization was filed before October 13, 2026 (grandfathered).
  - Transition timeline if the supplier is added to the Covered List.

**Step 6: Export or paste into the simulator**
- Student exports the supply chain graph from Sayari (entities, ownership, subsidiaries, shipment records) or manually pastes company names and HS codes into the simulator.
- Simulator flags: which entities are on which regulatory lists?

**Step 7: Run "Enacted Only" vs. "Enacted + Proposed" scenarios**
- **Enacted Only:** Simulator runs impact analysis against ENACTED_LAW items only.
  - Output: "Your supply chain is compliant with all enacted regulations as of [date]" or "Blocking issues: [list]."
- **Enacted + Proposed (Scenario A: S.5548 becomes law):** Simulator applies PENDING_BILL assumptions.
  - Output: "If S.5548 passes unchanged, your federal procurement [description] would be blocked starting [date]."
- **Enacted + Proposed (Scenario B: Reuters draft becomes law):** Simulator applies REPORTED_DRAFT assumptions.
  - Output: "If the reported FCC transceiver-model ban is confirmed and passes, your supply chain would have [impacts]. This scenario has confidence REPORTED_DRAFT: unverified."

**Step 8: Record assumptions and falsifiers**
- Student documents:
  - **Assumptions:** "We assume InnoLight is not added to the FCC Covered List within [timeline]."
  - **Falsifiers:** "If InnoLight is added to the Covered List, our optical transceiver supply chain becomes non-compliant. This would be known by [check FCC list date]."
  - **Scenario dependencies:** "The benefit of the 65% US-BOM exemption depends on the Reuters draft becoming law. Confidence: REPORTED_DRAFT (UNVERIFIED)."

---

### Five Example Learning Questions

**1. Question:** "InnoLight is on the DoW 1260H list (ENACTED_DESIGNATION) but not on the FCC Covered List (as of July 2026). Can we use an Innolight optical transceiver in a U.S. switch shipped to a DoD customer today?"
- **Answer:** Under ENACTED_LAW (FCC 26-50), yes, if the switch authorization predates October 13, 2026. Under PENDING_BILL (S.5548), no, if the bill is enacted—but S.5548 applies only to federal NSS procurement. The simulation should show all three outcomes with confidence tiers.
- **Learning:** Regulatory status (1260H) ≠ procurement restriction (Covered List or S.5548).

**2. Question:** "Our supplier says they can re-source a logic-bearing component from Thailand instead of China and avoid the FCC 26-50 rule. Is that correct?"
- **Answer:** No, if the component is "produced by" a Covered List entity. The FCC rule applies to the entity that produced the component, not the production location. If produced by a different entity located in Thailand, the rule does not apply unless that entity is on the Covered List.
- **Learning:** "Produced by" is an entity-based criterion, not location-based (except for specific production-location-based Covered List entries like foreign-produced routers).

**3. Question:** "Analyst estimate A says InnoLight has 27% market share; estimate B says 34%. Which should we use?"
- **Answer:** Both. Run sensitivity analysis. If 27% loss creates X disruption, and 34% loss creates Y disruption, the simulator should show both.
- **Learning:** ANALYST_ESTIMATE items are inputs, not facts. When estimates diverge, model both and flag the assumption.

**4. Question:** "S.5548 says the transition is five years from enactment. If it passes today, when does it take effect?"
- **Answer:** Five years from [enactment date]. If enacted in 2027, the ban takes effect in 2032. Interim steps may be sooner.
- **Learning:** PENDING_BILL items are not yet law. The final bill may differ from the introduced text.

**5. Question:** "If the Reuters draft (REPORTED_DRAFT) is confirmed and proposes a 65% US-BOM exemption, can we design around it?"
- **Answer:** Possibly, but the 65% threshold is UNVERIFIED. Scenario should flag: "Scenario: Reuters draft confirmed; assumption: 65% threshold applies; falsifier: if the final rule uses a different threshold."
- **Learning:** REPORTED_DRAFT and HYPOTHETICAL scenarios are planning tools, not predictions.

---

## 4. Datacenter Deployment and Design Impact Matrix

Summary of how each regulatory item triggers design decisions.

| **Regulatory Trigger** | **Evidence Tier** | **Status** | **Impact Area** | **Design Consideration** | **Assumptions / Falsifiers** |
|---|---|---|---|---|---|
| **FCC 26-50: Logic-bearing component rule** | ENACTED_LAW | VERIFIED Oct 13, 2026 | Optics component sourcing | New optics modules require new FCC authorization if from Covered List entity. Pre-Oct-13 authorizations grandfathered. | **ASSUMPTION:** Covered List does not expand to include InnoLight, Eoptolink before [date]. **FALSIFIER:** If InnoLight added to Covered List, existing SKUs cannot be amended without new auth. |
| **FCC Covered List (InnoLight, Eoptolink, Dongshan, TFC absent)** | ENACTED_LAW | PARTIAL (live status Nov 2026 UNVERIFIED) | Optics component sourcing | As of July 2026, these four companies are not on the Covered List, so their components do not trigger FCC 26-50 prohibition (yet). | **ASSUMPTION:** Covered List snapshot (July 2026) is representative of live list. **FALSIFIER:** If live list adds any of the four, impact assessment changes. Check FCC Covered List page monthly. |
| **Logic-bearing hardware definition (>9,000 pps or RF processing)** | ENACTED_LAW | VERIFIED | Optics component sourcing | Optical transceivers are cited as examples but not defined in the rule. Each transceiver must be analyzed: does it generate timing signals >9,000 pulses/sec using digital techniques? | **ASSUMPTION:** Pluggable optical transceiver (SFP, QSFP) with digital timing >9,000 pps = logic-bearing. **FALSIFIER:** If FCC issues guidance narrowing the definition or exempting certain types. Monitor FCC guidance. |
| **Foreign-produced router (Covered List entry, March 2026)** | ENACTED_LAW | VERIFIED | Router procurement, approval status | Foreign-produced routers on the Covered List require a Conditional Approval from DoW/DHS (valid up to 18 months) or pre-Oct-13 FCC authorization. | **ASSUMPTION:** Conditional Approvals are available for routers in [category]. **FALSIFIER:** If DoW/DHS stops issuing new Conditional Approvals. Status UNVERIFIED. |
| **Router Conditional Approval expiry (2027–2028)** | ENACTED_LAW | UNVERIFIED | Router procurement, renewal | If router Conditional Approvals expire in 2027–2028, models depending on those approvals will become non-compliant unless renewed. | **ASSUMPTION:** Conditional Approvals can be renewed; renewal timeline is [Y] months. **FALSIFIER:** If DoW/DHS does not renew approvals. Status UNVERIFIED; check DA 26-278 approval list. |
| **S.5548: Optical transceiver federal procurement ban** | PENDING_BILL | VERIFIED as introduced Sept 24, 2026 | Optics component sourcing (federal/NSS only) | If S.5548 passes unchanged, federal procurement of optical transceivers from InnoLight, Eoptolink, or later-designated entities is barred for national security systems. Five-year transition. | **ASSUMPTION:** S.5548 passes unchanged; five-year transition begins on [enactment date]. **FALSIFIER:** If bill is not enacted, is amended, or is blocked. Check congress.gov. |
| **S.5548: "Critical system" procurement definition** | PENDING_BILL | VERIFIED as introduced | Optics component sourcing (federal scope) | S.5548 distinguishes "critical systems" from non-critical systems, with exemptions for the latter. The definition may differ from the introduced text. | **ASSUMPTION:** Our datacenter qualifies as a "critical system" under S.5548. **FALSIFIER:** If the definition is narrower or broader than expected. Check final bill language. |
| **S.5548: Five-year transition** | PENDING_BILL | VERIFIED as introduced | Optics component sourcing (federal scope); buy-ahead strategy | Procurement ban takes effect five years after enactment. Interim steps may be sooner. | **ASSUMPTION:** Five years is sufficient lead time to qualify alternative suppliers. **FALSIFIER:** If interim steps trigger earlier actions or if alternative suppliers have longer qualification timelines. |
| **Reuters draft: Transceiver model ban starting 3.2T** | REPORTED_DRAFT | UNVERIFIED | Optics component sourcing; buy-ahead strategy | If FCC drafts (and later enacts) a ban on new Chinese transceiver models starting at 3.2T generation with phase-in ~2028–2029. | **ASSUMPTION:** 3.2T is the threshold; phase-in ramps 2028–2029. **FALSIFIER:** If (a) no draft is confirmed, (b) threshold is different, or (c) phase-in timeline differs. Status UNVERIFIED; Reuters source URL not found. |
| **Reuters draft: 65% US-BOM exemption** | REPORTED_DRAFT | UNVERIFIED | Optics component sourcing; BOM re-sourcing | If the Reuters draft includes a 65% US-BOM exemption, a transceiver with ≥65% U.S.-sourced BOM value may be exempt from the model ban (if the draft becomes law). | **ASSUMPTION:** 65% threshold, calculated as BOM cost (not unit count). **FALSIFIER:** (a) No exemption in final rule, (b) threshold is different, or (c) threshold applies to critical components only. Status UNVERIFIED. |
| **Analyst estimate: InnoLight 27–34% global market share** | ANALYST_ESTIMATE | UNVERIFIED | Supply-chain resilience; supplier diversification | If InnoLight controls 27–34% of the global optical transceiver market, restricting InnoLight would immediately create supply constraints. Lead time for alternative supplier qualification: 6–18 months. | **ASSUMPTION:** Analyst estimates bracket the true figure; market share is stable. **FALSIFIER:** If true market share is <20% or >40%, or if supplier capacity changes rapidly. Check analyst reports quarterly. |
| **Analyst estimate: InnoLight + Eoptolink ~60% of NVIDIA 800G volumes** | ANALYST_ESTIMATE | UNVERIFIED | Supply-chain resilience; high-performance optics sourcing | If Innolight and Eoptolink together serve ~60% of NVIDIA 800G optical transceiver volumes, restricting both would significantly impact high-performance datacenters. | **ASSUMPTION:** Estimate applies to NVIDIA 800G pluggable transceivers; production can be diverted within [X] months. **FALSIFIER:** If estimate is overstated or Western suppliers lack capacity. Monitor NVIDIA supply-chain announcements. |
| **Analyst estimate: DSPs from Broadcom/Marvell** | ANALYST_ESTIMATE | UNVERIFIED | Optics component sourcing; design flexibility | If logic-bearing components in optical transceivers are sourced from Broadcom/Marvell (U.S.-based), those transceivers are not subject to FCC 26-50 component rule. This provides a re-sourcing path. | **ASSUMPTION:** Broadcom/Marvell DSPs are available, qualified, and not on any restricted list. **FALSIFIER:** If (a) Broadcom or Marvell DSPs are added to any restricted list, or (b) alternative designs require Innolight/Eoptolink components that cannot be substituted. |
| **Analyst estimate: Thailand manufacturing may fall outside point-of-manufacture rules** | ANALYST_ESTIMATE | UNVERIFIED | Optics component sourcing; manufacturing location | If a component is manufactured in Thailand but designed by a Covered List entity, the component may or may not be "produced by" the Covered List entity under FCC 26-50. | **ASSUMPTION:** "Produced by" = "manufactured by" (location-based). **FALSIFIER:** If FCC guidance clarifies that "produced by" = "entity of origin" (ownership-based), regardless of manufacturing location. Status: OPEN QUESTION (SOURCES.md, item 4). |
| **Optics-agnostic fabric design** | DESIGN STRATEGY | APPLICABLE | Optics component sourcing | Use pluggable transceiver form factors (SFP, QSFP, QSFP-DD, OSFP) instead of integrated optics. This allows swapping transceiver suppliers without redesigning the switch. | **ASSUMPTION:** Pluggable form factor meets performance requirements. Multi-sourced qualification is feasible within lead time. **FALSIFIER:** If (a) performance gap vs. integrated optics is unacceptable, or (b) pluggable market shrinks. Monitor ASIC designs. |
| **Multi-sourcing strategy for optical transceivers** | DESIGN STRATEGY | APPLICABLE | Optics component sourcing; supplier diversification | Qualify multiple transceiver suppliers (Innolight, Eoptolink, Marvell, Broadcom, Western competitors) on the same switch or server. Place orders with 2–3 suppliers to diversify risk. | **ASSUMPTION:** Qualified suppliers exist and can ramp production. **FALSIFIER:** If (a) alternative suppliers are capacity-constrained, (b) single-supplier designs (LPO/CPO) become dominant, or (c) supply-chain concentration increases. |
| **Buy-ahead: 800G and 1.6T inventory before "new model" cutoff** | DESIGN STRATEGY | APPLICABLE (if Reuters draft or later rule) | Optics component sourcing; inventory and cash flow | If a ban on "new Chinese transceiver models" is confirmed, buy ahead of the phase-in date (reported ~2028–2029). Pre-approve designs and place large purchase orders before the cutoff. | **ASSUMPTION:** Current models remain authorized even after the ban; buy-ahead is legal. Inventory carrying cost is acceptable. **FALSIFIER:** If (a) the rule is retroactive, (b) inventory carrying costs exceed savings, or (c) technology roadmap shifts to 3.2T+ faster. |
| **Pluggable vs. LPO/CPO (Coherent Optics) architecture choice** | DESIGN STRATEGY | APPLICABLE | Optics component sourcing; risk profile | Pluggable transceivers can be sourced from multiple suppliers and swapped. LPO/CPO (integrated optics) are designed with a single supplier and cannot be easily swapped. | **ASSUMPTION:** LPO/CPO designs offer better performance but higher supplier lock-in risk. **FALSIFIER:** If (a) LPO/CPO suppliers diversify or (b) pluggable performance gap becomes unacceptable. Monitor OEM strategies. |
| **Federal vs. commercial datacenter zoning** | DESIGN STRATEGY | APPLICABLE (if S.5548 or Reuters draft) | Infrastructure deployment; NSS vs. commercial segregation | Separate federal/NSS datacenters from commercial datacenters. Federal facilities must comply with FCC 26-50 and (if S.5548 passes) cannot use restricted transceivers. Commercial facilities can use any transceiver (unless a broader rule is enacted). | **ASSUMPTION:** Infrastructure segregation is operationally feasible. **FALSIFIER:** If (a) operational segregation creates inefficiencies or (b) regulations eventually apply to commercial procurement. |
| **Switch and server exposure to FCC 26-50 component rule** | DESIGN STRATEGY | APPLICABLE | Equipment design and authorization | Each switch and server model must have an active FCC authorization (or modification of existing) if any logic-bearing component is from a Covered List entity. Pre-Oct-13 authorizations are grandfathered. Qualification timeline: 2–4 months per model. | **ASSUMPTION:** FCC authorizations for new switch/server models are obtainable within [X] months. **FALSIFIER:** If (a) FCC shifts to stricter enforcement or (b) Covered List expands to include critical component suppliers. |
| **Pending application safe harbor (pre-Oct-13 filing)** | DESIGN STRATEGY | APPLICABLE | Equipment design; legacy support | FCC 26-50 exempts applications pending before Oct 13, 2026, unless amended to add, substitute, or change a logic-bearing component. Plan designs to minimize amendments. | **ASSUMPTION:** Pending applications as of Oct 13, 2026 are exempt. Amendments for firmware updates do not trigger re-authorization. **FALSIFIER:** If FCC issues guidance narrowing the safe harbor. Monitor FCC guidance. |
| **Supplier capacity and price effects (S.5548 or Reuters scenario)** | ASSUMPTION | APPLICABLE | Cost and schedule; supply-chain economics | If S.5548 or Reuters draft is enacted, federal procurement would shift to alternative suppliers. Suppliers (Marvell, Broadcom, Cisco, Acacia) may raise prices and stretch lead times. Build cost and schedule buffers. | **ASSUMPTION:** Alternative suppliers can ramp capacity within [X] months; price increase is [Y]% and schedule extension is [Z] months. **FALSIFIER:** If (a) alternative suppliers lack capacity or (b) price/schedule delta exceeds expectations. Obtain supplier commitments in writing before buy-ahead orders. |
| **Router and power inverter approval expiry risk** | DESIGN STRATEGY | APPLICABLE | Infrastructure procurement; regulatory compliance | Foreign-produced routers on the Covered List require Conditional Approval (valid up to 18 months). If Conditional Approvals expire and are not renewed, router models become non-compliant. Similarly, if a power inverter action is enacted (status UNVERIFIED), inverters may require approval/re-approval. | **ASSUMPTION:** Conditional Approvals can be identified and tracked; renewal process is predictable. **FALSIFIER:** If (a) Conditional Approval expiry dates are not published or (b) DoW/DHS does not renew approvals. Status: Router expiry dates UNVERIFIED; inverter action UNVERIFIED. |
| **Rack power density and thermal constraints (optics supplier change)** | DESIGN STRATEGY | APPLICABLE | Infrastructure design; physical deployment | If transceiver suppliers are changed (due to supply restrictions), power consumption and thermal profiles may differ. Plan power distribution and cooling upgrades. | **ASSUMPTION:** Alternative supplier transceivers fit within existing power and thermal budgets. **FALSIFIER:** If power/thermal gap is too large to bridge. Conduct thermal testing early. |
| **BOM cost modeling with tariff and supply-chain shifts** | DESIGN STRATEGY | APPLICABLE | Cost modeling; financial planning | Model optics BOM cost under multiple scenarios: (1) today's cost with Innolight/Eoptolink, (2) cost with Western suppliers (if restricted), (3) cost with tariffs or supply-chain diversification fees. Include lead-time extensions and inventory carrying costs. | **ASSUMPTION:** Cost deltas and lead-time extensions can be estimated from supplier quotes. **FALSIFIER:** If (a) cost or lead-time deltas are larger than expected or (b) tariffs or geopolitical factors shift unexpectedly. Update model quarterly with real supplier quotes. |
| **Deployment schedules and regulatory timeline alignment** | DESIGN STRATEGY | APPLICABLE | Project planning; regulatory compliance | Align datacenter deployment schedules with regulatory timelines. If S.5548 is enacted and has a five-year transition, plan major refresh cycles to align with regulatory milestones. | **ASSUMPTION:** S.5548 transition timeline (5 years from enactment) is known and stable. **FALSIFIER:** If (a) enactment is delayed or accelerated or (b) transition timeline is shortened or extended. Monitor congressional action. |

---

## 5. Teaching Points: How Definitions Shape Outcomes

### "Chinese" is not a simple binary

The regulations reviewed in SOURCES.md define "Chinese" in three different ways, and the choice of definition can determine whether a company is restricted:

1. **Incorporation jurisdiction (entity domicile):** Zhongji Innolight Co., Ltd. is incorporated in China. By this definition, it is "Chinese."

2. **Manufacturing location (production-location-based):** The FCC Covered List includes "routers produced in a foreign country," a production-location-based criterion. A router manufactured in Taiwan or Vietnam by a Western company is "foreign-produced" and is on the list, even if the company is incorporated in the U.S.

3. **Ownership or control (UBO / SASAC / MIIT affiliation):** Section 1260H designates entities "indirectly owned by SASAC and directly affiliated with MIIT," a UBO/control-based criterion.

**How this matters:**
- **InnoLight (incorporated China; SASAC-owned):** Subject to Section 1260H (ownership-based) and would be subject to FCC 26-50 if added to the Covered List (entity-based).
- **Foreign-produced router (manufactured abroad; possibly Western-owned):** Subject to the production-location-based Covered List entry even if the company and ownership are Western.
- **A subsidiary of InnoLight manufactured in Thailand:** Depending on the FCC's interpretation of "produced by," it may or may not be restricted. This is an OPEN QUESTION (SOURCES.md, item 4).

**Student practice:** For each of the four companies of interest (InnoLight, Eoptolink, Dongshan Precision, TFC), determine whether they would be classified as "Chinese" under each of the three definitions. Document which definitions apply and which regulatory outcomes result.

### Installed base is untouched

FCC 26-50 applies only to **new equipment authorizations filed on or after October 13, 2026**. Equipment with existing authorizations (filed before October 13) can continue to be marketed, imported, and used under those existing authorizations.

**What this means:**
- Cisco switches authorized before October 13 with Innolight transceivers can continue to be sold and deployed indefinitely.
- A new SKU of the same switch model, if it incorporates changes to a logic-bearing component and is filed after October 13, requires a new authorization.
- Firmware updates or minor security patches do NOT automatically trigger re-authorization (but this is an OPEN QUESTION; FCC guidance is needed).

**Supply-chain consequence:** The installed base of equipment with pre-October 13 authorizations is a stable, unaffected supply. Procurement teams can plan to build infrastructure using legacy SKUs (pre-Oct-13 authorizations) for 5–10 years, then migrate to new suppliers as models refresh. This creates a "golden window" for inventory and planning.

**Student practice:** Identify all switch, server, and router SKUs currently in production with Innolight or Eoptolink components. Determine which were authorized before October 13, 2026, and thus are grandfathered. Estimate how long these models can be procured without new authorization.

---

## 6. Risks of Over-Claiming: What the UI Prevents

The simulator is designed to prevent analysts from overstating confidence in uncertain outcomes.

**Key safeguards:**

1. **Evidence tier is mandatory and visible.** Every regulatory item is tagged ENACTED_LAW, ENACTED_DESIGNATION, PENDING_BILL, REPORTED_DRAFT, ANALYST_ESTIMATE, or HYPOTHETICAL. The UI displays the tier next to every assertion.

2. **Status field (VERIFIED, PARTIAL, UNVERIFIED) is mandatory.** Even within a tier, claims are marked VERIFIED, PARTIAL, or UNVERIFIED. For example, FCC 26-50 is ENACTED_LAW and VERIFIED; Reuters draft is REPORTED_DRAFT and UNVERIFIED.

3. **Date stamps and version history.** Every regulatory fact is stamped with a last-verified date (e.g., 2026-11-16). Disclaimer: "Live Covered List status (Nov 2026): UNVERIFIED."

4. **Assumptions and falsifiers are explicitly recorded.** For each scenario, the simulator asks: "What assumptions does this scenario depend on? What would falsify it?" An assumption like "Covered List does not expand to include InnoLight within 2 years" is logged. A falsifier like "If InnoLight is added to the Covered List in Q2 2027" is recorded as a monitoring task.

5. **Scenario branching prevents false summaries.** If the analyst models two scenarios (Enacted Only vs. Enacted + S.5548), the UI requires separate impact summaries for each. The analyst cannot claim a single outcome; they must show that outcomes diverge.

6. **Open questions are tracked.** The simulator flags questions from SOURCES.md that remain unanswered:
   - "Do pluggable optical transceivers require FCC equipment authorization today?"
   - "What ownership threshold does the FCC use for subsidiaries and affiliates?"
   - If the analyst's supply chain depends on the answer, the UI highlights: "Your supply chain assumes [answer]. If [other answer], impact changes."

7. **Link to primary sources.** Every regulatory fact links to its primary source. The analyst can click through to verify claims.

8. **Conflict flagging.** If the analyst includes facts from two sources that conflict (e.g., Counterpoint says 27%, Cignal says 34%), the UI highlights the conflict and forces a sensitivity analysis.

9. **Warnings on "new model" definitions.** SOURCES.md lists an OPEN QUESTION: "What constitutes a 'new model' under 47 CFR Part 2?" If the analyst's scenario depends on a specific definition, the UI warns: "FCC guidance on 'new model' is pending. Your assumption: [definition]. If FCC defines it differently, impact changes."

**Example over-claim prevented by the UI:**

- **Analyst claim (without safeguards):** "InnoLight transceivers are banned as of October 13, 2026."
- **UI intervention:** "Evidence tier? Status? Warning: SOURCES.md shows InnoLight is NOT on the FCC Covered List as of July 2026, and FCC 26-50 applies only to Covered List entities. If your claim is 'InnoLight transceivers are banned *if added to the Covered List*,' mark it HYPOTHETICAL and explain the condition."
- **Corrected claim:** "If InnoLight were added to the FCC Covered List (HYPOTHETICAL), transceivers produced by InnoLight would be barred from new FCC authorizations starting October 13, 2026 (FCC 26-50, ENACTED_LAW). Current status: InnoLight is not on the Covered List (SOURCES.md, section C, PARTIAL status as of July 2026; Nov 2026 status UNVERIFIED). Monitoring point: check FCC Covered List page monthly."

---

## Conclusion

This simulation framework teaches students and analysts to reason about regulatory risk with intellectual honesty. It surfaces the difference between law, proposed legislation, reported drafts, and analyst guesses. It forces assumptions into the light. It prevents false summaries and grounds claims in primary sources.

The key insight is that **uncertainty is not a barrier to analysis; it is the subject of analysis**. By modeling Enacted Only, Enacted + Pending Bills, and Hypothetical scenarios in parallel, with explicit assumptions and falsifiers, analysts can make robust procurement and design decisions even when the regulatory future is unclear.

---

**Last updated:** 2026-11-16  
**Sources:** SOURCES.md (all regulatory facts derived from primary sources cited therein)  
**Next steps:** Implement the simulator UI; conduct pilot analysis with student supply chains; monitor regulatory and market developments; update SOURCES.md quarterly.
