# SPECIFICATIONS: Proposed Regulation Simulation

Derived from `CONCEPT-IDEA.md` and `SOURCES.md`. This is a specification only; it contains no code.

**Status of the repo:** the working directory held no demo, stack or code when inspected, so section 9 (file and route changes) is a proposal. Reconcile it with the real demo once one exists.

**Facts used here** come from `SOURCES.md` (last verified 2026-11-16). Where that file says UNVERIFIED, this spec carries the flag through and never upgrades it.

---

## 1. Goals and non-goals

**Goals**

1. Let a user inject a scenario built from new intelligence about a potential regulation, and see the modeled impact on supply chains and data center design.
2. Ship the "Next-Gen Optics Restriction" preset with placeholder BOM and supplier data, so the feature works before any Sayari analysis is done.
3. Let students replace the placeholder data with their own Sayari analysis of real parts.
4. Keep evidence tiers visible everywhere. A proposed or reported item is never presented as law.

**Non-goals**

- Predicting whether a regulation will be enacted or confirmed.
- Legal advice, or any finding that a named company is covered, sanctioned or wrongdoing.
- Calling Sayari from this specification. Section 7 defines integration points and stubs only.

---

## 2. Evidence tiers (applies to every section)

Order from strongest to weakest: `IN_FORCE` > `PROPOSED_BILL` > `REPORTED_DRAFT` > `HYPOTHETICAL`.

| Rule | Requirement |
|---|---|
| T1 | Every Item carries exactly one tier. The tier badge is shown wherever the Item or anything derived from it appears, including exports. |
| T2 | A derived output takes the **weakest** tier among the Items that contribute to it. No averaging. |
| T3 | No wording, color, icon or sort order may make a `PROPOSED_BILL` or `REPORTED_DRAFT` Item look like `IN_FORCE`. Banned phrasing for non-`IN_FORCE` tiers includes "is banned", "must", "prohibits" in present tense. Required phrasing: "proposed", "reported", "would". |
| T4 | An Item may be created as `IN_FORCE` only with a primary Source (a Federal Register notice, FCC document, enacted text). Otherwise it must be created as a weaker tier, or carry `verification = UNVERIFIED` (see 3.2). |
| T5 | Changing an Item's tier to a stronger one requires attaching a new Source. The change is written to the assumption log. |
| T6 | Probability and date-shift sliders never change the tier. |
| T7 | `verification = UNVERIFIED` is a separate flag from tier. It is shown next to the tier badge, and a derived output that depends on an unverified Item inherits an "includes unverified input" marker. |

---

## 3. Data model

All entities are plain records. Types are described abstractly. Dates are ISO 8601 (`YYYY-MM-DD`). Identifiers are stable strings.

### 3.1 Scenario

| Field | Type | Notes |
|---|---|---|
| `id` | string | |
| `name` | string | e.g. "Next-Gen Optics Restriction" |
| `description` | string | |
| `is_preset` | boolean | Preset scenarios are read-only; edits make a copy. |
| `created_at`, `updated_at` | datetime | |
| `item_ids` | list of Item id | At least one. |
| `intel_text` | string | The free-text intel box content (section 4.1). |
| `injector_settings` | object | The values set in section 4 (covered-entity definition, scope, sliders and so on). |
| `portfolio` | object | References to Sku, BomLine and Entity records used for outputs. |
| `assumption_log` | list of LogEntry | See 3.8. |
| `data_status` | enum | `PLACEHOLDER` \| `STUDENT_DATA` \| `MIXED`. Shown as a banner on every output. |

### 3.2 Item

One regulatory or intelligence statement.

| Field | Type | Notes |
|---|---|---|
| `id` | string | |
| `title` | string | |
| `tier` | enum | `IN_FORCE` \| `PROPOSED_BILL` \| `REPORTED_DRAFT` \| `HYPOTHETICAL` |
| `source_id` | string | Required. Points to a Source. |
| `source_url` | string | Required. Derived from the Source. May be the literal `UNVERIFIED` (see below). Never null. |
| `last_verified` | date | Required. Derived from the Source. For an UNVERIFIED Item this is the date the check was attempted. Never null. |
| `verification` | enum | `VERIFIED` \| `UNVERIFIED` |
| `regulator` | string | e.g. FCC, Congress, DoW |
| `instrument_type` | enum | `FINAL_RULE` \| `COVERED_LIST_ACTION` \| `BILL` \| `PRESS_REPORT` \| `FNPRM` \| `USER_HYPOTHETICAL` \| `OTHER` |
| `instrument_ref` | string | e.g. "FCC 26-50", "S.5548" |
| `covered_entity_def` | object | See 4.3. |
| `product_scope` | list of enum | Speed generations (see 4.4). |
| `applies_to` | list of enum | `NEW_AUTHORIZATIONS` \| `NEW_MODELS` \| `INSTALLED_BASE` \| `FEDERAL_ONLY` |
| `effective_date` | date or null | Null when not determined. |
| `effective_date_basis` | string | e.g. "30 days after Federal Register publication" or "enactment + 5 years". |
| `transition_years` | number or null | |
| `bom_exemption_threshold_pct` | number or null | |
| `affects_modeled_skus` | boolean | If false the Item is context only and contributes no exposure. See the design note below. |
| `scope_limits` | list of string | Plain statements of what the Item does not cover. Feeds the "what does not change" panel. |
| `unverified_details` | list of string | Specific sub-claims that are unverified. |
| `notes` | string | |

**Rule for `source_url`:** the literal string `UNVERIFIED` is allowed only when `verification = UNVERIFIED`. A VERIFIED Item must hold a real URL. The UI shows `UNVERIFIED` as a visible badge, not as a link.

**Design note on `affects_modeled_skus`:** `SOURCES.md` records that FCC 26-50 ¶37 limits the component rule to components made by entities identified on the Covered List. It does not extend to entities that only make the location-based categories (UAS, routers). The router and power inverter Items are therefore context for the preset. They default to `affects_modeled_skus = false`. A user can switch this on, which records an assumption log entry and caps the Item's contribution at `HYPOTHETICAL`.

### 3.3 Source

| Field | Type | Notes |
|---|---|---|
| `id` | string | |
| `title` | string | |
| `url` | string | Real URL or the literal `UNVERIFIED`. |
| `source_type` | enum | `PRIMARY` \| `LAW_FIRM` \| `TRADE_PRESS` \| `ANALYST` \| `PLACEHOLDER` |
| `last_verified` | date | |
| `verification` | enum | `VERIFIED` \| `UNVERIFIED` |
| `citation` | string | e.g. "91 FR 57798", "DA 26-278" |
| `excerpt` | string | Short quote. Optional. |
| `access_note` | string | e.g. "fcc.gov returned HTTP 403 to automated fetch" |

### 3.4 Entity

A company, with the attributes needed for the covered-entity tests.

| Field | Type | Notes |
|---|---|---|
| `id` | string | |
| `name` | string | |
| `is_placeholder` | boolean | Placeholder entities are labeled and never read as findings about a real company. |
| `country_incorporated` | string (ISO 3166) | |
| `ownership_edges` | list of {`owner_entity_id`, `owner_country`, `pct`, `source_id`} | Direct holdings. |
| `computed_cn_ownership_pct` | number | Derived. See 7.4 for the method. |
| `named_in_item_ids` | list of Item id | Entities named by an instrument's text. |
| `on_covered_list` | enum | `YES` \| `NO` \| `UNVERIFIED`, with `covered_list_as_of` date and `source_id`. |
| `on_1260h_list` | enum | `YES` \| `NO` \| `UNVERIFIED`, with source. |
| `sayari_ref` | string or null | Sayari entity identifier once resolved. |
| `provenance` | enum | `STUB` \| `SAYARI` \| `MANUAL` |

### 3.5 Sku

A module or product line.

| Field | Type | Notes |
|---|---|---|
| `id` | string | |
| `name` | string | |
| `speed_generation` | enum | `400G` \| `800G` \| `1.6T` \| `3.2T` |
| `form` | enum | `PLUGGABLE` \| `LPO` \| `CPO` \| `OTHER` |
| `maker_entity_id` | string | |
| `manufacturing_country` | string (ISO 3166) | Where the module is made. |
| `is_new_model_after` | date or null | First availability of this model. Used by the `NEW_MODELS` test. |
| `requires_fcc_authorization` | enum | `YES` \| `NO` \| `UNKNOWN` | Input, not a finding. A user-set assumption, logged. Controls whether the `NEW_AUTHORIZATIONS` test can apply (see 6.2). |
| `bom_line_ids` | list of BomLine id | |
| `annual_units` | map of year → number | 2026-2030. |
| `installed_units` | number | Units already deployed before the scenario start. |
| `zone` | enum | `FEDERAL` \| `COMMERCIAL` \| `MIXED` | Used by `FEDERAL_ONLY`. |
| `alt_suppliers` | list of {`entity_id`, `capacity_units_per_year`, `capacity_is_assumption`} | Capacity is always flagged (rule D6). |

### 3.6 BomLine

| Field | Type | Notes |
|---|---|---|
| `id` | string | |
| `sku_id` | string | |
| `component` | string | e.g. "DSP", "laser", "PCB" |
| `cost_usd` | number | |
| `origin_country` | string | |
| `us_sourced` | boolean | User-editable. Defaults to `origin_country == US`. |
| `is_logic_bearing` | enum | `YES` \| `NO` \| `UNKNOWN` | Input, based on the 47 CFR 2.902 definition in FCC 26-50 ¶28. A user judgment, logged. Not a legal finding. |
| `supplier_entity_id` | string | |
| `data_status` | enum | `PLACEHOLDER` \| `STUDENT_DATA` |

### 3.7 Relationships

Scenario → many Items. Item → one Source. Sku → many BomLines, one maker Entity. BomLine → one supplier Entity. Entity → many ownership edges, each with a Source.

### 3.8 LogEntry (assumption log)

`timestamp`, `actor`, `field`, `old_value`, `new_value`, `reason` (free text, optional), `tier_effect` (the weakest tier the change introduces), `kind` (`DEFAULT_PLACEHOLDER` \| `USER_EDIT` \| `SAYARI_IMPORT` \| `STUB_IMPORT`).

---

## 4. Scenario injector inputs

The injector is a form. Each control updates the Scenario draft. Every change appends a LogEntry.

### 4.1 Free-text intel box

- Plain text area for pasted intelligence (an article excerpt, a note, a bill summary).
- It is stored verbatim as `intel_text` and shown beside the outputs.
- The system does **not** parse it automatically in this version. The user must fill the structured fields below. The UI says so.
- Next to the box, the user must pick a tier and attach or enter a Source (type, URL or `UNVERIFIED`, date). The Item cannot be saved without both.

### 4.2 Regulator and instrument type

- `regulator`: select (FCC, Congress, DoW/DoD, Commerce, Other) plus free text.
- `instrument_type`: select from the Item enum in 3.2.
- Guard: choosing `PRESS_REPORT` forces the tier to `REPORTED_DRAFT` or `HYPOTHETICAL`. Choosing `BILL` forces `PROPOSED_BILL` or `HYPOTHETICAL`. A `FINAL_RULE` may be `IN_FORCE` only with a primary Source (rule T4).

### 4.3 Covered-entity definition

A single choice per Item. The choice decides which entities (and so which Skus) the Item reaches.

| Mode | Meaning | Test applied to a Sku |
|---|---|---|
| `INCORPORATED_IN_CHINA` | The producing entity is incorporated in China | `maker.country_incorporated == CN` |
| `MANUFACTURED_IN_CHINA` | The module is manufactured in China | `sku.manufacturing_country == CN` |
| `SAYARI_OWNERSHIP_GTE_X` | Ownership by China-incorporated owners is at least X% | `maker.computed_cn_ownership_pct >= X`; `X` is an input from 0 to 100, **inclusive** at the boundary |
| `NAMED_ENTITIES` | *(addition, not in the original brief)* The entities named in the instrument text | `maker.id` is in the Item's named list, including affiliates only if the user adds them |

`NAMED_ENTITIES` is added because the preset's instruments are name-based (S.5548 names InnoLight and Eoptolink with affiliates; FCC 26-50 reaches entities on the Covered List). The three modes above are for exploring "what if the definition were broader." The UI labels the three as hypothetical modeling choices unless the Item's own text defines the class.

For the preset:

- FCC 26-50 uses `NAMED_ENTITIES`, with the list being "entities on the Covered List". The Covered List status for the four preset companies is UNVERIFIED (SOURCES.md item 2), so the list is empty by default and the user must add entities.
- S.5548 uses `NAMED_ENTITIES` with InnoLight and Eoptolink and affiliates.
- The reported draft uses `INCORPORATED_IN_CHINA` as a **HYPOTHETICAL** reading, because the report's definition is not known. This is shown as an assumption.

### 4.4 Product scope by speed generation

Multi-select: `400G`, `800G`, `1.6T`, `3.2T`. Skus outside the selection are unaffected by the Item. The reported draft preset selects `3.2T` only. S.5548 selects all generations, because the bill's "optical transceiver" definition is broad (it includes pluggables and integrated optical modules, per the bill text read in SOURCES.md item 4). The FCC 26-50 preset leaves the scope as the user's choice, since the order's definition is functional and not by speed (SOURCES.md item 1).

### 4.5 Applies-to

Multi-select, default per preset:

| Value | Test |
|---|---|
| `NEW_AUTHORIZATIONS` | Counts only units that need a new equipment authorization on or after the effective date. Requires `requires_fcc_authorization != NO`. |
| `NEW_MODELS` | Counts only Skus with `is_new_model_after >= effective_date`. |
| `INSTALLED_BASE` | Counts `installed_units`. Default off for every preset. Turning it on is logged as a `HYPOTHETICAL` assumption, because FCC 26-50 ¶38 says previously authorized equipment is unaffected. |
| `FEDERAL_ONLY` | Counts only units in `FEDERAL` zones (or the `FEDERAL` share of `MIXED`). |

### 4.6 Dates and thresholds

- `effective_date`: date picker, nullable. For `IN_FORCE` Items with a source-stated date (FCC 26-50: 2026-10-13), the field is locked.
- `transition_years`: number. For S.5548 the default is 5, with basis "enactment + 5 years".
- `assumed_enactment_date`: date, **HYPOTHETICAL**, required when an Item's effective date is "enactment + N". Never defaulted to a real date. The UI asks for it.
- `us_bom_exemption_threshold_pct`: number 0-100. Preset default 65, labeled `REPORTED_DRAFT` ("possible exemption, reported, unconfirmed"). The exemption is off by default and enabled by a toggle (see 5.2).

### 4.7 Sliders

| Slider | Range | Behavior |
|---|---|---|
| Probability | 0-100% | Default 100 ("conditional on adoption"). Locked at 100 for `IN_FORCE`. Outputs show the conditional result by default. A weighted result appears only when the user moves the slider, and is labeled "probability-weighted, user assumption." The value is logged. No default is shipped as a forecast. |
| Date shift | -24 to +36 months, step 1 | Shifts `effective_date` for non-`IN_FORCE` Items only. Locked for `IN_FORCE`. Logged. |

---

## 5. Outputs

All outputs show: the data-status banner (`PLACEHOLDER`, `STUDENT_DATA` or `MIXED`), the tier of the weakest contributing Item, and an "includes unverified input" marker when applicable.

### 5.1 Exposure map by year, 2026-2030

- **Form:** a grid with rows = Skus (grouped by speed generation) and columns = 2026, 2027, 2028, 2029, 2030. Each cell shows the exposed share of that year's units. A totals row shows the portfolio total.
- **Per-cell logic:** exposed units for year Y = units in Y that satisfy all of: the covered-entity test (4.3), speed-generation scope (4.4), applies-to tests (4.5), and Y at or after the effective date plus any transition (5.4). The share is exposed units divided by units in Y.
- **Spend view:** a toggle switches from units to BOM spend.
- **Tier:** every cell carries the weakest tier among the Items that make it non-zero. Cells driven only by a `REPORTED_DRAFT` Item are styled and labeled as reported. Cells driven by more than one Item list them.
- **Probability-weighted view:** off by default (4.7).
- **Sources:** each cell links to its contributing Items and their Sources.

### 5.2 Editable US-BOM calculator

- Applies to a chosen Sku, using its BomLines.
- Each line has editable `cost_usd`, `origin_country` and `us_sourced`.
- **Calculation:** US-sourced share = sum of `cost_usd` where `us_sourced` is true, divided by total `cost_usd`, as a percentage.
- **Comparison:** the result is compared with the threshold (default 65). The exemption passes when share is **greater than or equal to** the threshold.
- **Display:** share, threshold, margin (share minus threshold), a pass/fail marker, and a per-line contribution bar.
- **Sensitivity:** "what US-sourced cost would a line need to change by to cross the threshold."
- **Labeling:** the whole panel is labeled `REPORTED_DRAFT`: "A US-sourced bill-of-materials exemption was reported as possible; it is unconfirmed." The definition of "US-sourced" (by value, by line) is a logged assumption, because the report's definition is not known.
- **Effect:** if the toggle "apply reported exemption" is on and a Sku passes, the exposure map removes that Sku from the `REPORTED_DRAFT` Item's exposure. That change is tier-capped at `REPORTED_DRAFT`.

### 5.3 Timeline with decision points

- Horizontal timeline, 2026-2030 (extendable to 2032 for the 5-year bill transition).
- **Markers from Items:** FCC 26-50 effective 2026-10-13 (`IN_FORCE`, verified); S.5548 introduced 2026-09-24 (`PROPOSED_BILL`); S.5548 effective at the assumed enactment date plus 5 years (shown only when the user enters an assumed enactment date, labeled `HYPOTHETICAL`); reported ramp window about 2028-2029 (`REPORTED_DRAFT`); the router and power inverter Items as context markers, with the unverified details flagged.
- **Decision points** are computed from user-set lead times (all `HYPOTHETICAL`, logged): qualification start = exposure start minus qualification lead time; last-buy or buy-ahead window = exposure start minus procurement lead time; "review point" at each Item's effective date. They appear as flags on the timeline with the tier of the Item that triggers them.
- A user can drag the date-shift slider (4.7) and see markers move.
- The timeline shows today's date.

### 5.4 Transition handling

Exposure begins at `effective_date + transition_years`, if a transition applies, and exposure is zero before that. For `IN_FORCE` Items with no transition, exposure begins at `effective_date`.

### 5.5 "What does not change" panel

- Generated from each Item's `scope_limits` plus the Sku and scope settings, shown as a list of statements, each with its tier, source and verification badge.
- It is always visible beside the exposure map, not hidden in a tooltip.
- Content for the preset (from SOURCES.md):
  - Previously authorized equipment may continue to be marketed, imported and used under its existing authorization (FCC 26-50 ¶38).
  - The component rule covers hardware only, not software or firmware (¶30, though the FNPRM seeks comment on software and firmware, ¶¶150-152).
  - The component rule applies only to logic-bearing components from entities identified on the Covered List (¶37). It does not extend to entities that only make location-based categories such as routers.
  - Non-logic-bearing parts (mechanical, passive, power-only) are outside the definition (¶28).
  - S.5548, as introduced, is a federal procurement restriction on critical systems; it does not restrict commercial sales (bill text). It is not enacted.
  - The reported draft concerns new models, not units already in service (per the brief; unconfirmed).
  - Nothing in this tool shows that any named company is covered or has done anything wrong.
- The panel also lists the user's own scope choices: for example "Installed base: not affected (default)."

### 5.6 Assumption log

- A table of every LogEntry (3.8), sortable by tier effect and time.
- Pre-filled with placeholder defaults the preset shipped with (kind `DEFAULT_PLACEHOLDER`).
- Exportable with the scenario.
- Shows a count of open UNVERIFIED items and PLACEHOLDER data points.

### 5.7 Export

A scenario export (JSON or readable report) includes the Items with tiers, Sources, `last_verified` dates, the injector settings, the assumption log and the data-status banner. The tier and verification badges are part of the exported data, not only the screen.

---

## 6. Calculation rules (summary)

### 6.1 Exposure for a Sku in year Y under one Item

Exposed if all of:

1. `Item.affects_modeled_skus` is true.
2. The Sku's maker and manufacturing country satisfy the covered-entity mode (4.3).
3. `Sku.speed_generation` is in the Item's scope (4.4).
4. At least one applies-to test (4.5) selects the units, using the unit counts for that test.
5. Year Y is at or after the effective date plus transition (5.4), after date-shift.
6. The Sku is not removed by a passing BOM exemption (5.2), when that toggle is on.

### 6.2 Applies-to details

- `NEW_AUTHORIZATIONS` counts units in years at or after the effective date, for Skus where `requires_fcc_authorization != NO`. If the value is `UNKNOWN`, the cell is shown as "possibly exposed (assumption)" and the tier is capped at `HYPOTHETICAL`. Whether a given module or its host needs a new authorization is an input, not a finding.
- `NEW_MODELS` counts all units of Skus with `is_new_model_after >= effective_date`, and none of the units of older models.
- `INSTALLED_BASE` counts `installed_units` in the first affected year.
- `FEDERAL_ONLY` multiplies the units by the federal share of the Sku's zone.

### 6.3 Combining Items

Per cell, exposed share is the union of the Items that flag it (no double counting). The cell tier is the weakest tier among Items that flag the cell. A cell flagged by an `IN_FORCE` Item and a `REPORTED_DRAFT` Item shows `REPORTED_DRAFT`, with both listed, per rule T2.

---

## 7. Sayari integration points, with stub fallbacks

**Tool availability:** no Sayari MCP tools were visible in the session where this specification was written. Therefore every integration point below has a stub fallback, and the specification gives no real tool names. When Sayari tools become visible, map each operation to the actual tool names and record the mapping in the integration module's documentation.

All integration goes through one adapter interface. The rest of the app never calls Sayari directly.

### 7.1 Adapter operations

| Operation | Purpose | Input | Output | Used by |
|---|---|---|---|---|
| `resolveEntity` | Find the Sayari record for a company | name, country (optional) | candidate matches with identifiers and confidence | Entity `sayari_ref`, student workflow step 2 |
| `getOwnership` | Ownership edges up and down the chain | entity ref | owners with `pct`, jurisdictions, source references | `SAYARI_OWNERSHIP_GTE_X` mode, `computed_cn_ownership_pct` |
| `getSupplyLinks` | Trade or shipment relationships for a part or supplier | entity ref, product/HS filter | supplier and buyer links, origin country | BomLine suppliers, manufacturing country |
| `getRiskFlags` | List or watchlist flags | entity ref | flag type and source | Entity `on_1260h_list` and similar flags (see the caution below) |
| `getFacilities` | Production sites and countries | entity ref | site, country | Sku `manufacturing_country` |

**Caution:** flags from Sayari are not the same as the FCC Covered List. `on_covered_list` is set from the FCC page or a dated FCC document (SOURCES.md item 2), never inferred from a Sayari flag.

### 7.2 Stub fallbacks

For each operation, if the adapter reports "tool not available," "error," or "no result," the app uses a stub:

| Fallback | Behavior |
|---|---|
| Stub data files | A fixed set of placeholder entities, ownership edges, supply links and facilities, in the placeholder data folder (section 9). Names are fictional ("Placeholder Optics Co. A") and never match real companies. |
| Labeling | Stub records carry `provenance = STUB`, `is_placeholder = true`, Source type `PLACEHOLDER`, and the data-status banner reads `PLACEHOLDER`. |
| Manual entry | Every field the adapter would fill can be typed in by the user (`provenance = MANUAL`), with a required source note. |
| CSV import | A student can import entities, ownership edges, BOM lines and Skus from a CSV, for results they obtained from Sayari outside the app. Imports are logged (`SAYARI_IMPORT` when the user says the data came from Sayari, `STUB_IMPORT` for the bundled files). |
| Failure message | A visible message: "Sayari data is not available; showing placeholder data." It never fails silently. |

### 7.3 Student workflow mapping

1. Pick parts: create Skus.
2. Analyze with Sayari: `resolveEntity`, `getOwnership`, `getSupplyLinks`, `getFacilities` fill Entities and BomLines (or manual entry or CSV).
3. Map findings to the scenario: choose the covered-entity mode and set `X`.
4. Replace placeholder data: replaced records switch `data_status` to `STUDENT_DATA`. The banner reads `MIXED` until all placeholders are replaced.
5. Assign tiers and verification: done per Item (4.1).
6. Run and interrogate: change one input at a time, read the assumption log.

### 7.4 Computed ownership

`computed_cn_ownership_pct` is the sum of ownership held by China-incorporated owners, combining direct and indirect holdings, where an indirect holding is the product of percentages along the path, and paths are summed. This method is an assumption. The UI states it, and any cycle or missing percentage makes the result "incomplete" with an explicit marker. A missing Sayari result means "unknown," never "zero." The panel shows the coverage note: "no Sayari hit does not mean no exposure."

---

## 8. Data center design rules

These rules turn the scenario results into design considerations. They are **suggestions with reasons**, not requirements. Each suggestion shows its triggering Items with tiers, in wording matched to the weakest tier (rule T3).

| ID | Rule | Trigger | Notes |
|---|---|---|---|
| D1 | **Optics-agnostic multi-sourcing.** Design hosts and procurement to accept standards-compliant optics from at least two supplier groups that do not share an owner (by the chosen entity definition). | Any contributing Item with non-zero exposure. | Flag any host feature that locks to one transceiver vendor. The S.5548 text itself says implementing regulations may not rely solely on proprietary vendor-authentication features that block non-host-manufacturer transceivers (new subsec. (c)(2)(B)(iii), per SOURCES.md item 4). Cite it as a bill provision, not law. |
| D2 | **Buy-ahead of 800G/1.6T.** Show an option: qualify or stock current-generation optics from sources outside the scenario's covered set before the exposure start. | Scenario scope includes a generation the portfolio will deploy, with exposure starting inside the planning window. | For a `REPORTED_DRAFT` Item the panel says "option, not recommendation," and shows cost-of-regret as a user-entered number (`HYPOTHETICAL`). The reported draft concerns new models from the 3.2T generation, so buy-ahead applies to current models only to the extent the user's scope says so. |
| D3 | **Federal vs commercial zoning.** Tag halls, pods or racks as `FEDERAL`, `COMMERCIAL` or `MIXED`, and report exposure per zone. | Any Item with `FEDERAL_ONLY`. | S.5548 as introduced is a federal procurement bill, so zoning decides which units it could touch. Recommend keeping federal-zone inventory separable. |
| D4 | **Pluggable vs CPO/LPO.** Compare exposure by `form`, and warn that a form change does not automatically avoid exposure. | The user compares forms. | The S.5548 "optical transceiver" definition includes linear pluggable optics and "integrated optical module" (bill text, new subsec. (j)(7)). The FCC 26-50 definition is functional (¶28), so whether a CPO or LPO assembly is a logic-bearing hardware component is a per-part judgment (`BomLine.is_logic_bearing`). Do not present any form as exempt. |
| D5 | **Vendor exposure to the component rule.** For each host or switch vendor, list BomLines with `is_logic_bearing = YES` whose supplier matches the covered-entity test, and show the vendor's share of affected spend. | The FCC 26-50 Item is in the scenario. | The FCC rule applies to a device's authorization, so host-level exposure depends on components inside the host. The FNPRM proposes HBOM and SBOM disclosures (SOURCES.md item 1), so vendor data may be unavailable today; show "unknown" and a vendor-question checklist. |
| D6 | **Supplier capacity is a flagged assumption.** Any alternative supplier capacity figure is entered by the user, shown with an "assumption" flag, and never presented as fact. | Any use of `alt_suppliers`. | Show a "capacity covers demand?" check as `HYPOTHETICAL`. Note that S.5548 would require a Commerce assessment of US and allied capacity (bill text, SOURCES.md item 4); the tool does not predict its outcome. |

---

## 9. File and route changes (proposed)

The repo had no stack or demo when this was written, so these names are neutral. Adapt the folder names, route style and file types to the real project.

**Route**

- A new section reached at `/regulation-simulation`, added to the demo's navigation as "Proposed Regulation Simulation."
- Sub-views: Injector, Exposure map, BOM calculator, Timeline, What does not change, Assumption log, Sources.

**Proposed files**

| Path | Purpose |
|---|---|
| `docs/SPECIFICATIONS.md` (this file, currently at the project root) | Specification |
| `data/presets/next-gen-optics-restriction.json` | Preset Scenario with the four Items and their Sources |
| `data/sources/sources.json` | Source records, mirroring `SOURCES.md` |
| `data/stubs/entities.json` | Placeholder Entities and ownership edges |
| `data/stubs/skus-and-bom.json` | Placeholder Skus and BomLines |
| `data/stubs/sayari/` | One stub response per adapter operation (7.1) |
| `src/regulation-simulation/` | Feature module: model definitions, validation, calculation (section 6), design rules (section 8) |
| `src/regulation-simulation/sayari-adapter` | Adapter interface and stub implementation |
| `src/regulation-simulation/ui/` | Injector, outputs and panels |
| `tests/regulation-simulation/` | Test cases from section 10 |

**Preset contents (from `SOURCES.md`)**

| # | Item | Tier | Verification | Notes |
|---|---|---|---|---|
| 1 | FCC 26-50 component rule | `IN_FORCE` | VERIFIED | Effective 2026-10-13; new authorizations only; hardware only; Covered List entities (¶37). Source: FCC-26-50 and 91 FR 57798. |
| 2 | Routers, Covered List, DA 26-278 (2026-03-23) | `IN_FORCE` | VERIFIED for the router action | `affects_modeled_skus = false` (context). Sub-claim "some approvals expire 2027-2028" is UNVERIFIED (listed in `unverified_details`). |
| 2b | Power inverters (Jul 2026) | `IN_FORCE` as stated in the brief | **UNVERIFIED**, `source_url = UNVERIFIED` | No primary source found. Context only. See open question Q1. |
| 3 | S.5548 | `PROPOSED_BILL` | VERIFIED (as introduced) | Introduced 2026-09-24; names InnoLight and Eoptolink; 5-year transition; federal procurement. Source: govinfo bill text; press release as secondary support. |
| 4 | Reported FCC draft on new Chinese transceiver models, 3.2T generation, ramp about 2028-29, possible BOM exemption at 65% | `REPORTED_DRAFT` | **UNVERIFIED**, `source_url = UNVERIFIED` | Attributed to Reuters, 2026-08-04, per the brief. The article was not read. The FCC has not confirmed it. |

Preset context: the 1260H listing of InnoLight (Federal Register 2026-11571, 2026-06-10) is stored as a Source and an Entity attribute (`on_1260h_list = YES`), not as a regulatory Item, because a 1260H listing is a Department designation and not an FCC Covered List action.

---

## 10. Test cases

Test data uses placeholder entities. "Placeholder Optics Co. A" is incorporated in China, makes module `M-A` in Thailand, and is 100% owned by a China-incorporated parent unless stated.

### 10.1 Thailand-manufactured module under each entity definition

Setup: one Sku `M-A` (800G), 1,000 units a year in 2027-2030, `requires_fcc_authorization = YES`, scope 800G, applies-to `NEW_AUTHORIZATIONS`, effective date 2027-01-01, no transition. Entity as above.

| ID | Mode | Setting | Expected |
|---|---|---|---|
| TH-1 | `INCORPORATED_IN_CHINA` | none | **Exposed** from 2027. Maker is incorporated in CN. |
| TH-2 | `MANUFACTURED_IN_CHINA` | none | **Not exposed.** The module is made in Thailand. The "what does not change" panel states that this definition ignores the maker's incorporation and ownership. |
| TH-3 | `SAYARI_OWNERSHIP_GTE_X` | X = 25, ownership = 100% | **Exposed.** |
| TH-4 | `SAYARI_OWNERSHIP_GTE_X` | X = 51, ownership = 40% | **Not exposed.** |
| TH-5 | `SAYARI_OWNERSHIP_GTE_X` | X = 50, ownership = 50% | **Exposed.** The boundary is inclusive. |
| TH-6 | `SAYARI_OWNERSHIP_GTE_X` | X = 50, ownership = 49% | **Not exposed.** |
| TH-7 | `SAYARI_OWNERSHIP_GTE_X` | ownership data missing (adapter returned no result) | The result shows "unknown / incomplete," not zero, with the coverage note (7.4). |
| TH-8 | `NAMED_ENTITIES` | Entity not in the named list | **Not exposed.** The module's Thai manufacturing and the maker's Chinese incorporation do not matter under this mode. |

Each result displays the tier of the contributing Item and the data-status banner `PLACEHOLDER`.

### 10.2 Installed-base case

Setup: Sku `M-B` (800G), 5,000 installed units from before 2026, 1,000 new units a year from 2027, entity covered under `NAMED_ENTITIES`, effective date 2027-01-01.

| ID | Applies-to | Expected |
|---|---|---|
| IB-1 | `NEW_AUTHORIZATIONS` only (FCC 26-50 preset) | Installed-base exposure **0** in every year. New units are exposed from 2027. The "what does not change" panel cites previously authorized equipment (FCC 26-50 ¶38). |
| IB-2 | `NEW_AUTHORIZATIONS` + `INSTALLED_BASE` | Installed-base exposure appears. The cell tier is capped at `HYPOTHETICAL`. A LogEntry records the change. The panel no longer lists installed base as unaffected. |
| IB-3 | `INSTALLED_BASE` with probability 40 | Conditional view still shows the full exposure. The weighted view shows 40% of it and is labeled "user assumption." Tier unchanged by the slider (T6). |

### 10.3 Evidence-tier and verification tests

| ID | Case | Expected |
|---|---|---|
| EV-1 | A cell is flagged by FCC 26-50 (`IN_FORCE`) and by the reported draft (`REPORTED_DRAFT`) | Cell tier shows `REPORTED_DRAFT`, both Items listed. |
| EV-2 | Try to save an Item with tier `IN_FORCE` and no Source | Rejected. |
| EV-3 | Try to save a `VERIFIED` Item with `source_url = UNVERIFIED` | Rejected. |
| EV-4 | Save the reported draft Item with `source_url = UNVERIFIED` and `verification = UNVERIFIED` | Accepted. The badge "REPORTED_DRAFT, unverified" appears everywhere the Item does. |
| EV-5 | Any output derived from the power inverter Item | Marked "includes unverified input." |
| EV-6 | Text check on non-`IN_FORCE` Items | No present-tense "is banned" or "prohibits" in generated text (T3). |
| EV-7 | Export a scenario | Tiers, verification flags, URLs and `last_verified` dates are all in the export. |
| EV-8 | Move the date-shift slider on an `IN_FORCE` Item | Control locked, date unchanged. |
| EV-9 | Switch `affects_modeled_skus` on for the router Item | Contribution appears, capped at `HYPOTHETICAL`, logged. |

### 10.4 BOM calculator

| ID | Case | Expected |
|---|---|---|
| BOM-1 | US-sourced cost 65.0 of 100.0, threshold 65 | Share 65.0%, **passes** (>=). |
| BOM-2 | US-sourced cost 64.99 of 100.0, threshold 65 | **Fails**. Margin -0.01. |
| BOM-3 | Edit a line's `us_sourced` flag | Share recalculates; a LogEntry is written. |
| BOM-4 | Exemption toggle off | Reported draft exposure unchanged; the calculator still displays the share. |
| BOM-5 | Exemption toggle on and Sku passes | Sku removed from the reported draft Item's exposure; cell tier stays `REPORTED_DRAFT`. |
| BOM-6 | Zero total cost | Calculator shows "no BOM data," not a number. |

### 10.5 Other behaviors

| ID | Case | Expected |
|---|---|---|
| OT-1 | Federal-only Item, Sku zone `COMMERCIAL` | Exposure 0. Zone `MIXED` with a 30% federal share gives 30% of units. |
| OT-2 | S.5548 with no assumed enactment date | No effective-date marker or exposure is shown; the UI asks for the date. With a date, the effective date is enactment + 5 years, tier `HYPOTHETICAL` for the date. |
| OT-3 | Product scope `3.2T` only, Sku is `800G` | Not exposed under that Item. |
| OT-4 | Sayari adapter returns "tool not available" | Stub data loads, the banner reads `PLACEHOLDER`, the failure message shows, and no silent fallback. |
| OT-5 | Student replaces all placeholder Skus and entities | Banner changes from `PLACEHOLDER` to `MIXED` to `STUDENT_DATA` as records are replaced. |
| OT-6 | `requires_fcc_authorization = UNKNOWN` | Exposure shown as "possibly exposed (assumption)", tier capped at `HYPOTHETICAL`. |
| OT-7 | Design rule D4 with a CPO Sku | Warning shown; the Sku is not labeled exempt. |
| OT-8 | Design rule D6 with a capacity figure | Figure carries an "assumption" flag in the UI and export. |
| OT-9 | Assumption log after editing three inputs | Three new `USER_EDIT` entries with old and new values. |
| OT-10 | Add a second Item with `applies_to` `NEW_MODELS` and 3.2T scope | Only Skus first available on or after the effective date are exposed. |

---

## 11. Acceptance criteria

The feature is accepted when all of the following hold.

1. **Section exists.** A "Proposed Regulation Simulation" section is reachable from the demo, with the injector and all five outputs (exposure map, BOM calculator, timeline, "what does not change" panel, assumption log).
2. **Preset loads.** "Next-Gen Optics Restriction" loads with the four items (and the unverified power inverter item), each with tier, source URL (or the literal `UNVERIFIED`) and last-verified date, matching `SOURCES.md`.
3. **Tier integrity.** Every Item, cell and export shows its tier. No `PROPOSED_BILL` or `REPORTED_DRAFT` content reads as law (tests EV-1 to EV-7 pass).
4. **Weakest-tier rule.** Derived outputs always take the weakest contributing tier.
5. **Unverified handling.** The power inverter item, the reported draft, and the router 2027-2028 expiry detail are visibly marked unverified wherever shown, and cannot be silently promoted.
6. **Injector.** All inputs in section 4 work, including the free-text box (stored, not auto-parsed), the three entity definitions, speed-generation scope, applies-to options, dates, transition years, the BOM threshold, and both sliders. Every change is logged.
7. **Entity definitions.** Tests TH-1 to TH-8 pass, including the inclusive ownership boundary and the missing-data case.
8. **Installed base.** Tests IB-1 to IB-3 pass. The default preset never counts installed base, and turning it on is logged and tier-capped.
9. **BOM calculator.** Tests BOM-1 to BOM-6 pass. The threshold is editable and the panel is labeled `REPORTED_DRAFT`.
10. **Exposure map.** It covers 2026-2030, supports a units and spend view, and shows contributing Items per cell. The conditional view is the default, and the probability-weighted view appears only on user action.
11. **Timeline.** It shows the Item dates and the computed decision points, and markers move with the date-shift slider. No effective date is invented for S.5548.
12. **What does not change.** The panel is always visible next to the exposure map, and its preset content matches the sourced scope limits in 5.5.
13. **Design rules.** D1-D6 produce suggestions with reasons and tiers. D4 never labels an architecture as exempt, and D6 capacity is always flagged as an assumption.
14. **Sayari fallback.** With no Sayari tools available, the feature still runs on clearly labeled stub data, manual entry and CSV import (OT-4, OT-5). Nothing in the app depends on a specific Sayari tool name.
15. **Placeholder safety.** Placeholder entities are fictional, labeled, and never presented as findings about real companies. The "not a finding of wrongdoing or coverage" statement is visible on outputs that name entities.
16. **Export.** Scenario export carries tiers, verification flags, URLs, dates, settings and the assumption log (EV-7).
17. **Documentation.** `SOURCES.md` and `CONCEPT-IDEA.md` are linked from the section, and the unresolved questions below are listed in the repo.

---

## 12. Open questions

| # | Question | Why it matters |
|---|---|---|
| Q1 | What is the power inverter action (July 2026) and where is its primary source? | SOURCES.md could not find it. Until it is found, the Item stays unverified, context-only, and `source_url = UNVERIFIED`. Decide whether an unverified `IN_FORCE` claim should be allowed at all, or should default to a weaker tier. This spec allows it with the unverified badge. |
| Q2 | Does a given optical module, or the host that contains it, require an FCC equipment authorization? | It decides whether `NEW_AUTHORIZATIONS` reaches optics. The spec treats it as an input (`requires_fcc_authorization`). |
| Q3 | Which entities are on the Covered List today? | fcc.gov was not readable. As of the March 23, 2026 list, none of InnoLight, Eoptolink, Dongshan Precision or TFC appeared. The default named list is empty until a student verifies the current list. |
| Q4 | What is the Reuters report's actual wording on scope, definition of "US-sourced," and the 65% figure? | The article was not read. The BOM definition and covered-entity reading for the reported draft are logged assumptions. |
| Q5 | Which Sayari tools will be available, and what are their names and fields? | Section 7 is written generically. Map operations once tools are visible. |
| Q6 | What is the demo's actual stack and structure? | Section 9 is a proposal and must be reconciled. |
| Q7 | Should `NAMED_ENTITIES` stay as a fourth mode? | Added here because the preset instruments are name-based. Confirm with the team. |
