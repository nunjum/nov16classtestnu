# SOURCES

Primary-source verification for Chinese optical transceiver and logic-bearing component restrictions (FCC, DoW, legislative, and reported/analyst items).

**Last-verified date for all items:** 2026-11-16, unless marked otherwise.

---

## A. FCC 26-50: Third Report and Order (Equipment Authorization Rule)

| Field | Value |
|---|---|
| **Evidence tier** | ENACTED_LAW |
| **Status** | VERIFIED |
| **Source type** | primary (FCC order) |
| **FCC Order URL** | https://docs.fcc.gov/public/attachments/FCC-26-50A1.pdf |
| **Federal Register notice** | 91 FR 57798–57801; Docket 2026-18535 |
| **FR URL** | https://www.govinfo.gov/content/pkg/FR-2026-09-11/pdf/2026-18535.pdf |
| **Adopted** | July 22, 2026 |
| **Released** | July 23, 2026 |
| **Published (Federal Register)** | September 11, 2026 |
| **Effective date** | October 13, 2026 (30 days after FR publication) |
| **CFR codification** | 47 CFR 2.902 (definitions added); 47 CFR 2.903 (prohibitions modified); 47 CFR 2.932, 2.1043, 2.1204 also modified; 47 CFR 1.50001 expanded |

**Confirmed scope and definition:**

- **Logic-bearing hardware component** (47 CFR 2.902): any device, system, module, sub-assembly, or integrated circuit generating and using timing signals at >9,000 pulses/second using digital techniques, or generating and using RF energy for data processing functions. Includes intentional radiators; excludes purely mechanical or structural parts, passive components without digital timing, power-only parts, bare motors.
- **Prospective only:** applies to new equipment authorizations filed on or after October 13, 2026. Previously authorized equipment may continue to be marketed, imported, and used under existing authorization. Applications pending before effective date are exempt unless amended to add, substitute, or change a logic-bearing component.
- **Hardware only (adopted rule):** the Final Rule covers only hardware components. 47 CFR 2.902 states this is "for now"; the FNPRM (91 FR 51139–51145, comment deadline Sept 8, reply Sept 21, 2026) proposes to extend the prohibition to software and firmware from Covered List entities. The FNPRM has not been adopted and remains PENDING_BILL status.
- **Covered List entities only:** ¶37 states the prohibitions apply only to "logic-bearing hardware components produced by an entity identified on the Covered List." Does NOT apply to entities that only produce production-location-based categories (routers, UAS, UAS critical components) unless that entity is otherwise separately identified on the Covered List.
- **"Produced by" includes subsidiaries and affiliates:** The order does not define the ownership threshold for subsidiaries and affiliates; this remains subject to future rulemaking and policy determination.

**Example noted in order:** ¶22 cites Hudson Institute commenters naming "optical transceivers" as a logic-bearing component example. The adopted definition does not name specific component types; whether a given optical transceiver meets the definition requires case-by-case analysis.

---

## B. DoW Section 1260H Designation (June 2026)

| Field | Value |
|---|---|
| **Evidence tier** | ENACTED_DESIGNATION |
| **Status** | VERIFIED (listing status as of June 10, 2026; entity count PARTIAL) |
| **Source type** | primary (Federal Register notice of Department designation) |
| **Federal Register notice** | 91 FR 35194+; "Notice of Availability of Designation of Chinese Military Companies" |
| **Document number** | 2026-11571 |
| **DoD docket** | DOD-2026-OS-1288 |
| **URL** | https://www.govinfo.gov/content/pkg/FR-2026-06-10/html/2026-11571.htm |
| **Published** | June 10, 2026 (filed June 8, 2026) |
| **Statutory basis** | Section 1260H of the William M. (Mac) Thornberry National Defense Authorization Act for Fiscal Year 2021 (Pub. L. 116-283) |

**Confirmed entities (four companies of interest):**

- **Zhongji Innolight Co., Ltd. (InnoLight):** ✓ Listed. Description: "InnoLight is indirectly owned by SASAC and directly affiliated with MIIT (Section 1260H(g)(2)(B)(i)(I))."
- **Eoptolink Technology Inc., Ltd.:** ✗ NOT listed in this June 2026 notice.
- **Dongshan Precision Manufacturing Co., Ltd.:** ✗ NOT listed in this June 2026 notice.
- **TFC:** ✗ NOT listed in this June 2026 notice.

**Entity count:** The notice does not provide a consolidated total. Approximately 108 primary entities are designated, with numerous additional subsidiaries and affiliated entities listed under parent companies (e.g., AVIC, CETC, State Grid). A precise total of 188 would require aggregating all parent entities and subsidiary entries throughout the full notice. Status: **PARTIAL** (count not independently verified against the full notice text).

**Section 805 contracting restriction:** The notice does not state an effective date for the contracting ban. The user brief specifies "Section 805 of the FY2024 NDAA bars DoW from contracting directly with 1260H entities from June 30, 2026." This statutory reference and date have not been verified against the FY2024 NDAA text. Status: **UNVERIFIED**.

**Separation from FCC Covered List:** A Section 1260H designation is a separate Department of War determination under a different statute. Nothing in the sources consulted shows this listing places an entity on the FCC Covered List. InnoLight is on the 1260H list but, per FCC 26-50 ¶ footnote 245, does not appear on the FCC Covered List (as of July 2026).

---

## C. FCC Covered List: Status of Four Companies

| Field | Value |
|---|---|
| **Evidence tier** | ENACTED_LAW (reference) |
| **Status** | PARTIAL |
| **Source type** | primary (FCC Covered List; point-in-time snapshot from DA 26-278) |

**Data points consulted:**

1. **Covered List as of March 23, 2026** (DA 26-278, Appendix A; https://docs.fcc.gov/public/attachments/DA-26-278A1.pdf):
   - Grep search for "InnoLight," "Zhongji," "Eoptolink," "Dongshan," "TFC": no matches found.
   - Confirmed entries: Huawei, ZTE, Hytera, Hikvision, Dahua, Kaspersky, China Mobile International USA, China Telecom (Americas), Pacific Networks/ComNet, China Unicom (Americas), foreign-produced UAS, UAS critical components, and foreign-produced routers.

2. **FCC 26-50 Footnote 245** (primary, adopted July 22, 2026):
   - Cites an industry list of optical transceiver market players including InnoLight.
   - States that none of the named "key players" "appear on the Covered List."
   - This is the FCC's own statement but covers only "key players"; does not explicitly name Eoptolink, Dongshan Precision, or TFC.

**Conclusion:** As of the most recent accessible point-in-time data (March 23 and July 22, 2026), InnoLight, Eoptolink, Dongshan Precision, and TFC do not appear on the Covered List. The live FCC Covered List page (https://www.fcc.gov/supplychain/coveredlist) could not be accessed; status after July 2026 is **UNVERIFIED**.

---

## D. Foreign-Produced Routers: DA 26-278

| Field | Value |
|---|---|
| **Evidence tier** | ENACTED_LAW (reference) |
| **Status** | VERIFIED |
| **Source type** | primary (FCC Public Safety and Homeland Security Bureau announcement) |
| **URL** | https://docs.fcc.gov/public/attachments/DA-26-278A1.pdf |
| **Released** | March 23, 2026 |
| **Docket** | WC 18-89, ET 21-232, EA 21-233 |

**Confirmed:**

- Routers produced in foreign countries were added to the FCC Covered List, with exemption for routers receiving a Conditional Approval from DoW or DHS.
- Conditional Approvals are valid for up to 18 months (Annex A).
- Annex A describes the application process; Appendix B lists four UAS approvals valid through December 31, 2026 (no router-specific approvals listed in this notice).

**Router approval expiration dates (2027–2028): UNVERIFIED.** The notice does not list router Conditional Approvals or expiry dates. FCC 26-50 ¶12 mentions that security-update waivers for previously authorized routers "now expire in 2029," but this is a different mechanism.

---

## E. Senate Bill S.5548: Optical Transceiver Procurement Restriction

| Field | Value |
|---|---|
| **Evidence tier** | PENDING_BILL |
| **Status** | VERIFIED (as introduced; subsequent status UNVERIFIED) |
| **Source type** | primary (official bill text) |
| **Bill URL** | https://www.govinfo.gov/content/pkg/BILLS-119s5548is/html/BILLS-119s5548is.htm |
| **Congress/Session** | 119th Congress, 2nd Session |
| **Introduced** | September 24, 2026 |
| **Referred to** | Senate Committee on Homeland Security and Governmental Affairs |
| **Official title** | "To prohibit Federal procurement of certain optical transceivers, and for other purposes." |
| **Short title** | "Securing National Security Systems from Chinese Optical Transceivers Act of 2026" |

**Sponsors:**
- Sen. J.D. McCormick (R, Ohio) — lead sponsor
- Sen. Mark Kelly (D, Arizona) — cosponsor [Note: user brief says Gallego; verify name]
- Sen. John Cornyn (R, Texas) — cosponsor
- Sen. John Fetterman (D, Pennsylvania) — cosponsor

**Mechanism and scope:**

- Amends Section 5949 of the James M. Inhofe National Defense Authorization Act for Fiscal Year 2023 (Pub. L. 117-263) to extend a semiconductor procurement ban to covered optical transceivers.
- **Federal procurement restriction only:** applies only to U.S. federal procurement for national security systems; does NOT limit commercial or civilian procurement.
- Transition period: five years after enactment.
- Defined as a "critical system" procurement restriction with limited exemptions for non-critical systems (new subsec. (v)).

**Named companies (Section 1(j)(3)(A)):**
- Zhongji Innolight Co., Ltd. (InnoLight)
- Eoptolink Technology Inc., Ltd. (Eoptolink)
- Includes subsidiaries, affiliates, and successors of the above.
- Additional entities may be added if the Defense or Commerce Secretary determines ties to a country of concern and publishes in the Federal Register.
- Covers transceivers with firmware, software, or components from these entities.

**Commerce assessment:** The bill requires the Commerce Secretary to assess U.S. and allied production capacity for covered optical transceivers within three years of enactment.

**Post-introduction status (congress.gov): UNVERIFIED.** Congressional action after introduction, votes, amendments, or enactment status are blocked from automated access. Status must be checked at https://www.congress.gov/bill/119th-congress/senate-bill/5548.

---

## F. Reuters: Reported FCC Draft on Chinese Optical Transceivers

| Field | Value |
|---|---|
| **Evidence tier** | REPORTED_DRAFT |
| **Status** | UNVERIFIED |
| **Source type** | Trade press (Reuters) |
| **Article date** | August 4, 2026 (reported) |
| **URL** | Not located |

**Reported claims (from user brief; not independently confirmed):**

- FCC is drafting restrictions on new Chinese optical transceiver models.
- Phase-in starting at the 3.2T generation, ramping ~2028–2029.
- Possible exemption if ≥65% of bill-of-materials (BOM) value is U.S.-sourced.
- InnoLight, Eoptolink, Dongshan Precision, and TFC are explicitly mentioned.
- Note: these four companies do NOT appear on the FCC Covered List (confirmed in item C above).

**Verification attempt:** Reuters.com is blocked from automated web access. No FCC public document confirms a draft transceiver-model ban (FCC 26-50 does not propose one; the FNPRM proposes extending the component rule to software/firmware, not introducing a model ban). Status remains **UNVERIFIED**. Source URL is needed to validate.

**Microsoft lobbying report (Nov 2026): UNVERIFIED.** No primary source located.

---

## G. Analyst Estimates

| Field | Value |
|---|---|
| **Evidence tier** | ANALYST_ESTIMATE |
| **Status** | UNVERIFIED |
| **Source type** | Analyst (market research firms) |

**Counterpoint Research:**
- InnoLight: ~27% of datacom transceiver market revenue (global) — **UNVERIFIED**
- InnoLight and Eoptolink combined: ~60% of NVIDIA 800G optical transceiver volumes — **UNVERIFIED**
- DSPs sourced from Broadcom/Marvell — **UNVERIFIED**
- Thailand manufacturing lines may fall outside point-of-manufacture rules — **UNVERIFIED**

**Cignal AI:**
- InnoLight: $2.6B of $7.7B in global optical transceiver revenue (1Q26) — approximately 34% — **UNVERIFIED**

**Methodology note:** Counterpoint estimate (27%) and Cignal estimate (34%) diverge significantly. The difference may reflect definitional choices (datacom only vs. all transceivers, revenue vs. units, different time periods). These estimates are adjustable inputs and should be verified against the original analyst reports.

**Sources:**
- Counterpoint Research: direct access to reports requires subscription; not independently verified here.
- Cignal AI: direct access to reports requires subscription; not independently verified here.

---

## OPEN QUESTIONS

(Do not answer from memory; verify against primary sources.)

1. **FCC equipment authorization scope for optical transceivers:**
   - Do pluggable optical transceivers require FCC equipment authorization under current regulations?
   - What constitutes a "new model" under 47 CFR Part 2?

2. **Other regulatory lists:**
   - Are InnoLight, Eoptolink, Dongshan Precision, or TFC on any of the following lists as of 2026-11-16?
     - Commerce Department Entity List
     - Treasury OFAC Specially Designated Nationals (SDN) List
     - NSF National Security Sensitive Research and Development (NS-CMIC) list

3. **Component rule cascade:**
   - If InnoLight were added to the FCC Covered List, would the prohibition in 47 CFR 2.903 reach a Western-made switch that incorporates an InnoLight optical transceiver module?
   - Would the transceiver be treated as a "logic-bearing hardware component produced by" InnoLight, or would it be "a device incorporating a component produced by" InnoLight (broader reading)?

4. **Subsidiary and affiliate threshold:**
   - What ownership percentage or control standard does the FCC use to determine whether a subsidiary or affiliate is "produced by" a Covered List entity under 47 CFR 2.902?
   - Has the FCC issued guidance or case-law precedent on this point?

---

## SUMMARY OF VERIFICATION

| Item | Tier | Status | Notes |
|---|---|---|---|
| **A. FCC 26-50 order and final rule** | ENACTED_LAW | VERIFIED | Adopted July 22, 2026; effective Oct 13, 2026; 47 CFR 2.902/2.903; hardware only; prospective; Covered List entities only. |
| **B. DoW Section 1260H (June 2026)** | ENACTED_DESIGNATION | VERIFIED | Zhongji InnoLight listed; Eoptolink, Dongshan, TFC NOT listed. Entity count ~108 primary (PARTIAL); June 8/10, 2026. |
| **C. FCC Covered List (four companies)** | ENACTED_LAW | PARTIAL | InnoLight, Eoptolink, Dongshan, TFC absent as of March 23 and July 22, 2026 snapshots. Live list (Nov 2026) UNVERIFIED. |
| **D. Foreign-produced routers (DA 26-278)** | ENACTED_LAW | VERIFIED | March 23, 2026; added to Covered List; 18-month Conditional Approval terms. Approval expiry dates (2027–28) UNVERIFIED. |
| **E. S.5548 optical transceiver bill** | PENDING_BILL | VERIFIED | Introduced Sept 24, 2026; McCormick, Cornyn, Fetterman, Gallego [check name]; federal NSS procurement restriction; five-year transition. Post-introduction status UNVERIFIED. |
| **F. Reuters (Aug 4 & Nov 11, 2026)** | REPORTED_DRAFT | UNVERIFIED | 3.2T transceiver model ban, 65% US BOM exemption, Microsoft lobbying — no primary source located; Reuters.com blocked. |
| **G. Analyst estimates** | ANALYST_ESTIMATE | UNVERIFIED | Counterpoint 27% (InnoLight datacom), Cignal 34% (InnoLight global optics 1Q26); sources require subscription access. |

**One-line summary:** FCC 26-50 and DoW 1260H designations are VERIFIED as of their published dates; S.5548 bill text verified; Reuters reports, analyst estimates, and future regulatory action remain UNVERIFIED or blocked from access.
