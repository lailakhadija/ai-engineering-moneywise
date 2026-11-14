# Week 3 Pipeline Decisions — MoneyWise

> **Draft status:** All numbers come from the recorded outputs of `notebooks/week3_pipeline.ipynb` (page-aware version). Read it through, check you agree with the wording, then delete this note.

**Notebook:** `notebooks/week3_pipeline.ipynb` | **Diagram:** `docs/week3_pipeline_diagram.png` | **Inventory:** `docs/week3_data_inventory.md`

---

## 1. Requirements and architecture link

| Topic 2 ID | Why it needs this data | V1 status |
| :---- | :---- | :---- |
| FR1, FR3 (NFR2) | Answers must come from approved documents, and the system must be able to say when they do not contain the answer. | Supported: readable text from one approved FEN document (39 pages; 1 blank and 1 contents page skipped). |
| FR2 | Every answer needs a source reference with a page. | **Supported at PDF-page level:** all 330 chunks keep source file, SHA-256, and PDF page. The printed page label is not mapped. |
| RAI4 | Source dates must be shown. | **Partly supported:** the document year is known, but the date is not yet stored per chunk. |
| NFR5 | Updating a document must not require changing other components. | Supported: the pipeline reruns on a changed file, and checksums show the change. |

**Architecture link:** the pipeline expands the **Knowledge Source** part of the Week 2 V1 architecture (see `week3_pipeline_diagram.png`). Embeddings, retrieval, and the language model are outside Topic 3.

---

## 2. Selected source

**S01 — FEN *Malaysia National Strategy for Financial Literacy 2026-2030* (PDF, 39 pages).** Chosen because it is official, recent, and contains the youth evidence used in Week 1. Pathway A (PDF / TXT) is the only pathway used.

A second file (FLCC, 56 pages) was uploaded and inspected but not processed. The Week 1 scope listed three sources; only one is processed in V1, which the lab allows when it is justified. The others remain planned.

---

## 3. Part B comparison and chosen configuration

Both approaches ran on the same extracted pages (131,507 characters including page-join newlines).

| Approach | Chunks | Avg chars | At 500 cap | Mid-word cuts | Chunks with page ref | Other observations |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **A — Fixed-size:** whole document, 500-character slices | 263 | 499.1 | 262 | **175** | 0% | The first sample is the contents list. Footers and chart numbers stay in. |
| **B — Page-aware + sentence-aware:** one page at a time, packed from whole sentences up to 500 characters | 330 | 374.7 | 13 | **0** | **100%** | Skipped 1 blank and 1 contents page. Dropped 174 number-only lines and removed 37 footers. The first sample is real prose (page 3). |

**How I got here:** the lab's starter paragraph-aware splitter (first run) produced 268 chunks, with 254 (94.8%) at the 500 cap and `page = null`. It cut words and kept no pages, so I replaced it with approach B.

**What I saw when I opened sample records:**

- Chunk 0001 (page 3) is clean prose from the Prime Minister's message, with no cut words.
- Chunk 0166 (page 18) is readable prose about consumer protection and financing habits.
- Chunk 0330 (page 39) is only a chart-label fragment (68 characters), so some infographic text remains.
- No chunk contains the running footer text.

**Selected approach: B (page-aware + sentence-aware, 500 characters).**

- **Benefit:** every chunk keeps its PDF page number, which FR2 needs for citations, and no word is cut in half (0 against 175 in A). Footers, the contents list, and infographic numbers no longer pollute the chunks.
- **Accepted limitation:** chunks are shorter (average 374.7, minimum 20) because they never cross a page break, so a sentence that continues onto the next page is split. Headings merge into the following text, a few infographic labels remain (for example chunk 0330), and the 174 dropped number-only lines were not checked one by one.

**Planned next iteration (not done yet):** map PDF pages to printed page labels; detect headings; handle sentences that cross pages; drop leftover chart labels; and record the `pypdf` version used.

### Chosen preparation configuration

| Setting | Value | Reason |
| :---- | :---- | :---- |
| Chunk size | 500 characters | The lab's example size. Small enough to cite precisely. Not tuned yet. |
| Method | `page-aware-sentence` | See above. |
| Page handling | One page at a time; skip blank pages (1) and the contents page (page 2) | Keeps page references and avoids contents-list noise. |
| Cleaning rules | Remove the running footer ("Malaysia National Strategy for Financial Literacy 2026 - 2030", optionally followed by chapter tab numbers); drop lines made only of digits and number punctuation | Chosen from the inspection output (chunks 0001, 0135, 0268 of the first run). |
| Line breaks | Replaced by spaces within a page | Gives complete sentences, but merges headings into the text. |
| Missing text | Blank pages are skipped and counted; no text is filled in | No guessing of content. |

Note on character counts: the page text totals 131,469 characters. The lab code joins the 39 pages with newlines, which gives the 131,507 shown in Cell 5A1.

---

## 4. ETL or ELT, and batch or streaming

- **ETL.** The text is cleaned and chunked **before** it is stored as the prepared artefact. There is no database or vector store at this stage that could transform it later, so loading raw data first (ELT) would add nothing. The raw PDF stays unchanged.
- **Batch.** The source document changes rarely, and the assistant does not need live data. The pipeline is run on demand when a new version is published. Streaming would add complexity without benefit.

---

## 5. Output locations

Colab ran against Google Drive under `ai_engineering/week3_project/`. In the repo these map to:

| Output | Drive path | Repo path |
| :---- | :---- | :---- |
| Prepared chunks (Part B) | `data/processed/prepared_text.jsonl` | `data/processed/prepared_text.jsonl` |
| Part B lineage manifest | `data/week3_manifest.json` | `data/week3_manifest.json` |
| Part C output and run manifest | `data/pipeline_v1/documents/` | `data/pipeline_v1/documents/` |
| Notebook | n/a | `notebooks/week3_pipeline.ipynb` |

---

## 6. Stage contracts and evidence

| Stage | Contract / evidence | Observed in this run | Failure response |
| :---- | :---- | :---- | :---- |
| Ingest | The chosen file exists and is the exact intended source | Cell 3 listed 2 files; `select_source` picked `FEN_NS2_ENG_Interactive_FA_LowRes.pdf` by name | Stop with `FileNotFoundError` |
| Inspect | The file is readable and has extractable text | 39 pages, 1 empty page, 131,469 characters of page text | Stop with "No extractable text" (scanned PDFs need OCR, which is deferred) |
| Prepare | The chosen method produces the expected output | 330 chunks, 37 pages with chunks, 0 forced mid-word cuts, 0 footers left | Stop if no chunks are produced |
| Validate | The output exists, is non-empty, and reads back correctly | 210,422 bytes, record count matches, every chunk has a page, Validation PASS | Stop on a missing or empty output |
| Record lineage | The source links to its exact output with checksums | Cell 6B PASS; Cell 8A manifest written; Cell 9 PASS; Part C count equals Part B (330) | Stop if a mapping or manifest is missing, or a checksum does not match |

### Two plausible failure points (also shown in the diagram)

1. **No extractable text** (scanned or garbled PDF, or a page with no text layer). *This already happened in a mild form:* page 1 of the PDF is empty. *Response:* the pipeline skips and counts blank pages, and only stops if the whole document has no text. A fully scanned file would stop with an error, the source would be flagged in the inventory, and another file would be used. OCR is outside Topic 3.
2. **Missing or empty output, or fingerprints change on rerun.** *Response:* stop and do not register lineage. Fix the settings, rerun, and update the recorded source version if the document changed.

---

## 7. Provenance and access caveats

- `source_version` and `access_rights` in the manifests read **"Not verified"** by design, and stay so until the terms are checked.
- FEN's reuse terms were not found, so the PDF and the prepared text are treated as not redistributable. The raw PDF (5.2 MB) is **not committed**.
- A SHA-256 hash proves a file did not change. It does not prove the data is accurate, current, or permitted to share.
- The repeatability check passes within one session. Across sessions, the FLCC character count differed (77,950 vs 77,889), which may come from a different `pypdf` version. The S01 count did not change.
- Ask your instructor whether `prepared_text.jsonl` (full document text, about 210 KB) may go into the repo. If not, keep it in the private Drive folder and commit only the manifests.

---

## 8. Traceability update (add to `week2_architecture_decisions.md`)

| Objective | Requirement | Evaluation Criterion | Architecture Element | Later Evidence (new) |
| :---- | :---- | :---- | :---- | :---- |
| O1 | FR1, FR3 | Answer correctness; "not enough information" handling | Knowledge Source, Knowledge Access | `data/week3_manifest.json`; `data/processed/prepared_text.jsonl` (330 chunks); `docs/week3_pipeline_decisions.md` |
| O1 | FR2, RAI4 | Source citation and grounding; source dates shown | Knowledge Source | Every chunk has source, SHA-256, and PDF page. The printed page and the document date per chunk are **not yet** stored. |
| O1 | NFR5 | Update without changing other components | Knowledge Source | `data/pipeline_v1/documents/pipeline_manifest.json` (SHA-256 and repeatability PASS) |
