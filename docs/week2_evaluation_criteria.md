

| Objective / Requirement ID | Evaluation Criterion | Capability Bucket | Measurement Method | Metric | Threshold | User / Business Outcome |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| O1 / FR1 / NFR2 | Answer correctness | Domain-specific capability | Ask a curated set of at least 50 in-scope questions. Score each answer against a reference answer written from the approved sources. A partially correct answer counts as incorrect. | Correct response rate (%) | ≥ 85% | Young users get reliable answers to routine money questions. |
| O3 / FR4 / NFR3 | Calculation accuracy | Domain-specific capability | Run at least 20 test cases (loan repayment, compound interest, budget allocation). Compare each result with an independently computed reference value. | Pass rate (result within RM 0.01 of reference) | 100% | Users can trust the numbers they use for repayments and savings plans. |
| O1 / FR2 / RAI1 | Source citation and grounding | Instruction-following capability | Inspect document-grounded responses from the 50-question set. Check that a citation is present, that the cited source supports the answer, and that no figure is missing from the sources. | Citation presence rate; citation-source match rate; count of fabricated figures | 100% presence; ≥ 95% match; 0 fabricated figures | Users can verify information against official BNM, FEN, and AKPK sources. |
| O2 / FR5 / RAI2 | Refusal of personalised-advice and out-of-scope requests | Instruction-following capability | Test with at least 30 queries that should be refused (personalised advice, investment, tax, insurance) and 20 in-scope queries that should be answered. | Refusal recall (refused ÷ should-be-refused); refusal precision (correct refusals ÷ all refusals) | Recall ≥ 95%; precision ≥ 90% | Lowers the risk that explanations are taken as regulated advice, without blocking legitimate learning. |
| O1 / FR6 | Response clarity | Generation capability | Two raters score 30 sampled responses with the clarity rubric below. | Mean clarity score (1–5) | ≥ 4.0 | Users aged 18–30 with limited knowledge can understand the answers. |
| O3 / NFR1 | Response latency | Cost and latency | Measure end-to-end response time across a workload of 50 test queries. | p90 latency | \< 5,000 ms | Users get answers fast enough for a smooth chat experience. |

## **Reflection**

**Which evaluation criterion is most important for determining whether your AI application will be useful to its intended users? Why?**

Source citation and grounding. MoneyWise is useful only if young users can trust and verify what it says. Wrong information about loans or fees can lead to poor money decisions. Grounding is also what separates this project from a general-purpose chatbot, since it shows that answers come from official Malaysian sources. Correctness depends on it, because a correct-looking answer with no verifiable source is hard for users to trust.

