# SOURCES

Primary-source check for the "Next-Gen Optics Restriction" preset (see `CONCEPT-IDEA.md`).

- **Last-verified date for everything below:** 2026-11-16, unless an item says otherwise.
- **How I checked:** I fetched documents from `docs.fcc.gov`, `govinfo.gov`, the Federal Register API and `mccormick.senate.gov`. I read the two FCC PDFs as extracted text. `fcc.gov`, `congress.gov` and the `federalregister.gov` web pages blocked automated access, and `reuters.com` is blocked in this tool. I did not work around those blocks.
- **Search limits:** I had no web search tool. I could only fetch URLs I already knew or could derive. "UNVERIFIED" below means "not found with these means," not "does not exist."
- **Source types:** primary / law-firm / trade press / analyst. Every source actually used here is primary except the Reuters item, which is unverified.

---

## 1. FCC 26-50 (ET Docket No. 21-232): Third Report and Order and Third FNPRM

| Field | Value |
|---|---|
| Source | FCC-26-50, Third Report and Order and Third Further Notice of Proposed Rulemaking |
| URL | https://docs.fcc.gov/public/attachments/FCC-26-50A1.pdf |
| Type | primary (FCC order) |
| Last verified | 2026-11-16 |
| Adopted / released | Adopted July 22, 2026; released July 23, 2026 (cover page) |

**Federal Register notices (both primary, read via the Federal Register JSON API because the web pages redirect to a bot check):**

| Notice | Citation | Published | Key date | URL |
|---|---|---|---|---|
| Final rule (Third Report and Order) | 91 FR 57798-57801, doc. 2026-18535 | 2026-09-11 | **Effective October 13, 2026** ("This final rule is effective October 13, 2026.") | https://www.federalregister.gov/api/v1/documents/2026-18535.json (PDF: https://www.govinfo.gov/content/pkg/FR-2026-09-11/pdf/2026-18535.pdf) |
| Proposed rule (Third FNPRM) | 91 FR 51139-51145, doc. 2026-16197 | 2026-08-07 | Comments due Sep 8, 2026; reply comments due Sep 21, 2026 | https://www.federalregister.gov/api/v1/documents/2026-16197.json (PDF: https://www.govinfo.gov/content/pkg/FR-2026-08-07/pdf/2026-16197.pdf) |

**Confirmed from the order text:**

- **Effective date.** ¶38 and ¶245 say the rule is effective 30 days after Federal Register publication. The Federal Register notice gives October 13, 2026. The two are consistent.
- **Scope: new authorizations only.** ¶38: "this prohibition is effective 30 days after publication in the Federal Register, as the change only impacts new equipment authorizations." Previously authorized equipment "may continue to be marketed, imported, and used pursuant to its existing authorization." Applications pending before the effective date are exempt (the passage around ¶38).
- **Scope: hardware only.** ¶30: "For now, we limit our rule to hardware components, though we keep the record open on software and firmware components."
- **Scope: Covered List entities only.** ¶37: the prohibitions "apply only for logic-bearing hardware components produced by an entity identified on the Covered List." They do not apply to components from entities that only produce the production-location-based categories (UAS, UAS critical components, routers) "unless that entity is otherwise identified on the Covered List."
- **Definition (¶28; new 47 CFR 2.902).** The order defines logic-bearing hardware components as "any device, system, module, sub-assembly, integrated circuit, or other physical component that generates and uses timing signals or pulses at a rate in excess of 9,000 pulses (cycles) per second and uses digital techniques; inclusive of telephone equipment that uses digital techniques or any device, system, module, sub-assembly, integrated circuit, or other physical component that generates and uses radio frequency energy for the purpose of performing data processing functions". It is based on the existing "digital device" definition in 47 CFR 15.3(k), except that it does not exclude intentional radiators. ¶28 lists non-logic-bearing examples: purely mechanical or structural parts, passive parts without digital timing signals, power-only parts, and a bare motor.
- **Optical transceivers.** ¶22 quotes Hudson Institute commenters who list "optical transceivers" as an example of logic-bearing components. The adopted definition does not name specific component types. Whether a given transceiver meets the definition is not settled by the order text.

**What the FNPRM proposes beyond the adopted rule (¶¶128-234):**

- Splitting the Covered List into producer/provider-based and production-location-based categories (¶129).
- White labeling (¶130).
- Hardware and software bill of materials disclosures (¶¶139-146). ¶145 asks whether to limit these to logic-bearing components or to listed components "such as modular transmitters and IoT modules, semiconductors, and optical transceivers."
- Banning authorization of devices with *any* component from a Covered List entity (¶148), or a rebuttable presumption against such authorization (¶149).
- Extending this to software and firmware from Covered List entities (¶¶150-152).
- Importation and marketing limits, streamlined revocation, term limits on equipment authorizations, SDoC registration, and a U.S.-based liable party requirement (¶¶159-234).
- Definitions of "produced in a foreign country," "UAS critical components" and "routers" (¶¶197-207).

**Not verified:** I did not read every paragraph of the 108-page order. The paragraph numbers above are from the table of contents and the paragraphs I read. Check the order before quoting a paragraph in public. The Federal Register notice abstracts use the same headings but do not contain the order's full text.

---

## 2. FCC Covered List

| Field | Value |
|---|---|
| Source | FCC Covered List page |
| URL | https://www.fcc.gov/supplychain/coveredlist |
| Type | primary |
| Live page | **UNVERIFIED.** The page returned HTTP 403 to automated fetches, so I could not read the current list. |

**Status for the four named companies: UNVERIFIED as of today.** There are two point-in-time sources. Neither is the live list.

1. **Covered List, updated March 23, 2026** (Appendix A of DA 26-278, https://docs.fcc.gov/public/attachments/DA-26-278A1.pdf, primary). A text search for InnoLight, Zhongji, Eoptolink, Dongshan and TFC found no matches. The entries are Huawei, ZTE, Hytera, Hikvision, Dahua, Kaspersky, China Mobile International USA, China Telecom (Americas), Pacific Networks/ComNet, China Unicom (Americas), foreign-produced UAS and UAS critical components, and foreign-produced routers. The names above are my reading of the extracted text. I read only part of Appendix A's entries, and the grep found nothing for the four names. This evidence is current only to March 23, 2026, and the list may have changed since.
2. **FCC 26-50 footnote 245** (primary, adopted July 22, 2026). It cites an industry listing of optical transceiver market players that includes InnoLight, and states that none "appear on the Covered List." This is the FCC's own statement, but it covers "key players" and does not name Eoptolink, Dongshan Precision or TFC.

**Other things worth knowing:**

- Dongshan Precision and TFC are checked only against the March 23 list.
- Being absent from the Covered List is separate from being on the DoW 1260H list (item 5). InnoLight is on 1260H but, per the sources above, not on the Covered List. That distinction matters because the FCC 26-50 component rule applies only to Covered List entities (¶37).
- Result for the preset: the FCC 26-50 component rule, as written, would not reach these four companies' components unless they are added to the Covered List. That is my reading of ¶37 plus the absence finding above. It is not something the FCC states about these companies.

---

## 3. Router action DA 26-278 and the power inverter action

### Router action

| Field | Value |
|---|---|
| Source | DA 26-278, "FCC's Public Safety and Homeland Security Bureau Announces Addition of Routers Produced in Foreign Countries to FCC Covered List" |
| URL | https://docs.fcc.gov/public/attachments/DA-26-278A1.pdf |
| Type | primary |
| Last verified | 2026-11-16 |
| Released | March 23, 2026 (WC Docket 18-89, ET Docket 21-232, EA Docket 21-233) |

**Confirmed:**

- The Bureau added "Routers produced in a foreign country, except routers which have been granted a Conditional Approval by DoW or DHS" to the Covered List, dated March 23, 2026.
- The basis is a National Security Determination received by the FCC on March 20, 2026 (Appendix C).
- Exemption path: DoW or DHS may send the FCC a determination that a given router or class of routers does not pose the risks. Annex A describes the Conditional Approval application process.
- Annex A says "Conditional Approvals will be granted for periods of up to 18 months."

**The "some router approvals expire 2027-2028" claim: UNVERIFIED.**

- This notice lists no router Conditional Approvals. Its Appendix B (dated March 18, 2026) lists only four UAS approvals, valid "March 17, 2026-December 31, 2026."
- The only 2027 date in the notice is January 1, 2027, for Blue UAS-listed UAS. That is not a router date.
- The 18-month maximum would put an approval granted after March 2026 near or after late 2027, but I found no actual approval or expiry date. Keep the preset's 2027-2028 note marked unverified until a router approval list is found.
- Separately, FCC 26-50 ¶12 says waivers allowing permissive changes (such as security updates) to previously authorized UAS and routers "now expire in 2029." That is a different mechanism from Conditional Approvals.

### Power inverter action

**UNVERIFIED. I did not find it.**

- DA 26-278 does not mention inverters.
- A keyword search of FCC 26-50 for "inverter" found nothing.
- A Federal Register API search for FCC documents mentioning "Covered List" from 2026-03-24 onward returned 24 documents. I saw 20 on page 1 and none was an inverter action. A separate search for "inverters" with the Covered List and FCC filters returned zero results.
- The FCC page that would list it (item 2) is blocked to me.
- Possibly related but not shown to be: a Department of Energy request for information on foreign-made bulk-power system equipment (Federal Register doc. 2026-18370, published 2026-09-09, supporting an August 26, 2026 executive order). The excerpt I saw does not mention inverters, so I make no claim that it is the inverter action.
- The "Jul 2026" date and the "DoW/DHS approval" condition in the preset brief are **unconfirmed**. Please supply the document you have in mind, or check the Covered List page directly.

---

## 4. S. 5548

| Field | Value |
|---|---|
| Source (bill text) | S. 5548, 119th Congress, 2d Session, as introduced (govinfo; congress.gov blocked automated access, HTTP 403) |
| URL | https://www.govinfo.gov/content/pkg/BILLS-119s5548is/html/BILLS-119s5548is.htm |
| Type | primary (official bill text) |
| Last verified | 2026-11-16 |
| congress.gov page | https://www.congress.gov/bill/119th-congress/senate-bill/5548 (**not read**; latest action and status beyond introduction are therefore UNVERIFIED) |

**Confirmed from the text as introduced:**

- Official title: "To prohibit Federal procurement of certain optical transceivers, and for other purposes." Short title (Sec. 1): "Securing National Security Systems from Chinese Optical Transceivers Act of 2026."
- Sponsor: Sen. McCormick. Cosponsors: Sens. Gallego, Cornyn, Fetterman. Introduced September 24, 2026. Referred to the Homeland Security and Governmental Affairs Committee.
- Mechanism: it amends Section 5949 of the FY2023 NDAA (Pub. L. 117-263) to extend that procurement ban from semiconductors to "covered optical transceivers." The ban does not limit procurement for "a system that is not a critical system" (new clause (v)).
- Named companies (new subsec. (j)(3)(A)): "Zhongji Innolight Co., Ltd. (InnoLight)" and "Eoptolink Technology Inc., Ltd. (Eoptolink)," with their subsidiaries, affiliates and successors. Other entities are covered if the Defense or Commerce Secretary determines they are tied to a country of concern and publishes that in the Federal Register. Transceivers with firmware, software or components from these entities are also covered.
- Transition: the ban takes effect "five years after the date of the enactment" (new subsec. (c)(1)(B)).

**Not verified:** the enactment status after introduction. As of the text I read, it is an introduced bill and must be labeled `PROPOSED_BILL`. The bill is a **federal procurement** restriction. It is not a consumer or general-market ban, and the preset should say so.

### Sponsors' press release

| Field | Value |
|---|---|
| Source | Sen. McCormick's office, "Senators McCormick, Gallego, Cornyn, Fetterman Introduce Bill to Keep Chinese Transceivers Out of U.S. National Security Systems" |
| URL | https://www.mccormick.senate.gov/news/press-releases/senators-mccormick-gallego-cornyn-fetterman-introduce-bill-to-keep-chinese-transceivers-out-of-u-s-national-security-systems/ |
| Type | primary (sponsor press release, advocacy in nature) |
| Date on release | September 25, 2026 |
| Last verified | 2026-11-16 |

It confirms the bill name, the four senators, InnoLight and Eoptolink, the five-year transition, and federal procurement for national security systems. It also mentions Commerce assessment of US and allied production capacity, and limited renewable waivers. The release text I saw does not give the bill number. The release is dated one day after the bill text's September 24 introduction date, which I did not reconcile. The release says the Department of War added InnoLight to its 1260H list in June (see item 5). Part of the page was cut off, so details after the endorsements are unread.

---

## 5. DoW Section 1260H listing for InnoLight (June 2026)

| Field | Value |
|---|---|
| Source | Federal Register, "Notice of Availability of Designation of Chinese Military Companies," 91 FR (Vol. 91, No. 111), p. 35194 for the entry |
| URL | https://www.govinfo.gov/content/pkg/FR-2026-06-10/html/2026-11571.htm |
| Type | primary (Federal Register notice of a Department determination) |
| Doc. no. / docket | 2026-11571 / DOD-2026-OS-1288 |
| Published | June 10, 2026 (signed June 5, filed June 8) |
| Last verified | 2026-11-16 |

**Confirmed:** the notice has an entry for "Zhongji Innolight Co., Ltd. (Innolight)." The entry reads: "InnoLight is indirectly owned by SASAC and directly affiliated with MIIT (Section 1260H(g)(2)(B)(i)(I))." Statutory basis: Section 1260H of the FY2021 NDAA (Pub. L. 116-283). The notice states no separate effective date. The entry text above comes from my page-level read of the notice, so quote it from the source before relying on the exact wording.

**Also checked:**

- Eoptolink, Dongshan and TFC did not appear in this notice's text.
- A later notice, "Notice of Removal of Designated Chinese Military Company" (doc. 2026-22840, published 2026-11-10), removes only "WuXi AppTec Co., Ltd." after a court order. InnoLight is not mentioned, so InnoLight's listing was not removed by that notice. I did not search for any other later notice.

**Not the same as the Covered List.** A 1260H listing is a Department designation under a different statute (FY2021 NDAA Section 1260H). Nothing I read shows it placing a company on the FCC Covered List. Its legal consequences are not covered here.

---

## 6. Reuters, August 4, 2026: reported FCC draft on Chinese transceiver models

| Field | Value |
|---|---|
| Tier | `REPORTED_DRAFT` |
| Status | **UNVERIFIED** |
| Source type | Not established. Reuters would be news reporting (closest to trade press). It is not a primary source. |
| URL | None found |
| Last verified | 2026-11-16 (attempted; nothing confirmed) |

- `reuters.com` is blocked for this tool, and I had no search to locate the article. I did not read it, so I cannot confirm the headline, byline, date, the 3.2T reference, the ramp timing of about 2028-29, or the possible 65% US-sourced BOM exemption. Those details come from your brief only.
- No primary source exists to check against. I found no FCC document announcing a ban on new Chinese transceiver models. FCC 26-50 mentions optical transceivers only in ¶22, ¶145 and footnote 245, and none of those is such a ban. The FCC has not confirmed the report, per your brief.
- The preset should keep the label: "Reported by Reuters (Aug 4, 2026); FCC has not confirmed." Add the URL once you have it.

---

## Summary of verification status

| # | Item | Status |
|---|---|---|
| 1 | FCC 26-50 order and Federal Register notices | Verified (effective Oct 13, 2026; definition and FNPRM scope quoted) |
| 2 | Covered List (InnoLight, Eoptolink, Dongshan, TFC absent) | **UNVERIFIED for the live list.** Absent as of the March 23, 2026 list; FCC 26-50 fn. 245 supports absence for InnoLight. |
| 3a | Router action DA 26-278 | Verified. The "2027-2028 approval expiry" claim is **UNVERIFIED**. |
| 3b | Power inverter action | **UNVERIFIED. Not found.** |
| 4 | S.5548 bill text and sponsors' press release | Verified (as introduced); congress.gov status page not read |
| 5 | DoW 1260H listing for InnoLight (June 10, 2026) | Verified; not removed by the Nov 10 notice |
| 6 | Reuters Aug 4, 2026 report | **UNVERIFIED** (`REPORTED_DRAFT`) |
