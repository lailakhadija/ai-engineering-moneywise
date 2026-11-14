## Week 3 — V1 Data Pipeline (Pathway A: PDF)

**Source:** FEN *Malaysia National Strategy for Financial Literacy 2026-2030* (PDF, 39 pages). Pathway A only; one source in V1.

**Result:** 39 pages (1 blank, 1 contents page skipped), 330 page-aware, sentence-aware chunks (up to 500 characters), every chunk keeps its PDF page, 0 cut words (a fixed-size baseline cut 175), lineage manifest verified, repeatability check PASS.

**Known limitations:** page numbers are PDF pages (printed labels are not mapped), a few chart labels remain, and headings merge into the following text. Next iteration: map printed pages, detect headings, and record the `pypdf` version.

| Artefact | Location |
| :---- | :---- |
| Pipeline notebook | [`notebooks/week3_pipeline.ipynb`](notebooks/week3_pipeline.ipynb) |
| Data inventory | [`docs/week3_data_inventory.md`](docs/week3_data_inventory.md) |
| Pipeline diagram | [`docs/week3_pipeline_diagram.png`](docs/week3_pipeline_diagram.png) |
| Pipeline decisions | [`docs/week3_pipeline_decisions.md`](docs/week3_pipeline_decisions.md) |
| Part B lineage manifest | [`data/week3_manifest.json`](data/week3_manifest.json) |
| Part C run manifest | [`data/pipeline_v1/documents/pipeline_manifest.json`](data/pipeline_v1/documents/pipeline_manifest.json) |

**Data rights:** raw PDFs are not committed. FEN reuse terms are not verified.
