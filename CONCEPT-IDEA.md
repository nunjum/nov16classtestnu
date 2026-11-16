# Concept: Proposed Regulation Simulation

## Summary

A "Proposed Regulation Simulation" section lets a user inject a scenario built from new intelligence about a potential regulation. The section then shows the modeled impact on supply chains and data center design. It ships with placeholder data. Students later replace that data with their own Sayari analysis of real parts.

The section models consequences. It does not predict whether a regulation will happen.

## Why anticipatory analysis matters

- Data center hardware has long lead times. Design, qualification and procurement decisions are made years before a transceiver ramps. A rule that takes effect after those decisions are locked in is expensive to absorb.
- Regulation usually shows up first as a bill, a press report or a draft. By the time it is in force, the options for responding are narrower.
- Supply chains are rarely visible beyond tier 1. Anticipating exposure means knowing who makes a part, who owns the manufacturer, and where else the part could be sourced.
- Scenario work builds the habit of asking "what if" before a rule exists. It keeps that habit separate from claiming that a rule exists.

## Evidence-tier principle

Every item in a scenario carries exactly one tier. The tier is shown wherever the item appears: in inputs, in outputs, and in anything exported.

| Tier | Meaning | How it may be presented |
|---|---|---|
| `IN_FORCE` | Enacted or adopted, and currently applicable | As current requirement, with scope limits stated |
| `PROPOSED_BILL` | Introduced legislation that has not been enacted | As "proposed", never as law |
| `REPORTED_DRAFT` | Reported by press or sources, and not confirmed by the issuing body | As "reported", with the outlet and a note that it is unconfirmed |
| `HYPOTHETICAL` | A user-invented or exploratory assumption | As an assumption, labeled as such |

Rules:

1. **Never present reported or proposed items as law.** No wording, color, icon or default sort order may make a `PROPOSED_BILL` or `REPORTED_DRAFT` item look like an `IN_FORCE` one.
2. **Tier travels with the item.** Any modeled output shows the tiers of the inputs behind it. An impact driven by a `REPORTED_DRAFT` input is labeled as such, even when other inputs are `IN_FORCE`.
3. **Outputs are never stronger than inputs.** Combining tiers yields the weakest tier among the contributing items, not an average.
4. **Unverified details are flagged.** Where a fact is not verified, the field carries an explicit `unverified` marker, separate from the tier.
5. **No promotion without evidence.** Moving an item to a stronger tier requires a cited primary source (a Federal Register notice, a bill text, an agency statement).

## Student workflow

1. **Pick parts.** Choose real parts or part families that matter to the scenario, for example optical transceivers in a given speed generation.
2. **Analyze with Sayari.** Use Sayari to establish who makes each part, who owns and controls the manufacturer, and what the relevant sourcing alternatives are. Students run this analysis themselves and record sources.
3. **Map findings to the scenario.** For each regulation item, decide whether the analyzed entities and parts fall in scope. Record the reasoning, not only the result.
4. **Replace the placeholder data.** Plug the Sayari-derived results into the scenario's supplier, part and exposure fields. Keep a note of what was replaced and what remains placeholder.
5. **Assign tiers and confidence.** Tag each regulation item with its tier, and tag each data point as verified or unverified.
6. **Run and interrogate.** Run the simulation, change one assumption at a time, and write up which conclusions survive and which depend on weak-tier inputs.

Placeholder data in the shipped preset is labeled as placeholder everywhere it is shown. It must not be read as findings about real companies.

## Demo preset: "Next-Gen Optics Restriction"

All dates and details are as supplied for this concept. They have not been independently checked here and should be confirmed against primary sources before anything is presented to an audience.

| # | Tier | Item | Notes |
|---|---|---|---|
| 1 | `IN_FORCE` | FCC 26-50 component rule | New authorizations only. Hardware only. Covers logic-bearing components from Covered List entities. |
| 2 | `IN_FORCE` | Routers (Mar 2026) and power inverters (Jul 2026) covered unless approved by DoW/DHS | Some router approvals expire 2027-2028. **Mark as unverified.** |
| 3 | `PROPOSED_BILL` | S.5548 | Applies to federal national-security systems. Names InnoLight and Eoptolink. 5-year transition. Introduced Sep 24, 2026. Not enacted. |
| 4 | `REPORTED_DRAFT` | FCC ban on new Chinese transceiver models from the 3.2T generation | Ramp about 2028-29. Possible exemption for a BOM that is at least 65% US-sourced. Reported by Reuters. The FCC has not confirmed it. |

Preset handling notes:

- Items 1 and 2 are the only ones that may be described as current rules, and only within their stated scope (for example, item 1 does not reach existing authorizations or software).
- Item 3 must always read as a bill. Its scope (federal national-security systems) is narrower than a general market restriction, and the UI should say so.
- Item 4 must always read as a press report. The 65% exemption is a possibility within an unconfirmed report, so it is doubly conditional. Model it as a toggle, not a default.
- A scenario that stacks all four items is a `HYPOTHETICAL` composite. It is not a forecast of what will be enforced.

## Five example learning questions

1. Which of the four preset items would change what a data center operator may buy today, and which only matter if something is later enacted or confirmed? How does the tier help you tell?
2. For a transceiver you analyzed in Sayari, what does the ownership chain say about whether the manufacturer could be a Covered List entity or a named company? What can't the ownership data tell you?
3. If the reported 3.2T restriction applied only to new models, how would that change the timeline for a design that is qualified on today's models? What would you need to know about the part roadmap to answer?
4. Suppose a US-sourced BOM exemption at 65% were adopted. What data would you need to estimate a real part's US-sourced share, and how much of your estimate would rest on assumptions?
5. Two scenarios give the same modeled impact, but one rests on `IN_FORCE` items and the other on a `REPORTED_DRAFT`. How should a decision-maker treat them differently, and how should you word each in a briefing?

## Risks of over-claiming

- **Tier laundering.** A reported item gets cited in a summary and loses its "reported" label. Mitigation: tier is part of the data, and exports carry it.
- **Scope creep.** A rule limited to new authorizations and hardware is described as a general ban. Mitigation: show scope limits next to each item.
- **Naming companies.** Placing a real company in a scenario can read as an accusation. Mitigation: label placeholders, cite sources for real entities, and state that being modeled is not a finding of wrongdoing or of being covered.
- **False precision.** Modeled numbers look authoritative. Mitigation: show ranges or assumptions, and flag which inputs are weak-tier.
- **Treating absence as evidence.** No Sayari hit does not mean no exposure. Mitigation: record coverage limits in the student write-up.
- **Stale facts.** Regulatory status changes quickly. Mitigation: every item shows an "as of" date and a source, and the preset is reviewed before each use.
- **Unverified details presented as settled.** The router approval expirations are one example. Mitigation: use the `unverified` marker, and don't remove it without a source.

## How it fits the existing demo

At the time of writing, the project directory is empty. There is no existing demo, stack or scenario code that I could inspect. Because of that, this section is a statement of intent, not a description of current code. It should be revised once the demo exists.

Intended fit:

- The simulation is an additional section alongside the demo's existing views, not a replacement for any of them.
- It reuses whatever part, supplier and data center design data the demo already has. It layers scenario inputs on top, so the baseline view stays unchanged when no scenario is active.
- The preset is a default scenario that students overwrite. Placeholder data and Sayari-derived data are kept distinguishable.
- Evidence tiers are a first-class field in the scenario data, so any view that shows scenario output can show the tier too.

Open questions for the team:

- Where will the demo's existing supply chain and data center design data live, and in what shape?
- How will Sayari results be brought in: manual entry, file import, or something else?
- Who reviews the preset's facts before each class?
