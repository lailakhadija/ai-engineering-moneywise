# ai-engineering-moneywise
# MoneyWise — AI-Powered Financial Literacy Assistant
Answers young Malaysians' questions about budgeting, credit, debt, and financial concepts using official Malaysian financial education documents, with citations and appropriate safety guardrails.

## Problem Statement
Young Malaysians may lack the financial knowledge needed to manage budgets, credit, and debt effectively. 
Financial guidance is spread across agencies such as BNM, FEN, and AKPK, making reliable information difficult to navigate. 
AI engineering enables MoneyWise to retrieve relevant official documents and provide plain-language answers with citations, without giving personalised financial advice.

## Evidence of the Problem
Evidence 1: Malaysian youth face higher debt burdens from student loans, store credit, and borrowing from family and friends. Four in five Malaysians seek debt advice from family and close friends. (Financial Education Network, 2025).

Evidence 2: A survey of 1,121 upper secondary students, mostly from B40 communities, found that only 25% had high financial knowledge. Around 90% wanted to learn more, while approximately 85% found financial topics stressful. (Financial Industry Collective Outreach, 2023). Money SENse Report (PDF)

## Initial Related Work / Existing Approaches
1. LLM chatbots for personal finance: Lakkaraju, K., Vuruma, S. K. R., Pallagani, V., Muppasani, B., & Srivastava, B. (2023). Can LLMs Be Good Financial Advisors?.
Limitation: Chatbots produced plausible answers but showed calculation errors and generic responses. The study tested only 13 queries, focused on US financial products, and did not use official documents.

2. Malaysian financial education resources: Financial Education Network (FEN) and AKPK financial education and counselling services. (Financial Education Network, 2025). Malaysia National Strategy for Financial Literacy 2026–2030 (PDF).
Limitation: Resources are distributed across agencies and may be difficult to navigate. Stakeholders identified a need for more personalised and timely financial education.

3. LLM hallucinations in finance: Kang, H., & Liu, X.-Y. (2023). Deficiency of Large Language Models in Finance: An Empirical Examination of Hallucination. Limitation: LLMs generated inaccurate or outdated financial information. Although RAG improved factual accuracy, the study did not address Malaysian financial sources or young adults' everyday financial questions.

## Project Objectives
O1: To design a document-grounded financial literacy assistant using official Malaysian financial education sources.
O2: To develop the assistant using RAG, source citations, deterministic financial calculators, and safety guardrails that redirect personalised financial advice requests to AKPK or licensed financial professionals.
O3: To evaluate answer groundedness, calculation accuracy, refusal precision and recall, and response latency.

## Target Users
Malaysians aged 18–30, particularly university students and young adults entering the workforce who need accessible explanations of everyday financial concepts.

## Data Sources
Source 1: BNM Financial Education and Awareness — https://www.bnm.gov.my/financial-education-awareness
Source 2: FEN Financial Literacy Core Competencies for Malaysian Adults (PDF) — https://www.fenetwork.my/wp-content/uploads/2023/02/FLCC-for-Malaysian-adults_compressed.pdf
Source 3: FEN Malaysia National Strategy for Financial Literacy 2026–2030 (PDF) — https://www.fenetwork.my/wp-content/uploads/2025/10/FEN_NS2_ENG_Interactive_FA_LowRes.pdf

## Weekly Progress
| Week | Focus | Deliverable | Status |
|------|-------|-------------|--------|
| W1 | System map + project scope | README + docs | ✅ |
