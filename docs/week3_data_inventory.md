# Week 3 Data Inventory — MoneyWise

**Pathway:** A (PDF documents), one source in V1. No dummy dataset is used.
**Notebook:** `notebooks/week3_pipeline.ipynb` (run in Google Colab, files stored on Google Drive)

---

## Step A1 — From Topic 2 requirements to data needs

| Requirement ID | Needed information / feature | Candidate source | Why usable / constraint |
| :---- | :---- | :---- | :---- |
| FR1 / FR3 / NFR2 | Approved text on financial literacy, so answers can be grounded and the system can say when the documents do not contain the answer | S01: FEN *Malaysia National Strategy for Financial Literacy 2026-2030* (PDF) | Official, public, recent, and it contains the youth evidence used in Week 1. **Constraint:** it is a national strategy, not a how-to guide, so answers will be about priorities, findings, and policy rather than step-by-step money advice. |
| FR2 / RAI4 | Source identity, page, and date for each chunk, for citations | Same PDF (file name, SHA-256, PDF page, owner, year) | Every chunk keeps its **PDF page number**. The printed page label is not mapped yet, and the document date is not stored per chunk (partly supported). |
| FR5 | Names of official help channels (for example AKPK debt counselling) for refusal redirects | Same PDF (partial coverage) | The inspected text discusses debt counselling services, and the PDF links to an `akpk.org.my` resource. This gives partial coverage. The redirect wording can still be a fixed template. |
| NFR5 | A repeatable way to refresh the data when the document changes | SHA-256 checksum recorded in the manifests; repeatability check (Cell 9) | A checksum shows a file changed. It does not prove the content is accurate or permitted to share. |

---

## Step A4 — Data inventory

| Source ID | Filename / URL | Type and purpose | Version / date / owner | Access / rights | Inspection evidence |
| :---- | :---- | :---- | :---- | :---- | :---- |
| S01 | `FEN_NS2_ENG_Interactive_FA_LowRes.pdf`, from https://www.fenetwork.my/wp-content/uploads/2025/10/FEN_NS2_ENG_Interactive_FA_LowRes.pdf | PDF, 5,226,880 bytes. National strategy for financial literacy 2026-2030. **Processed in V1.** Supports FR1, FR2, FR3 and the Week 1 problem evidence. | Owner: Financial Education Network (FEN, co-chaired by BNM and the Securities Commission). Year: 2025 (the file path shows an October 2025 upload). Downloaded `[10 November 2026]`. | Public PDF. Reuse terms **not verified**. Manifest says "Not verified". | **Whole file:** 39 pages, 1 empty page, 131,469 characters in page text. **Subset 1 (page 1):** empty, no text layer. **Subset 2 (page 19):** readable text about debt management ("Improve debt management knowledge and practices for long-term financial health ... increase awareness and accessibility of debt counselling services"). **Prepared chunks checked:** 0001 (page 3, opening message), 0166 (page 18, prose), 0330 (page 39, a short chart-label fragment). |
| S02 | `FLCC-for-Malaysian-adults_compressed.pdf`, from https://www.fenetwork.my/wp-content/uploads/2023/02/FLCC-for-Malaysian-adults_compressed.pdf | PDF, 1,156,794 bytes. Financial Literacy Core Competencies for Malaysian Adults. **Uploaded and inspected only; not processed in V1.** Candidate source for the next iteration, because it covers budgeting, credit and debt, and saving. | Owner: Financial Education Network (FEN). The text says January 2021, but the cover page carries a layout stamp dated 14 Feb 2023, so the version is ambiguous. Downloaded `[10 November 2026]`. | Public PDF. Reuse terms **not verified**. | **Whole file:** 56 pages, 0 empty pages, 77,889 characters (an earlier session gave 77,950, see issue 6). **Subset 1 (page 1):** cover text plus a print-production line ("Layout FLCC Amended Final Printed B.indd ... 14/02/2023"), which is noise. **Subset 2 (PDF page 28):** readable table text, section 3.1.3 Credit Score, printed page label **24**. |

### Prepared outputs (from S01)

| Artefact | Location in repo | Contents | Evidence |
| :---- | :---- | :---- | :---- |
| `prepared_text.jsonl` | `data/processed/` | **330 chunks** (20 to 500 characters, average 374.7), each with record ID, text, source file, SHA-256, processing label, and PDF **page** (pages 3 to 39, 37 pages with chunks) | 210,422 bytes. Read back and counted in Cell 5A2. Validation PASS. |
| `week3_manifest.json` | `data/` | 1 lineage entry: S01 → `prepared_text.jsonl`, with checksums and the cleaning description | Cell 6B: COMMON LINEAGE VALIDATION PASS |
| `pipeline_manifest.json` | `data/pipeline_v1/documents/` | Part C run record (source fingerprint, output fingerprint, mapping) | Cell 8A SUCCESS; Cell 9 repeatability PASS; Part C record count matches Part B (330) |

**Source SHA-256 (S01):** `0f36d2bebbd5fafdd5a3de3e0ba154ff10ba8b516f298d2a290e1118ff73650b`

**Cleaning applied in V1:** 1 blank page skipped (page 1), 1 contents page skipped (page 2), 174 number-only lines dropped, 37 running footers removed. After cleaning, 0 chunks still contain the footer text.

**Raw vs prepared storage:** raw PDFs and prepared files live in the Google Drive folder `ai_engineering/week3_project/data/` (`raw/`, `processed/`, `pipeline_v1/`). Raw PDFs are not committed. Prepared text is committed only if reuse is permitted (see the decisions file).

---

## Known data issues carried to Topic 4

1. **PDF page vs printed page:** chunks carry the PDF page index. For S02 the two differ (PDF page 28 is printed page 24). For S01, the PDF has 39 pages and the first chunk is on PDF page 3, but the printed numbering is not mapped. `[[FILL: open the PDF and check whether PDF pages match printed pages, or whether it uses two-page spreads]]`
2. **Chart labels remain:** number-only lines were removed, but text labels from infographics stay. The last chunk (0330, page 39) is only a label fragment ("Index Objective Financial Resilience Subjective Financial Well-being", 68 characters).
3. **Removed numbers not individually checked:** the 174 dropped lines were judged to be infographic values, but a few could be real figures from tables.
4. **Headings merge into the text:** line breaks became spaces, so a heading and its first sentence run together.
5. **Chunks never cross pages:** a sentence that continues onto the next page is split in two, and short chunks occur (minimum 20 characters).
6. **Extraction can differ between sessions:** S02 gave 77,950 characters in one session and 77,889 in another, while S01 was identical. A likely cause is a different `pypdf` version, which is not confirmed. Pin and record the version in the next iteration.
7. **Rights:** access terms are `Not verified`. S02's version is ambiguous (January 2021 text, February 2023 layout stamp).
