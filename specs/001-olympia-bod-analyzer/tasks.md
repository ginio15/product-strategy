# Task Plan — Olympia BoD Analyzer MVP Comparison Core

**Feature Branch**: `001-olympia-bod-analyzer`  
**Spec**: [spec.md](./spec.md)  
**Plan**: [plan.md](./plan.md)

---

## Tracking Overview

| Order | Task ID | Phase | Description | Status |
|-------|---------|-------|-------------|--------|
| 1 | T001 | Setup | Scaffold FastAPI backend project | ☐ |
| 2 | T002 | Setup | Initialize React + Tailwind frontend | ☐ |
| 3 | T003 | Setup | Create shared schema workspace | ☐ |
| 4 | T004 | Foundational | Implement encrypted SQLite/SQLCipher factory | ☐ |
| 5 | T005 | Foundational | Add centralized configuration & deletion audit interface | ☐ |
| 6 | T006 | Foundational | Stand up authenticated upload session API skeleton | ☐ |
| 7 | T007 | Foundational | Build deletion audit log model & CLI verifier | ☐ |
| 8 | T008 | Foundational | Create frontend shell layout | ☐ |
| 9 | T009 | US1 | Implement PDF parsing pipeline | ☐ |
| 10 | T010 | US1 | Integrate OCR fallback | ☐ |
| 11 | T011 | US1 | Build section alignment & omission detection service | ☐ |
| 12 | T012 | US1 | Implement numeric variance utilities | ☐ |
| 13 | T013 | US1 | Wire analysis + per-section narrative pipeline | ☐ |
| 14 | T014 | US1 | Persist structured outputs, narratives & citations | ☐ |
| 15 | T015 | US1 | Capture observability metrics | ☐ |
| 16 | T016 | US1 | Implement manual deletion API route | ☐ |
| 17 | T017 | US1 | Create frontend analysis client | ☐ |
| 18 | T018 | US1 | Develop dashboard diff/omission/narrative components | ☐ |
| 19 | T019 | US1 | Build session controls UI | ☐ |
| 20 | T020 | US1 | Author regression harness for extraction, omissions & narratives | ☐ |
| 21 | T021 | US2 | Implement commitment extraction engine | ☐ |
| 22 | T022 | US2 | Implement tone shift detector | ☐ |
| 23 | T023 | US2 | Extend insights repository | ☐ |
| 24 | T024 | US2 | Expose commitments/tone API endpoints | ☐ |
| 25 | T025 | US2 | Add confidence gating logic | ☐ |
| 26 | T026 | US2 | Build frontend commitments panel | ☐ |
| 27 | T027 | US2 | Build tone highlights visualization | ☐ |
| 28 | T028 | US2 | Add regression tests for commitment/tone | ☐ |
| 29 | T029 | US3 | Implement narrative generator with failover hooks | ☐ |
| 30 | T030 | US3 | Create citation footnote builder | ☐ |
| 31 | T031 | US3 | Build export assembly service | ☐ |
| 32 | T032 | US3 | Integrate PDF renderer | ☐ |
| 33 | T033 | US3 | Add export API endpoint and polling | ☐ |
| 34 | T034 | US3 | Implement frontend export panel | ☐ |
| 35 | T035 | US3 | Write integration test for export | ☐ |
| 36 | T036 | Polish | Implement failover monitoring & banner | ☐ |
| 37 | T037 | Polish | Configure Playwright E2E flow | ☐ |
| 38 | T038 | Polish | Add performance benchmark script | ☐ |
| 39 | T039 | Polish | Document operations in quickstart | ☐ |
| 40 | T040 | Polish | Run agent context sync script | ☐ |

*Mark the Status column with ☑ when a task completes to maintain progress visibility.*

---

## Phase 1 — Setup & Environment Baseline

**Objective**: Establish backend, frontend, and shared workspaces aligned with the planned architecture.

- **Independent Test Criteria**: Repositories build without errors; lint passes for empty scaffolds; shared schema package importable from both backend and frontend.

Tasks:
- [ ] **T001**: Scaffold FastAPI backend project with Poetry config, base app, and settings module (`backend/pyproject.toml`, `backend/src/app/main.py`).
- [ ] **T002**: Initialize React + Tailwind frontend with Vite tooling and CI-ready scripts (`frontend/package.json`, `frontend/src/main.tsx`).
- [ ] **T003**: Create shared schema workspace with package metadata and build script for Python/TypeScript outputs (`shared/schemas/pyproject.toml`, `shared/schemas/README.md`).

---

## Phase 2 — Foundational Infrastructure (Blocking)

**Objective**: Implement cross-story prerequisites for secure storage, configuration, and baseline UI shell.

- **Independent Test Criteria**: Manual upload endpoint accepts stub payloads; encrypted SQLite database created; base UI renders without runtime errors.

Tasks:
- [ ] **T004**: Implement encrypted SQLite/SQLCipher connection factory and migrations bootstrap (`backend/src/ingest/storage.py`, `backend/src/migrations/0001_init.py`).
- [ ] **T005**: Add centralized configuration and secrets loader with deletion audit service interface (`backend/src/config.py`).
- [ ] **T006**: Stand up authenticated upload session API skeleton enforcing two-PDF constraint (`backend/src/api/routes/upload.py`).
- [ ] **T007** [P]: Build deletion audit log model and repository with CLI verifier (`backend/src/observability/audit.py`).
- [ ] **T008** [P]: Create frontend shell (split-view layout, routing scaffold, theme tokens) (`frontend/src/components/Layout.tsx`, `frontend/src/styles/theme.css`).

---

## Phase 3 — User Story P1 (US1) — Rapid Deck Comparison for Analysts

**Goal**: Analysts upload two decks and receive a dashboard summarizing financial, narrative, tone changes with citations.

- **Independent Test Criteria**: Regression harness shows ≥90% text coverage & ≥80% numeric correctness; dashboard renders top three changes with drill-down citations; manual deletion removes sessions and logs events.

Tasks:
- [ ] **T009**: Implement PDF parsing pipeline (PyMuPDF/pdfplumber) preserving hierarchy metadata (`backend/src/parsing/pipeline.py`).
- [ ] **T010**: Integrate Tesseract/layoutparser OCR fallback with preprocessing for image slides (`backend/src/parsing/ocr.py`).
- [ ] **T011**: Build section alignment service using multilingual sentence-transformers embeddings and emit omission lists for unmatched sections (`backend/src/alignment/service.py`).
- [ ] **T012**: Implement numeric variance computation and table alignment utilities (`backend/src/alignment/variance.py`).
- [ ] **T013**: Wire upload analysis route to orchestrate parsing → alignment → per-section narrative generation workflow (`backend/src/api/routes/analyze.py`, `backend/src/narratives/section_summary.py`).
- [ ] **T014** [P]: Persist structured outputs, per-section narratives, citation references, and evidence hashes (`backend/src/ingest/repository.py`).
- [ ] **T015** [P]: Capture observability metrics (coverage %, latency p95, deletion events) via OpenTelemetry (`backend/src/observability/metrics.py`).
- [ ] **T016**: Implement manual deletion API route honoring audit requirements (`backend/src/api/routes/deletion.py`).
- [ ] **T017** [P]: Create frontend analysis client with polling and failover-aware retries (`frontend/src/services/analysisClient.ts`).
- [ ] **T018** [P]: Develop dashboard components for summaries, omissions, per-section narratives, and citation deep-links (`frontend/src/components/AnalysisDashboard.tsx`).
- [ ] **T019**: Build session controls (upload UX, manual deletion button, progress indicators) (`frontend/src/components/SessionControls.tsx`).
- [ ] **T020**: Author regression harness covering extraction accuracy, omission detection, narrative length checks, and runtime budgets (`backend/tests/regression/test_extraction_accuracy.py`).

**Checkpoint**: Analysts can complete end-to-end diff workflow with citations and view deletion audit trail.

---

## Phase 4 — User Story P2 (US2) — Commitment & Tone Accountability

**Goal**: Board members view commitment statuses and tone shifts with confidence gating and citations.

- **Independent Test Criteria**: Commitment/tone module auto-promotes only ≥0.95 confidence insights; UI highlights softened/strengthened language; regression tests verify coverage against labeled deck pair.

Tasks:
- [ ] **T021**: Implement commitment extraction + matching engine with status labeling (`backend/src/insights/commitments.py`).
- [ ] **T022** [P]: Implement tone shift detector leveraging linguistic cues and embeddings (`backend/src/insights/tone.py`).
- [ ] **T023**: Extend insights repository to store commitment/tone results and confidence metadata (`backend/src/insights/repository.py`).
- [ ] **T024** [P]: Expose commitments/tone API endpoints with pagination and filters (`backend/src/api/routes/insights.py`).
- [ ] **T025**: Add confidence gating + manual review flag logic (0.95 threshold enforcement) (`backend/src/insights/confidence.py`).
- [ ] **T026** [P]: Build frontend commitments panel with filters and citation tooltips (`frontend/src/components/CommitmentsPanel.tsx`).
- [ ] **T027** [P]: Build tone highlights visualization with contextual hover overlays (`frontend/src/components/ToneHighlights.tsx`).
- [ ] **T028**: Add regression tests for commitment/tone accuracy and confidence gating (`backend/tests/regression/test_commitment_tone.py`).

**Checkpoint**: Commitment/tone panel is operational with accurate confidence filtering and UI cues.

---

## Phase 5 — User Story P3 (US3) — Exportable Executive Brief

**Goal**: Executives download a citation-rich PDF brief summarizing insights and open questions.

- **Independent Test Criteria**: PDF brief generates in ≤30s with clickable citations and recommended questions; export reports failover status when secondary model engaged.

Tasks:
- [ ] **T029**: Implement narrative generator orchestrating GPT calls with failover hooks (`backend/src/narratives/generator.py`).
- [ ] **T030** [P]: Create citation footnote and metadata builder shared across dashboard/export (`shared/schemas/citation.py`).
- [ ] **T031**: Build export assembly service combining insights, narratives, and variances (`backend/src/export/assembler.py`).
- [ ] **T032**: Integrate WeasyPrint/ReportLab PDF renderer with layout templates (`backend/src/export/pdf_brief.py`).
- [ ] **T033** [P]: Add export API endpoint and status polling (`backend/src/api/routes/export.py`).
- [ ] **T034** [P]: Implement frontend export panel with confidence/status messaging (`frontend/src/components/ExportPanel.tsx`).
- [ ] **T035**: Write integration test validating export citations and timing budget (`backend/tests/integration/test_export_brief.py`).

**Checkpoint**: Executable brief downloadable with full evidence traceability and failover transparency.

---

## Phase 6 — Polish & Cross-Cutting Concerns

**Goal**: Finalize observability, performance, E2E validation, and documentation per constitution.

- **Independent Test Criteria**: Playwright E2E run passes; performance script documents pipeline timing; quickstart includes deletion workflow exercise; agent context updated.

Tasks:
- [ ] **T036**: Implement GPT failover monitoring module and frontend status banner (`backend/src/narratives/failover.py`, `frontend/src/components/FailoverBanner.tsx`).
- [ ] **T037** [P]: Configure Playwright E2E flow covering upload → dashboard → export (`frontend/tests/e2e/analysis.spec.ts`).
- [ ] **T038** [P]: Add performance benchmark script & documentation for 100-page decks (`backend/tests/perf/run_pipeline_benchmark.py`).
- [ ] **T039**: Document end-to-end setup, deletion workflow, and observability dashboards in quickstart (`specs/001-olympia-bod-analyzer/quickstart.md`).
- [ ] **T040**: Run agent context sync script and record summary in plan (`.specify/scripts/bash/update-agent-context.sh`).

**Checkpoint**: System meets constitutional gates with automated E2E coverage and documented operations.

---

## Dependencies & Story Order

1. Phase 1 → 2 → 3 → 4 → 5 → 6 (sequential).  
2. US1 (P1) must complete before US2 and US3.  
3. US2 and US3 can proceed in parallel once US1 milestone is met.

---

## Parallel Execution Examples

- **Phase 2**: T007 and T008 in parallel after T004–T006.  
- **US1**: After T013, run T014–T018 concurrently.  
- **US2**: T022, T024, T026, T027 in parallel once T021 starts.  
- **US3**: T030, T033, T034 parallel after T029.  
- **Polish**: T037 and T038 parallel post-T036.

---

## Implementation Strategy

1. Deliver MVP by completing Phases 1–3 (US1) to unlock analyst-facing dashboard.  
2. Layer US2 commitment/tone capabilities to satisfy governance differentiators.  
3. Add export workflow (US3) for executive readiness.  
4. Finish with cross-cutting observability, failover, and documentation polish to meet constitutional gates.

---

## Summary Metrics

- **Total Tasks**: 40  
- **Task Distribution**: Setup 3, Foundational 5, US1 12, US2 8, US3 7, Polish 5  
- **Parallel Opportunities**: 12 tasks marked for concurrent execution  
- **MVP Scope**: Complete Phases 1–3 (Tasks T001–T020) for initial analyst workflow.
