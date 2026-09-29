# Requirements: Insurance Plan RAG Query Engine (v1)

Status: broad requirements locked. The design and task breakdown will be regenerated from this document.

## 1. Problem statement
POSP agents, whose familiarity with health and life products varies, spend too long finding and comparing plan facts across insurer brochures, and risk mis-stating those facts to customers.

## 2. Users and moments of use
**Primary user:** POSP agents and partners, with a mix of experience in health and life products.

**Moments of use, in v1 priority order:**
1. Picking which plan to pitch
2. Preparing before a pitch
3. Live, in the middle of a customer conversation
4. Handling a customer's follow-up or objection later

## 3. Scope
**In scope for v1**
- Questions about a single plan
- Questions across plans: "which plans cover X?"
- Differences between named plans: "what's different between A and B?"
- Side-by-side comparison of 2–3 named plans
- Health and life brochures

**Out of scope for v1**
- Suitability checks, customer-profile matching, and recommendations
- Any source other than insurer brochures, including policy wordings, CIS, prospectus, and general knowledge
- Premium quotes or estimates
- Older brochure versions

## 4. Functional requirements
These use the EARS format: WHEN / IF / THE SYSTEM SHALL.

### R1. Question types
- **R1.1** WHEN an agent asks about a named plan, THE SYSTEM SHALL answer from that plan's brochure.
- **R1.2** WHEN an agent asks which plans cover or offer something, THE SYSTEM SHALL check every uploaded plan, optionally narrowed by line of business or insurer, and SHALL list the plans whose brochure states it.
- **R1.3** WHEN an agent asks what differs between named plans, THE SYSTEM SHALL list the factual differences and SHALL NOT declare a better plan.
- **R1.4** WHEN an agent requests a side-by-side of 2–3 named plans, THE SYSTEM SHALL present the same attributes for every plan (see Open item O1).
- **R1.5** IF a question asks for a recommendation or suitability judgement (for example, "which plan is best for my customer?"), THE SYSTEM SHALL decline and offer the factual question it can answer instead.

### R2. Grounding and honesty
- **R2.1** THE SYSTEM SHALL answer only from the content of uploaded brochures.
- **R2.2** THE SYSTEM SHALL cite the plan and page number for every fact it states.
- **R2.3** IF a brochure does not mention something, THE SYSTEM SHALL say the brochure does not mention it, and SHALL NOT state or imply that it is not covered.
- **R2.4** In any comparison, IF a brochure does not state a value, THE SYSTEM SHALL show "not stated" and SHALL NOT infer the value.
- **R2.5** THE SYSTEM SHALL NOT combine facts from different plans into a single statement.
- **R2.6** IF the system is not confident an answer is supported by the brochure, THE SYSTEM SHALL refuse rather than give a best-effort answer.
- **R2.7** WHEN refusing, THE SYSTEM SHALL name the closest related content the brochure does contain, with its page, when such content exists.
- **R2.8** THE SYSTEM SHALL use plain language while keeping the brochure's exact figures, limits, and defined terms.

### R3. Source of truth and versioning
- **R3.1** Insurer brochures SHALL be the only source of truth.
- **R3.2** WHEN a new version of a plan's brochure is uploaded, THE SYSTEM SHALL replace the previous version, and only the latest version SHALL be used to answer. No answer SHALL mix content from two versions.
- **R3.3** Every answer SHALL show the version and date of each brochure it cites (see Open item O2).

### R4. Corpus scope
- **R4.1** Cross-plan answers SHALL state the set they searched, for example "among the 6 uploaded health plans".
- **R4.2** IF an agent asks about a plan that has not been uploaded, THE SYSTEM SHALL say it has no brochure for that plan, and SHALL NOT answer from any other source.

### R5. Neutrality
- **R5.1** THE SYSTEM SHALL NOT rate, rank, or score plans or insurers.
- **R5.2** Lists of plans SHALL appear in a neutral order, such as alphabetical, that does not suggest a preference.

### R6. Prohibited content
THE SYSTEM SHALL NOT do any of the following:
- **R6.1** Quote or estimate premiums, including sample premiums or benefit illustrations printed in brochures.
- **R6.2** Promise, predict, or imply claim outcomes.
- **R6.3** Answer from general knowledge. This includes explaining an insurance term the brochure does not define.

### R7. Terms and definitions
- **R7.1** WHEN an agent asks what a term means for a plan, and that plan's brochure defines the term, THE SYSTEM SHALL give the brochure's definition with a citation.
- **R7.2** IF the brochure does not define the term, THE SYSTEM SHALL say so (per R2.3 and R6.3).

### R8. Answer shape
- **R8.1** Every answer SHALL lead with a direct answer that can be read in a few seconds, followed by the supporting detail. This serves both live use and prep.

### R9. Brochure management
- **R9.1** An authorised user SHALL be able to upload, replace, list, and delete plan brochures.
- **R9.2** Each plan SHALL be identified by an ID supplied by the host app.

### R10. Integration
- **R10.1** THE SYSTEM SHALL be consumed by the existing host app through an API.

## 5. Non-functional requirements
- **NFR1 Accuracy first:** whenever accuracy and coverage conflict, accuracy wins.
- **NFR2 Speed:** answers SHALL be fast enough for use during a live conversation. Proposed target: p95 under 4 seconds for a single-plan question.
- **NFR3 Cost:** the system SHALL run at the lowest workable cost, with no paid fixed infrastructure in v1.
- **NFR4 Auditability:** THE SYSTEM SHALL log every question with its answer, the sources cited, and the brochure versions used.
- **NFR5 Environment:** the system SHALL be developable and runnable in GitHub Codespaces.
- **NFR6 Swappability:** the model and storage providers SHALL be replaceable without rewriting the system.

## 6. Success measures, in priority order
Targets are proposed and will be confirmed before evaluation.

| # | Measure | Proposed target |
|---|---|---|
| 1 | Accuracy: stated facts that are correct and correctly cited | ≥ 98% |
| 1a | "Not mentioned" is never phrased as "not covered" | 100% |
| 1b | Correct refusal on questions the brochures cannot answer | ≥ 90% |
| 2 | Time saved: a question answered faster than finding it manually in the brochure | Measured in pilot |
| 3 | Adoption: agents using it repeatedly | Measured in pilot |
| 4 | Pitch and conversion outcomes | Observed, not a v1 gate |

## 7. Open items
- **O1:** The attribute set for side-by-side comparisons. Options are a fixed list per line of business, agent-chosen attributes, or both.
- **O2:** Where the brochure version and date come from: entered at upload, read from the brochure itself (UIN or date in the footer), or both.
- **O3:** The v1 pilot corpus, meaning which plans and insurers get uploaded first.
- **O4:** Confirmation of the targets in section 6.