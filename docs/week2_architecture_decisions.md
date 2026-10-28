**Architecture Decision Record (ADR)**

| Decision | Requirement / Constraint | Alternatives Considered | Selected Option | Trade-off Accepted |
| :---- | :---- | :---- | :---- | :---- |
| Include knowledge access | Answers must be grounded in the approved BNM, FEN, and AKPK documents, with source references and dates (FR1, FR2, FR3, NFR2, RAI1, RAI4) | Base model only; put all documents into every request; dynamic knowledge access over the approved documents | Dedicated knowledge access component with a separate, dated knowledge source | More system complexity, and retrieval may miss a relevant passage, but better grounding, traceability, and easier document updates (NFR5) |
| Include a calculation service | Calculation results must match reference values within RM 0.01 (FR4, NFR3) | Language model does the arithmetic; dedicated calculation service | Separate calculation service. The language model only explains the result. | Only a fixed set of calculation types is supported, and the user's numbers must be extracted correctly, but results are exact and testable |
| Include scope / advice control | Personalised-advice and out-of-scope requests must be refused, because personalised advice is a regulated activity (FR5, RAI2) | Instruction in the model prompt only; explicit scope / advice control component | Explicit control that classifies each request before generation, with a fixed refusal-and-redirect response | Possible false refusals or missed advice requests, and an additional processing step, but refusals can be tested directly and refused requests cost less |
| Include grounding and citation control | Every claim must be supported by an approved source, with no fabricated figures, and source dates must be shown (FR2, FR3, RAI1, RAI4) | Trust the model to cite correctly; human review of every answer; automatic check after generation | Grounding and citation control applied before the answer is shown | Added latency (NFR1), and some good answers may be wrongly rejected, but unsupported answers are caught automatically |
| Include privacy control | Personal identifiers must not reach external model services or logs (RAI3) | Warn users not to share personal data; redact after logging; redact before any model use or logging | Privacy control at the start of the request path, keeping the amounts needed for calculations | Detection may miss identifiers or remove numbers users need, and adds a small delay, but supports the zero-leak target |

**Traceability Matrix** 

| Objective | Requirement | Evaluation criterion | Architecture element | Later evidence |
| :---- | :---- | :---- | :---- | :---- |
| O1 — Design a grounded assistant | FR1, FR2, FR3, NFR2 | Answer correctness (≥ 85%) | Knowledge Source, Knowledge Access, Language Model Service | Results on the 50-question in-scope set (E1) |
| O1 — Design a grounded assistant | FR2, FR3, RAI1, RAI4 | Source citation and grounding (100% presence, ≥ 95% match, 0 fabricated figures) | Knowledge Source, Grounding & Citation Control | Citation audit of responses (E3) |
| O1 — Design a grounded assistant | FR6 | Response clarity (mean ≥ 4.0) | Language Model Service | Clarity rubric scores on 30 sampled responses (E1) |
| O2 — Develop with retrieval, calculators, and guardrails | FR5, RAI2 | Refusal recall (≥ 95%) and precision (≥ 90%) | Scope / Advice Control, Refusal & Redirect | Refusal test set results (E4) |
| O2 — Develop with retrieval, calculators, and guardrails | RAI3 | Privacy leak count (0) — tested separately from the Part B table | Privacy Control | Inspection of outgoing payloads and logs with synthetic data |
| O3 — Evaluate the solution | FR4, NFR3 | Calculation accuracy (100% within RM 0.01) | Calculation Service, Grounding & Citation Control | Results on the 20 calculation cases (E2) |
| O3 — Evaluate the solution | NFR1, NFR4 | Response latency (p90 \< 5,000 ms); cost per 100 queries | Whole request path, Application Interface | Timing and cost log from the 50-query workload (E5) |

### **Reflection**

**Which architecture decision involved the most important trade-off? Which requirement drove the decision, and what trade-off did you accept?**

The decision to include a scope / advice control involved the most important trade-off. It was driven by FR5 and RAI2: the system must refuse personalised-advice requests, because personalised advice is a regulated activity in Malaysia and wrong advice could harm users. The trade-off I accepted is that the control may refuse some legitimate educational questions, or miss an indirectly phrased advice request, and it adds one processing step. I accepted this because a separate control can be tested directly with the refusal test set, which an instruction inside the model prompt cannot.

