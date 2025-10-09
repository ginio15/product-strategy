# 🧱 Olympia BoD Analyzer – MVP Backlog (v1.0)

## 🧭 Overview

### Goal
Build a functional prototype that compares two consecutive BoD PDFs (e.g., Westnet Q2 vs Q3) and reveals key financial, narrative, and tonal changes — faster, accurately, and verifiably.

### Success Metrics
- ⏱ Reduce BoD prep time by ≥ 60 %
- 🎯 ≥ 80 % accuracy vs manual findings
- 🔍 Catch ≥ 1 missed insight from manual review
- 💬 Executives say “This saves time and gives clarity”

## 🧩 EPIC 1 — Document Ingestion & Parsing

### Story 1.1 — PDF Upload Interface
**Description:** Allow users (analysts) to upload two BoD PDFs (current & previous quarter).

#### Tasks
- Create upload form (drag-and-drop or select file).
- Validate file type/size.
- Store temporarily on server.

#### Acceptance Criteria
- ✅ User can upload 2 PDFs (max 100 MB each).
- ✅ Upload success/failure clearly displayed.
- ✅ Files persist for current session only.

### Story 1.2 — PDF Parser & Structure Extraction
**Description:** Extract text, tables, and headings from PDFs, preserving page order and hierarchy.

#### Tasks
- Use PyMuPDF or pdfplumber for text extraction.
- Detect headings and section titles via font size/weight.
- Extract tables via Camelot or pdfplumber tables.
- Detect image-based slides via OCR (Tesseract) fallback.
- Store structured output in JSON format:
  ```json
  { "sections": [ { "title": "...", "page": 12, "content": "..." } ] }
  ```

#### Acceptance Criteria
- ✅ ≥ 90 % of textual content extracted correctly.
- ✅ Table data preserved in structured form.
- ✅ OCR fallback handles image-only slides.

## ⚖️ EPIC 2 — Section Alignment & Comparison Engine

### Story 2.1 — Section Matching
**Description:** Align semantically similar sections across two PDFs, even if names differ.

#### Tasks
- Use sentence-transformers embeddings for title similarity.
- Compute cosine similarity score.
- Match each section in Doc B with its closest in Doc A.
- Mark unmatched sections as “New” or “Removed.”

#### Acceptance Criteria
- ✅ Aligned sections ≥ 85 % accurate on test pair.
- ✅ New/Removed sections flagged correctly.

### Story 2.2 — Text & Table Diff
**Description:** Detect and highlight textual changes and numeric variances between matched sections.

#### Tasks
- Implement sentence-level diff with difflib or token-based diff.
- Detect number patterns and compute % changes.
- Format diffs with color codes:
  - 🟩 Added text
  - 🟥 Removed text
  - 🟧 Tone change
- For tables, align by header labels and compute deltas.

#### Acceptance Criteria
- ✅ Numeric deltas correctly computed for ≥ 80 % of table cells.
- ✅ Visual diff output JSON includes color tags.
- ✅ Unmatched numbers or formats flagged gracefully.

## 🧠 EPIC 3 — NLP Analysis & Narrative Generation

### Story 3.1 — Commitment Tracker
**Description:** Extract commitments (“we will”, “target”, “plan to”) and compare across reports.

#### Tasks
- Regex + GPT-based extraction for future-tense statements.
- Match commitments to follow-up mentions.
- Classify status: Achieved / Ongoing / Dropped.

#### Acceptance Criteria
- ✅ Top 10 commitments detected accurately.
- ✅ Each labeled with status + citation (slide/page).

### Story 3.2 — Tone & Confidence Shift Detection
**Description:** Highlight softened or strengthened language between reports.

#### Tasks
- Compare modal verbs and adverbs (will → may, certain → possible).
- Use GPT-4-Turbo for contextual tone tagging.
- Color-code tone changes in output.

#### Acceptance Criteria
- ✅ Tone changes detected at sentence level.
- ✅ Visual highlight (🟧) appears in diff view.
- ✅ No false positives above 30 %.

### Story 3.3 — Per-Section Narrative Generation
**Description:** Generate concise summaries describing what changed in each aligned section.

#### Tasks
- Prompt LLM with section pair + extracted diffs.
- Constrain output to 2–3 sentences.
- Include quantitative + qualitative insights.

#### Acceptance Criteria
- ✅ Summaries cover numeric + narrative change.
- ✅ Citations to source sections included.
- ✅ Readable within 10 seconds per section.

## 💻 EPIC 4 — Web Dashboard & User Interface

### Story 4.1 — Dashboard Layout (Option C)
**Description:** Design two-pane layout: summary (top) + side-by-side diff (bottom).

#### Tasks
- Build React + Tailwind UI.
- Top: Accordion or tab per section → shows AI summary.
- Bottom: Side-by-side viewer with color-coded highlights.
- Include “Next Section / Previous Section” navigation.

#### Acceptance Criteria
- ✅ Clean, minimalist executive-style interface.
- ✅ Color codes consistent with backend diff.
- ✅ Responsive for desktop/tablet.

### Story 4.2 — Citations & Evidence
**Description:** Provide clickable references linking each insight to its slide/page.

#### Tasks
- Add tooltip or side label showing “Page 12 | Section: Financials”.
- Anchor each highlight to a page number.
- Store mapping in metadata JSON.

#### Acceptance Criteria
- ✅ Each highlight includes a valid citation.
- ✅ Clicking citation scrolls to relevant text/slide.

### Story 4.3 — Export “BoD Meeting Brief”
**Description:** Generate printable summary report (PDF) combining narratives, variances, and highlights.

#### Tasks
- Use ReportLab or WeasyPrint to export summary.
- Include metadata (date, docs compared, total findings).
- Optional executive summary at top.

#### Acceptance Criteria
- ✅ PDF exports cleanly formatted.
- ✅ All key metrics and summaries included.
- ✅ Download under 10 s.

## 🧪 EPIC 5 — Validation & Testing

### Story 5.1 — Pilot Evaluation
**Description:** Test MVP on the Westnet July → Nov 2024 decks.

#### Tasks
- Analysts validate AI findings vs manual results.
- Record accuracy metrics and missing insights.
- Gather qualitative feedback.

#### Acceptance Criteria
- ✅ ≥ 80 % match rate with manual findings.
- ✅ ≥ 1 new insight discovered by system.
- ✅ Analyst prep time reduced ≥ 60 %.

## 🚀 EPIC 6 — Deployment & Setup

### Story 6.1 — Containerization
**Tasks**
- Create Dockerfile for backend + frontend.
- Include environment variables for API keys.
- Ensure local file storage isolation.

#### Acceptance Criteria
- ✅ Single command deploys full stack locally.
- ✅ PDF data never leaves container.

### Story 6.2 — Configuration & API Keys
**Tasks**
- Add .env for OpenAI / Anthropic API keys.
- Fallback mode for offline testing (mock responses).

#### Acceptance Criteria
- ✅ Configurable without code changes.
- ✅ Fails gracefully without internet.

## 📆 Sprint Plan (Approx. 8 Weeks)

| Sprint | Duration   | Focus                     | Key Output                          |
|--------|------------|---------------------------|-------------------------------------|
| 1      | Weeks 1–2 | Parsing + Upload          | Working PDF parser, upload UI      |
| 2      | Weeks 3–4 | Comparison Engine         | Section alignment + numeric/tone diffs |
| 3      | Weeks 5–6 | NLP + UI Integration      | Narrative summaries, dashboard functional |
| 4      | Week 7    | Export + Testing          | BoD brief export, pilot feedback   |
| 5      | Week 8    | Refinement                | Accuracy tuning, bug fixes, presentation demo |

## 🔐 Non-Goals (MVP)
- Multi-quarter timeline memory
- Portfolio-wide analytics
- Integration with Outlook/Teams
- Advanced RBAC and user management

## 🏁 Definition of Done
- MVP fully runs locally or in private cloud via Docker.
- User uploads two PDFs, views comparisons, reads summaries, and exports a brief.
- Tested successfully on Westnet decks.
- Achieves baseline metrics for accuracy and time savings.

**End of BACKLOG.md**  
*(Version 1.0 — Prepared October 2025 for Olympia Group / Westnet Pilot)*
