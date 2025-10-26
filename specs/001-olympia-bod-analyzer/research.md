# Phase 0 Research Log — Olympia BoD Analyzer MVP

**Branch**: `001-olympia-bod-analyzer`  
**Date**: 2025-10-10  
**Plan Reference**: [plan.md](./plan.md)  
**Spec Reference**: [spec.md](./spec.md)

## Research Scope

Capture the evidence, decisions, and open questions required to clear the Phase 0 gates in `plan.md`. Each section aligns with the constitution principles and enumerated research topics: privacy/storage, OCR pipeline reliability, citation deep-linking, GPT failover, embedding/alignment model selection, observability metrics, and the end-to-end harness decision.

---

## 1. Privacy Impact & Storage Plan

### Mandate
- Uphold Constitution Principle 1 (Confidential Data Stays Local).  
- Updated FR-010 requires on-prem storage with no automated purge window while preserving manual deletion controls.  
- Unlimited file size (FR-001) shifts risk to storage sizing rather than ingestion limits.

### Current Findings
- **Storage topology**: Repository currently assumes local encrypted filesystem plus SQLite (per plan). No explicit tooling defined yet for envelope encryption or key rotation.  
- **Retention controls**: System now relies on analyst-triggered deletions—any authenticated user can remove decks and derived assets; therefore audit trails must capture who deleted what and when.  
- **At-rest encryption**: Likely requirement for OS-level disk encryption plus application-layer encryption for stored derived artifacts (e.g., AES-256 keys managed via Olympia key vault).  
- **In-transit encryption**: Internal services must run over TLS (even if on-prem). Reverse proxy (e.g., Traefik or Nginx) should terminate TLS but keep data within Olympia network.

### Options Considered
1. **Application-managed encryption** using Python `cryptography` Fernet keys stored in Olympia HSM/Secrets Manager.  
2. **Filesystem-level encryption** via LUKS or FileVault equivalents, with application handling only access control.  
3. **Hybrid**: OS-level encryption + application-level encryption for JSON evidence bundles and SQLite using SQLCipher.

### Recommendation
- Adopt **Hybrid** approach: mount encrypted volume for raw decks + derived files, but also encrypt SQLite with SQLCipher and encrypt JSON evidence bundles at rest with AES-256 keys stored in Olympia-managed secrets store.  
- Replace automated purge job with an auditable deletion service surfaced in the analyst UI/API so any user can manually remove deck sessions; log every deletion event for compliance reporting.  
- Record privacy controls and deletion workflow in `quickstart.md` (Phase 1 deliverable) including verification steps and audit log review guidance.

### Open Questions
- **NEEDS CLARIFICATION**: Which key management service (if any) does Olympia mandate for on-prem workloads? (e.g., HashiCorp Vault, Azure Key Vault on-prem connector).  
- **NEEDS CLARIFICATION**: Confirm reference hardware/storage baseline to size encrypted volume for "unlimited" file size (target deck pair size, concurrent sessions).

---

## 2. OCR & Bilingual Pipeline Assurance

### Mandate
- Constitution operational constraints require bilingual (Greek/English) extraction.  
- FR-002 & Edge Cases demand ≥90% text coverage, OCR fallback for image slides, and accurate table capture.

### Current Findings
- Planned stack: PyMuPDF + pdfplumber for native text, Tesseract + layoutparser for OCR.  
- For Greek text accuracy, Tesseract performs best using `ell+eng` language model; may require finetuning or using OEM LSTM mode.  
- Layoutparser can detect regions but may need custom layout model for BoD slides with dense tables.  
- Need benchmark dataset: propose using historical Westnet decks (with anonymized derivatives) to measure text coverage and table accuracy.  
- Observed risk: OCR latency increases pipeline time; must keep total runtime ≤15 minutes (Principle 4).

### Options Considered
1. **Tesseract default (LSTM) with preprocessing** (binarization, deskew).  
2. **Google Vision OCR on-prem emulation** (not aligned with Principle 1 unless fully offline).  
3. **Trained EasyOCR / PaddleOCR** models with Greek support.

### Recommendation
- Start with **Tesseract LSTM** using combined `eng+ell` language pack, apply preprocessing with OpenCV (thresholding, deskew) before OCR, and evaluate accuracy vs latency.  
- Maintain fallback pipeline to re-process low-confidence pages with alternative OCR if accuracy <90%.  
- Build regression harness (Pytest) that measures coverage per document; store metrics for Principle 3 compliance.

### Open Questions
- Provide sample decks (with sensitive content redacted) for benchmarking?  
- Confirm tolerance for GPU acceleration (if we consider PaddleOCR) within Olympia infrastructure.

---

## 3. Citation Deep-Link Strategy

### Mandate
- Principle 2 requires slide/page/hash citations accessible within two clicks.  
- FR-011 mandates clickable "Slide X" links in dashboard and exports.

### Current Findings
- Need consistent coordinate system for both dashboard viewer and exported brief.  
- Potential approach: store for each insight the source `deck_id`, `page_number`, `bounding_box` (x1, y1, x2, y2), and content hash (SHA-256 of normalized text).  
- For PDF viewing, can leverage PDF.js (frontend) to render and scroll to bounding box anchor.  
- Offline exports only require citations; evidence snapshots become optional UX enhancement rather than a compliance necessity.

### Options Considered
1. **PDF.js Web Viewer** with custom deep-link query params (e.g., `?page=12&highlight=abc`).  
2. **Server-side rendered reference images** (PNG per page) with anchor overlays.  
3. **Hybrid**: store evidence snapshots (small PNG crop) along with canonical coordinates.

### Recommendation
- Implement **Hybrid** viewer support while treating snapshots as optional assets: primary viewer uses PDF.js deep-linking, and exports rely on citations-only format.  
- Shared schema should include citation object: `{ slide: int, page: int, bbox: [x1,y1,x2,y2], content_hash: str, snap_id: Optional[str] }`.  
- Dashboard renders clickable `Slide X` that triggers scroll and highlight; export uses footnotes referencing same ID and omits embedded imagery by default.

### Open Questions
- Confirm whether analysts require offline evidence snapshots embedded directly in export (affects storage/purge).  
- Validate whether Olympia legal requires hashed slide content or full text in audit logs.

---

## 4. GPT-4 Failover Playbook

### Mandate
- FR-013 requires automatic failover when primary GPT-4 Turbo unavailable.  
- Principle 5 emphasises explainability and versioned prompts.

### Current Findings
- Primary model: `gpt-4.1-turbo` (per plan). Candidate secondary: `gpt-4o-mini` or `gpt-4.0-mini` for lower latency; alternatively on-prem fallback like `gpt-3.5-turbo` standard.  
- Need health check monitoring for primary endpoint (latency, success rate).  
- Failover strategy must log events and expose status in UI (e.g., banner stating "using secondary model").  
- Prompts must be compatible; may need slight adjustments for smaller context window.

### Options Considered
1. **OpenAI-to-OpenAI failover**: use Organization-level secondary endpoint with same API semantics.  
2. **Azure OpenAI mirror**: maintain second deployment with same model; requires network reach and compliance review.  
3. **Local smaller model** (e.g., LLaMA) for offline fallback—could degrade quality significantly.

### Recommendation
- Implement **OpenAI-to-OpenAI failover** using secondary deployment (either separate region or model).  
- Introduce health check middleware (FastAPI) that tests primary before each batch; if 5xx or timeout > configurable threshold, automatically switch and record event.  
- Store failover metadata with each narrative output for audit (model name, timestamp, reason).  
- Document manual override procedure in `quickstart.md`.

### Open Questions
- Confirm whether Olympia security permits external API calls during analysis or requires offline cache mode.  
- Identify exact secondary model/endpoint already provisioned (if any).  
- Determine SLA for returning to primary (automatic retry interval?).

---

## 5. Embedding & Alignment Model Selection

### Mandate
- Section alignment (FR-003) and commitment/tone detection rely on embeddings with accuracy ≥80%.  
- Must support bilingual text and scale to ~100 sections per deck.

### Current Findings
- Candidate models: `sentence-transformers/paraphrase-multilingual-mpnet-base-v2`, `text-embedding-3-large` (OpenAI), or `intfloat/multilingual-e5-large`.  
- Need on-prem inference compatibility (Principle 1) — prefer open models to avoid runtime external calls.  
- Embedding size impacts storage and latency; need to balance with accuracy.

### Options Considered
1. **Multilingual MPNet** (offline capable, 768-dim).  
2. **E5-Large** fine-tuned on financial corpora (requires GPU for optimal speed).  
3. **OpenAI embeddings** (high accuracy but external call, conflicting with privacy).

### Recommendation
- Adopt **sentence-transformers/multilingual-mpnet-base-v2** as default offline embedding model; evaluate accuracy on deck corpus.  
- Consider domain adaptation via continued training on historic BoD decks (without leaving environment); store training pipeline for future improvement.  
- For commitment/tone extraction, combine embeddings with keyword heuristics to increase recall.

### Open Questions
- Confirm hardware availability (GPU vs CPU) for embedding inference and fine-tuning.  
- Provide labeled dataset (matched sections, commitments) to evaluate accuracy before finalizing model.

---

## 6. Observability & Accuracy Metrics Design

### Mandate
- Principle 3 & NFR-001 require accuracy %, latency p95, purge audits, without storing raw decks.  
- Principle 4 requires time-to-insight metrics.

### Current Findings
- Need metrics pipeline that captures: extraction coverage %, table accuracy %, alignment precision/recall, commitment/tone confidence distribution (target 0.95 auto-promotion threshold), pipeline stage durations, and manual deletion audit events.  
- Must avoid storing sensitive content; only metadata & hashes permitted.  
- Proposed stack: OpenTelemetry exporters writing to self-hosted Prometheus/Grafana or Elastic; logs sanitized and stored locally.

### Options Considered
1. **Prometheus + Grafana** for metrics, Loki for logs.  
2. **OpenTelemetry + TimescaleDB** storing metrics with retention policy.  
3. **Custom SQLite metrics table** (low overhead but harder to visualize).

### Recommendation
- Use **OpenTelemetry** instrumentation with Prometheus exporter; dashboards in Grafana hosted within Olympia network.  
- Define metric naming spec (e.g., `bod_extraction_text_coverage_ratio`, `bod_pipeline_stage_duration_seconds`, `bod_deletion_events_total`).  
- Implement deletion audit log table capturing `artifact_id`, `deleted_at`, `actor` and emit metrics `bod_deletion_events_total` / `bod_active_sessions`.  
- Provide CLI script to generate compliance report (tie into quickstart).

### Open Questions
- Confirm existing observability stack within Olympia IT (reuse vs new).  
- Clarify retention policy for metrics (default 30 days?).

---

## 7. End-to-End Harness Decision

### Mandate
- Plan flagged NEEDS CLARIFICATION on whether Playwright remains the E2E tool.  
- Constitution requires automated tests covering accuracy and UI flows before release.

### Current Findings
- Frontend is React; backend is FastAPI. Playwright offers cross-browser testing and PDF interactions.  
- Alternative: Cypress (JS) or Python-based integration harness (e.g., pytest + playwright plugin).  
- Need to automate analyst workflow: upload decks, wait for analysis, verify dashboard data and citations.

### Options Considered
1. **Playwright (TypeScript)**: existing plan default, good for headless Chromium, can script file uploads and check UI states.  
2. **Cypress**: simpler for component tests but less suited for multi-tab or PDF viewer interactions.  
3. **k6 + API smoke**: for backend only, would require separate UI testing.

### Recommendation
- Stay with **Playwright** (TypeScript) for full-stack E2E, integrate with CI (GitHub Actions or local runner).  
- Add supporting API smoke tests via pytest for pipeline endpoints to isolate failures.  
- Ensure test data uses synthetic decks with sanitized content.

### Open Questions
- Confirm licensing or security approvals for running Playwright browsers within Olympia environment.  
- Clarify expected cadence for E2E suite (per merge vs nightly) given runtime (~15 min pipeline).  
- Determine if analysts need manual verification script alongside automated harness.

---

## Summary of Pending Clarifications

1. Key management platform for encryption keys and secrets (Privacy & Storage).  
2. Reference hardware and storage capacity expectations for "unlimited" deck uploads.  
3. Availability of redacted sample decks for OCR benchmarking and evaluation datasets for embeddings/alignments.  
4. GPU allowance (if any) for OCR/embedding acceleration in on-prem environment.  
5. Legal guidance on storing hashed slide content vs full text in audit logs.  
6. External API policy for GPT failover — confirm permitted endpoints & network constraints.  
7. Selected secondary LLM endpoint/model (if already provisioned) and failback SLA expectations.  
8. Existing observability stack within Olympia IT and metric retention policy.  
9. Approval for Playwright usage and desired execution cadence (CI vs manual).  
10. Expected dataset availability for accuracy regression (commitments, tone, alignments).  
11. Confirmation of hardware resources (CPU/GPU) for embedding inference and fine-tuning.

*Please provide answers or guidance on the above to unblock final Phase 0 confirmation. Additional questions can be logged as research progresses.*
