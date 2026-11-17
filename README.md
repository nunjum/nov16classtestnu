# Anticipatory Regulatory Scenario Simulator

## Table of Contents

1. [Overview](#overview)
2. [Quick Start](#quick-start)
3. [Evidence Tier Legend](#evidence-tier-legend)
4. [Student Guide: Sayari to Simulator](#student-guide-sayari-to-simulator)
5. [File Index](#file-index)
6. [Next Steps: Backend & Live Sayari Integration](#next-steps-backend--live-sayari-integration)
7. [Background & Motivation](#background--motivation)

---

## Overview

The **Proposed Regulation Simulation** is a web-based tool for modeling supply-chain compliance under enacted, pending, reported, and hypothetical regulations. It allows procurement teams and analysts to:

- **Model multiple confidence levels simultaneously:** View ENACTED_LAW (green), ENACTED_DESIGNATION (blue), PENDING_BILL (yellow), REPORTED_DRAFT (orange), and HYPOTHETICAL (red) regulations side by side.
- **Apply regulatory definitions:** Choose how "Chinese" or "restricted" is determined: incorporation, manufacturing location, or ownership threshold.
- **Test sensitivity to scenarios:** See which SKUs pass Enacted Only, fail under Pending Bills, and what assumptions break the model.
- **Import real Sayari data:** Add entities, ownership chains, and BOMs to the simulator; watch compliance status update live.
- **Save and compare scenarios:** Keep up to 3 saved scenarios and export results as JSON or CSV.

**Status:** Fully implemented and working. The demo is in `demo.html`. All baseline scenarios from SOURCES.md are preloaded.

---

## Quick Start

### Opening the Simulator

1. Download or clone this repository.
2. Open `demo.html` in any web browser (no build step, no server required).
3. The simulator loads with illustrative data and default settings.

### Publishing via GitHub Pages

1. Push this folder to a GitHub repository.
2. Go to **Settings → Pages**.
3. Set **Source** to `main` branch, root directory.
4. GitHub will publish the site at `https://<username>.github.io/<repo-name>/`.
5. Share the link to `demo.html` (e.g., `https://<username>.github.io/<repo-name>/demo.html`).

### First Steps in the Simulator

1. **Toggle Enacted + Proposed Rules:** Default is ON. Turn OFF to see only current law.
2. **Select Deployment Scope:** Commercial Datacenter vs. Federal National Security System. (S.5548 applies only to Federal NSS.)
3. **Choose Entity Definition Mode:**
   - **Incorporated in China:** Any company domiciled in China.
   - **Manufactured in China:** Any product built in China.
   - **Ownership >= X%:** Entities with SASAC/MIIT affiliation or above ownership threshold.
4. **Adjust Thresholds:** US-BOM exemption (default 65%), phase-in year (default 2028), ownership % (default 50%).
5. **Click "+ Import Data"** to add your own Sayari entities and products.
6. **Run All Tests** to verify the rule engine's behavior.

---

## Evidence Tier Legend

| Tier | Color | Meaning | Example |
|------|-------|---------|---------|
| **ENACTED_LAW** | 🟢 Green | Law in effect and binding | FCC 26-50 (effective Oct 13, 2026) |
| **ENACTED_DESIGNATION** | 🔵 Blue | Official designation; not auto-prohibition | DoW 1260H list (June 10, 2026) |
| **PENDING_BILL** | 🟡 Yellow | Proposed legislation, introduced to Congress | S.5548 (introduced Sept 24, 2026) |
| **REPORTED_DRAFT** | 🟠 Orange | Reported by press; not confirmed by agency | Reuters FCC transceiver ban (Aug 4, 2026) |
| **ANALYST_ESTIMATE** | 🟠 Orange | Market research; unverified | Counterpoint Research: InnoLight 27% market share |
| **HYPOTHETICAL** | 🔴 Red | User-defined scenario or assumption | Custom rule injected for testing |

**Key rule:** Tiers always travel with their data. A SKU failing due to a REPORTED_DRAFT item is labeled as such, never presented as blocked by law.

---

## Student Guide: Sayari to Simulator

<details>
<summary>Click to expand student guide</summary>

### Step 1: Gather Sayari Data

Export or note the following from Sayari for each entity or product:

- **Entity Name** and unique ID
- **Country of incorporation** (ISO 2-letter code: CN, US, TW, etc.)
- **Ultimate Beneficial Owner (UBO)** (e.g., SASAC, MIIT, State Grid)
- **Ownership %** of UBO (0–100)
- **Parent company** ID (if subsidiary)
- **Manufacturing locations** (countries where built)
- **HS codes** for products (e.g., 8517.6210 for optical transceivers)
- **Record source URL** (link to Sayari record)

### Step 2: Choose Import Method

#### Method A: Manual Form Entry

In the simulator, click **"+ Import Data"** drawer:
1. Enter Entity ID, Name, Country, etc.
2. Click **Add Entity**.
3. The entity is live in the rule engine immediately.

#### Method B: Paste JSON

Prepare a JSON array (see example below) and paste into the drawer.

#### Method C: Upload CSV or JSON

Download the template from the drawer, fill in your data, and upload.

### Step 3: Examples (Illustrative)

<details>
<summary>Copyable JSON Example</summary>

```json
[
  {
    "entityId": "innolight_primary",
    "name": "Zhongji Innolight Co., Ltd.",
    "country": "CN",
    "parentId": null,
    "ownershipPct": 100,
    "ubo": "SASAC",
    "buildLocation": "China",
    "coveredList": false,
    "list1260H": true,
    "hsCodes": ["8517.6210", "8517.6220"],
    "url": "https://sayari.com/companies/innolight"
  },
  {
    "entityId": "innolight_thailand",
    "name": "Innolight Thailand Manufacturing",
    "country": "TH",
    "parentId": "innolight_primary",
    "ownershipPct": 100,
    "ubo": "SASAC",
    "buildLocation": "Thailand",
    "coveredList": false,
    "list1260H": false,
    "hsCodes": ["8517.6210"],
    "url": "https://sayari.com/companies/innolight-thailand"
  },
  {
    "entityId": "broadcom_optics",
    "name": "Broadcom Optical Solutions",
    "country": "US",
    "parentId": null,
    "ownershipPct": 100,
    "ubo": null,
    "buildLocation": "USA",
    "coveredList": false,
    "list1260H": false,
    "hsCodes": ["8517.6210"],
    "url": "https://sayari.com/companies/broadcom"
  }
]
```

**Note:** This is ILLUSTRATIVE data. Replace with your actual Sayari findings.

</details>

<details>
<summary>Copyable CSV Example</summary>

```csv
entityId,name,country,parentId,ownershipPct,ubo,buildLocation,coveredList,list1260H,hsCodes,url
innolight_primary,Zhongji Innolight Co. Ltd.,CN,,100,SASAC,China,false,true,"8517.6210,8517.6220",https://sayari.com/companies/innolight
innolight_thailand,Innolight Thailand Manufacturing,TH,innolight_primary,100,SASAC,Thailand,false,false,8517.6210,https://sayari.com/companies/innolight-thailand
broadcom_optics,Broadcom Optical Solutions,US,,100,,USA,false,false,8517.6210,https://sayari.com/companies/broadcom
```

**Note:** This is ILLUSTRATIVE data. Replace with your actual Sayari findings.

</details>

### Step 4: Add Products / BOMs

The simulator ships with 6 sample SKUs (Cisco, Arista switches in 800G/1.6T/3.2T). To add your own:

1. Click **"+ Import Data"** drawer.
2. Currently, products are added via the form under "Manual Entry" (Product name, Maker, Speed Gen, Build Location, US-BOM %).
3. Each product's BOM is auto-scored based on US-sourced components.

**Example BOM logic (illustrative):**
- Cisco 3.2T switch
  - Optical transceiver (InnoLight): 25% BOM cost, non-US-sourced
  - Switch ASIC (Marvell): 35% BOM cost, US-sourced
  - PSU (Broadcom): 15% BOM cost, US-sourced
  - Passive components (various): 25% BOM cost, non-US-sourced
  - **Total US-sourced:** 35% + 15% = 50%

### Step 5: Run Scenarios

1. Set **Entity Definition Mode** (Incorporation, Manufacturing, Ownership).
2. Toggle **Enacted + Proposed** to include/exclude speculative rules.
3. Adjust **US-BOM Threshold**, **Phase-in Year**, **Ownership %**.
4. Watch the **Compliance Status** column update in real time.
5. Check **Assumptions & Falsifiers** to see what could flip the status.

### Step 6: Save & Compare Scenarios

1. Click **"💾 Scenarios"** button.
2. Enter a name (e.g., "Federal NSS - Next-Gen Optics").
3. Click **Save**.
4. Load, compare, and export scenarios (JSON or CSV).

### Example Learning Questions

**Q1:** "If we model MANUFACTURING mode instead of INCORPORATION, how does a Thailand-built InnoLight module rank?"

**A1:** Under MANUFACTURING mode, it's not restricted (not built in China). Under INCORPORATION, it's restricted (InnoLight incorporated in China). This sensitivity shows why **entity definition matters**.

**Q2:** "Our 800G switch was authorized pre-Oct-13. Does S.5548 affect it?"

**A2:** No. FCC 26-50 grandfathers pre-Oct-13 authorizations, and S.5548 also has a 5-year transition. Status: TRANSITION_WINDOW (safe until 2032 if S.5548 passes).

</details>

---

## File Index

| File | Purpose |
|------|---------|
| **README.md** | This file. Overview, quick start, student guide, next steps. |
| **demo.html** | Complete, working simulator. Single HTML file, no build. Open in browser. |
| **SOURCES.md** | Primary-source verification of all 7 baseline regulatory items (FCC 26-50, DoW 1260H, S.5548, Reuters draft, analyst estimates, etc.). URLs, verification status, last-verified dates. |
| **CONCEPT-IDEA.md** | Product vision, evidence-tier framework, student workflow, 5 learning questions, datacenter impact matrix, teaching points, risks of over-claiming. |
| **SPECIFICATIONS.md** | Technical design: architecture, TypeScript interfaces, 9-priority rule engine, import/export schemas, UI layout, 7 test cases, acceptance criteria, backend integration points. |

---

## Next Steps: Backend & Live Sayari Integration

<details>
<summary>Click to expand backend roadmap</summary>

### Phase 2: REST API Backend

**Goal:** Enable user accounts, live Sayari data, persistent storage, and team collaboration.

**Technology:** Node.js/Express (or Python/Flask), PostgreSQL, Sayari API key.

**Endpoints to build:**

```
POST /api/scenarios                    # Create scenario
GET /api/scenarios/:id                 # Retrieve scenario
PUT /api/scenarios/:id                 # Update scenario
DELETE /api/scenarios/:id              # Delete scenario
POST /api/entities/import              # Import Sayari data
GET /api/entities                      # List entities
POST /api/regulatory-items/custom      # Create custom rule
GET /api/regulatory-items              # List all rules
POST /api/compare                      # Side-by-side comparison
GET /api/export                        # Export JSON/CSV
```

### Phase 3: Live Sayari API Integration

**Goal:** Fetch real UBO, ownership, shipment data from Sayari.

**What to build:**

1. **Entity lookup:** `POST /api/entities/lookup?name=InnoLight` → fetch from Sayari, store in DB.
2. **Ownership graph:** `GET /api/entities/:id/ownership-chain` → traverse UBO chain, render SVG.
3. **Shipment history:** `GET /api/entities/:id/shipments` → HS codes, destinations, volumes.
4. **Subsidiary mapper:** `GET /api/entities/:id/subsidiaries` → recursively find children.

**Sayari API key management:**
- Store API key in environment variable (`.env`), never in code.
- Implement rate limiting and caching (Redis).
- Handle errors gracefully; fall back to cached data if Sayari is unreachable.

### Phase 4: Real-Time Regulatory Feed

**Goal:** Automatically track when new regulations are enacted, bills introduced, or designations updated.

**What to build:**

1. **FCC Covered List poller:** Daily check `https://www.fcc.gov/supplychain/coveredlist` for changes.
2. **Congress.gov RSS feed:** Watch for new bills or amendments.
3. **DoW 1260H feed:** Watch Federal Register for designation updates.
4. **Webhook notifications:** Alert users when lists change or bills advance.

**Database schema update:**
```sql
CREATE TABLE regulatory_items (
  id UUID PRIMARY KEY,
  name TEXT NOT NULL,
  tier ENUM ('ENACTED_LAW', 'ENACTED_DESIGNATION', 'PENDING_BILL', ...),
  status ENUM ('VERIFIED', 'PARTIAL', 'UNVERIFIED'),
  source_url TEXT,
  date_enacted DATE,
  date_verified TIMESTAMP,
  created_at TIMESTAMP,
  updated_at TIMESTAMP
);

CREATE TABLE entities (
  id UUID PRIMARY KEY,
  sayari_id TEXT UNIQUE,
  name TEXT NOT NULL,
  country CHAR(2),
  ultimate_beneficial_owner TEXT,
  ownership_pct DECIMAL,
  parent_id UUID REFERENCES entities(id),
  manufacturing_locations TEXT[],
  hs_codes TEXT[],
  on_covered_list BOOLEAN,
  on_1260h_list BOOLEAN,
  record_source_url TEXT,
  retrieved_at TIMESTAMP,
  created_at TIMESTAMP
);

CREATE TABLE scenarios (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  name TEXT NOT NULL,
  settings JSONB,
  created_at TIMESTAMP,
  updated_at TIMESTAMP
);
```

### Phase 5: Team Collaboration

**Goal:** Share scenarios, comment on SKU compliance, track decisions.

**Features:**
- User accounts with email/OAuth.
- Scenario sharing (view/edit permissions).
- Inline comments on SKUs and assumptions.
- Audit trail: who changed what, when.
- Export scenarios with full chain-of-custody metadata.

### Implementation Checklist

- [ ] Set up Node.js/Express backend.
- [ ] Design PostgreSQL schema.
- [ ] Implement user authentication (OAuth2 with GitHub/Google).
- [ ] Build REST API endpoints.
- [ ] Integrate Sayari API client.
- [ ] Add caching layer (Redis).
- [ ] Set up regulatory feed poller.
- [ ] Deploy to AWS/Heroku/DigitalOcean.
- [ ] Modify `demo.html` to call backend APIs instead of using in-memory data.
- [ ] Add team collaboration features.
- [ ] Write backend tests.

</details>

---

## Background & Motivation

A planned section of the demo where a user injects a scenario built from new intelligence about a potential regulation. The section then shows the modeled impact on supply chains and data center design. It ships with a placeholder preset, "Next-Gen Optics Restriction." Students later replace the placeholder data with their own Sayari analysis of real parts.

Every regulatory item carries an evidence tier: `ENACTED_LAW`, `ENACTED_DESIGNATION`, `PENDING_BILL`, `REPORTED_DRAFT`, `ANALYST_ESTIMATE`, or `HYPOTHETICAL`. Proposed or reported items are never presented as law.

**Why this matters:**

- **Data center hardware has long lead times.** Design decisions made years before production. A rule that takes effect after those decisions are locked in forces expensive pivots.
- **Regulation shows up first as a signal.** By the time it's law, options narrow. Anticipatory analysis keeps response options open.
- **Supply chains are rarely visible beyond tier 1.** Knowing who makes a part, who owns them, and where else the part can be sourced is critical to risk modeling.
- **Scenario work builds the habit of "what if" thinking.** It keeps that habit separate from claiming a rule exists.

**What students learn:**

- How to read a Federal Register notice, bill text, or company designation.
- How regulatory definitions (incorporation, manufacturing location, ownership) change outcomes.
- How to trace an ownership chain and spot taint.
- How to reason about confidence tiers and uncertainty.
- How to build a supply-chain model with Sayari data and test it against multiple futures.

---

**Last updated:** 2026-11-16  
**Demo status:** Fully implemented and working  
**Data status:** Illustrative (replace with your Sayari findings)

