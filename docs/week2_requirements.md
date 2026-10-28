**\# Requirements Specification — \[MoneyWise\]**

**Version: 1.0** 

**Functional Requirements**

| \# | Requirement — The system SHALL | Scope Boundary — SHALL NOT (where relevant) |
| :---: | :---- | :---- |
| **FR1** | Answer questions about basic personal finance concepts (budgeting, saving, credit cards, buy-now-pay-later, personal loans, and where to get help) using content from the approved BNM, FEN, and AKPK documents. | Invent rates, fees, rules, or programme details that are not supported by the source documents. |
| **FR2** | Provide a source reference (document title, section or page, and the document's date) for each answer based on the approved documents. | Present unsupported information as if it came from an official source. |
| **FR3** | State clearly when the approved documents do not contain enough information to answer, and point the user to an appropriate official channel. | Guess or fill gaps with unsupported general knowledge. |
| **FR4** | Calculate loan repayments, compound interest, and simple budget allocations from values provided by the user, and show the inputs, assumptions, and result. | Assume interest rates or other inputs that the user or a cited source has not provided. |
| **FR5** | Decline requests for personalised financial advice or topics outside the defined scope (e.g., "which fund should I buy?", tax or insurance advice), explain what it can help with instead, and redirect the user to an appropriate channel such as AKPK or a licensed adviser. | Recommend a specific product, provider, investment, or course of action for the user. |
| **FR6** | Present answers in clear, plain language suitable for adults aged 18–30 with limited financial knowledge, and explain technical terms when it uses them. | Assume prior financial knowledge.  |

**Non-functional Requirements**

| \# | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **NFR1** | Performance | The system shall keep interactive responses within the agreed latency target. | Measure end-to-end response time on a defined workload of 50 test queries. | p90 latency | \< 5,000 ms |
| **NFR2** | Correctness | Answers to in-scope questions shall be factually consistent with the approved source documents. | Score responses to a curated set of at least 50 in-scope questions against a rubric and reference answers. | Correct response rate (%) | ≥ 85% |
| **NFR3** | Correctness (calculation) | Financial calculations shall match reference calculations. | Run at least 20 calculation test cases (loan repayment, compound interest, budget allocation) and compare results with independently computed values. | Pass rate (result within RM 0.01 of reference) | 100% |
| **NFR4** | Cost | Inference cost shall stay within the project budget. | Record model/API usage over the 100-query evaluation workload. | Cost per 100 queries |  |
| **NFR5** | Maintainability | Updated source documents shall be incorporated without changing unrelated components. | Run a controlled document-update test: replace one source with a modified version and ask 3 questions about the changed facts. | Update time; changed-fact questions answered from the new version | \< 15 min; 3 of 3 |

**Responsible AI Requirements**

| \# | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **RAI1** | Hallucination (invented rates, fees, or rules) | The system shall support every factual claim with an approved source and shall not state unsupported figures. | Evaluate document-grounded responses on the 50-question in-scope set. Check citation presence, whether the cited source supports the answer, and whether any figure is missing from the sources. | Citation presence rate; citation-source match rate; count of fabricated figures | 100% presence; ≥ 95% match; 0 fabricated figures |
| **RAI2** | Implied advice (explanation treated as a recommendation; regulated activity) | The system shall refuse personalised-advice and out-of-scope requests, redirect appropriately, and still answer legitimate educational questions. | Run a test set of at least 30 queries that should be refused (personalised advice, investment, tax, insurance) and 20 in-scope queries that should be answered. | Refusal recall (refused ÷ should-be-refused); refusal precision (correct refusals ÷ all refusals) | Recall ≥ 95%; precision ≥ 90% |
| **RAI3** | Privacy (users paste income, IC numbers, or account details) | The system shall prevent personal identifiers from being sent to external model APIs or stored in logs. | Run at least 20 test prompts containing synthetic IC, bank account, card, and phone numbers. Inspect outgoing API payloads and stored logs. | Number of detected personal-data leaks | 0 |
| **RAI4** | Outdated information (rules and rates change) | The system shall show the date of each cited source and indicate that figures or rules may have changed. | Inspect all document-grounded responses in the evaluation set for a source date and a currency notice. | Source-date display rate | 100% |

