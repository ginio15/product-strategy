# Feature Specification: Olympia BoD Analyzer MVP Comparison Core

**Feature Branch**: `001-olympia-bod-analyzer`  
**Created**: 2025-10-09  
**Status**: Draft  
**Input**: User description: "Olympia BoD Analyzer MVP compares two consecutive BoD PDFs (e.g., Westnet July vs November 2024) to extract, align, and summarize financial, narrative, tone, and commitment changes with citations and exportable brief."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Rapid Deck Comparison for Analysts (Priority: P1)

An Olympia analyst uploads the latest and previous BoD decks for a portfolio company and receives a
dashboard that highlights the most material differences (financial, narrative, tone) within minutes,
each backed by citations.

**Why this priority**: Without fast, trustworthy diffs, analysts cannot cut prep time or surface
board-ready insights, undermining the MVP’s core promise.

**Independent Test**: Upload two validated reference decks and confirm the dashboard lists top
changes with citations, allowing an analyst to complete review without additional tooling.

**Acceptance Scenarios**:

1. **Given** two valid PDF decks for the same company, **When** the analyst uploads them, **Then**
   the system produces a dashboard summarizing financial variances, narrative shifts, and tone
   changes with slide/page citations.
2. **Given** the generated dashboard, **When** the analyst expands a section, **Then** they see a
   concise summary plus highlighted excerpts and metrics supporting the change.

---

### User Story 2 - Commitment & Tone Accountability (Priority: P2)

An Olympia board member reviews how previously stated commitments evolved and whether management’s
language became more or less confident, with each item linked to original and current slides.

**Why this priority**: Tracking delivery promises and confidence shifts is critical for governance
decisions and is a differentiator versus manual review.

**Independent Test**: Generate the commitment/tone panel from the same deck pair and verify that it
lists commitments with status (Achieved/Ongoing/Dropped) and highlights softened or strengthened
language, each referencing original and current slides.

**Acceptance Scenarios**:

1. **Given** commitments extracted from the prior deck, **When** the board member filters by
   "Dropped", **Then** they see items marked as dropped with the updated narrative and citation.
2. **Given** tone analysis results, **When** the board member hovers over a highlighted sentence,
   **Then** the system displays the linguistic change (e.g., “will” → “aim to”) and source slide
   metadata.

---

### User Story 3 - Exportable Executive Brief (Priority: P3)

An Olympia executive downloads a one-page brief that distills the dashboard’s findings into an
offline-ready summary containing key metrics, risks, and recommended questions.

**Why this priority**: Executives often prepare on the move; an export ensures insights travel
outside the web app while preserving traceability.

**Independent Test**: Trigger the export with the reference decks and confirm the PDF brief compiles
top insights, variances, and questions, all mapped to citations, without accessing the live
dashboard.

**Acceptance Scenarios**:

1. **Given** a completed analysis, **When** the executive requests an export, **Then** the system
   generates a brief summarizing key changes, status of commitments, and unresolved risks with slide
   references.
2. **Given** the exported brief, **When** the executive scans the recommended questions section,
   **Then** each question references the insight and slide that triggered it.

---

### Edge Cases

- Decks contain mixed languages (Greek/English) or image-only slides requiring OCR fallback.
- Uploaded decks differ in section order or naming, causing partial matches.
- One deck omits a previously present section entirely.
- Numeric tables use inconsistent currency or number formats between quarters.
- Extraction confidence falls below the 0.6 threshold for commitments or tone detection.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The system MUST allow analysts to upload exactly two PDF decks (current and previous)
  for the same portfolio company per session without imposing a hard file-size limit; uploads rely on
  available storage, with validation focused on file integrity rather than size.
- **FR-002**: The system MUST parse text, tables, and image-based content, producing structured data
  that preserves page and section hierarchy for ≥90% of content.
- **FR-003**: The system MUST align semantically equivalent sections across decks and flag sections
  that are new or removed when no suitable match exists.
- **FR-004**: The system MUST calculate financial variances (absolute and percentage) for all
  comparable numeric values and display raw figures alongside computed deltas.
- **FR-005**: The system MUST detect commitments expressed in the earlier deck, determine their
  follow-up status, and label each as Achieved, Ongoing, or Dropped with citations.
  Commitment and tone insights require a minimum confidence score of 0.95 to be auto-promoted; lower scores are flagged for manual review.
- **FR-006**: The system MUST detect tone changes by identifying modal verb or confidence language
  shifts and present them with contextual highlighting and metadata.
- **FR-007**: The system MUST surface omissions by listing topics or sections present previously that
  are absent in the latest deck.
- **FR-008**: The system MUST generate per-section narratives that blend quantitative and qualitative
  findings in ≤3 sentences, each including references to supporting evidence.
- **FR-009**: The system MUST provide an exportable PDF brief summarizing key metrics, risks,
  commitments, and proposed board questions with traceable citations. Offline exports require citations alone; evidence snapshots are not embedded.
- **FR-010**: The system MUST enforce privacy controls ensuring uploaded decks, parsed outputs, and
  generated insights remain within Olympia-controlled storage with no automated purge window; any user may trigger manual deletion per Olympia retention policy.
- **FR-011**: Citations MUST render as clickable "Slide X" links that deep-link to the exact
  location in the referenced deck within both the dashboard and exported brief.
- **FR-012**: The MVP MAY operate with a shared analyst login; dedicated authentication or role-based differentiation for board members and executives is out of scope for the MVP and will be addressed in future releases.
- **FR-013**: If the primary GPT-4 Turbo API is unavailable, the system MUST fail over to a secondary
  OpenAI endpoint/model automatically and surface status to the user.

### Non-Functional Requirements

- **NFR-001**: Observability MUST capture accuracy percentages, p95 latency for pipeline stages, and
  deletion audit logs while explicitly excluding raw deck content or PII from logs/metrics.

### Key Entities *(include if feature involves data)*

- **Deck Pair**: Represents the current and previous BoD decks under comparison; stores metadata such
  as company, time period, upload timestamps, and processing status.
- **Section Alignment**: Captures matched sections between decks, including similarity scores, page
  references, and alignment status (Matched/New/Removed).
- **Insight**: Encapsulates a surfaced finding (financial variance, commitment status, tone change,
  omission) with descriptive text, quantitative metrics, confidence score, and citations.
- **Commitment Record**: Tracks a promise detected in the prior deck, its follow-up evidence, and the
  resulting status label plus rationale.
- **Export Brief**: Summarizes selected insights into an ordered collection suitable for PDF export,
  including metadata such as generation time and authoring analyst.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- The system processes and summarizes two 80-page decks in under 5 minutes per comparison session.
- For the initial pilot, at least one complete deck pair is analyzed end-to-end, with all core features exercised.
- All auto-promoted commitment and tone insights achieve a minimum confidence score of 0.95; lower-confidence items are flagged for manual review.
- The dashboard surfaces at least three material changes per deck pair that were not previously identified in manual analyst prep during pilot feedback.
- Analyst surveys confirm a reduction in prep time of at least 60% compared to baseline manual workflows.


## Assumptions & Dependencies

- Analysts provide consecutive decks for the same company and confirm the reporting periods before
  upload.
- Reference hardware and environment follow the baseline defined in the implementation plan to meet
  performance targets.
- Bilingual extraction support (Greek/English) and OCR models are pre-configured and validated.
- Privacy and security reviews are completed prior to enabling any optional external integrations.
- Citations exposed to users follow "Slide X" labeling and must remain clickable even in offline
  exports.
- Storage capacity planning MUST assume no enforced PDF size ceiling during upload.

## Clarifications

### Session 2025-10-10

- Q: What citation format must every insight use in the UI and exports? → A: Clickable "Slide X" link to exact slide
- Q: Select the observability bundle the MVP must capture. → A: Log accuracy %, latency p95, deletion audit logs (no raw data)
- Q: Do analysts, board members, and executives require distinct logins/roles? → A: No
- Q: If GPT-4 API is unavailable mid-run, what should the system do? → A: Use alternate OpenAI model
- Q: What’s the max file size allowed per uploaded PDF? → A: No limit
- Q: What is the mandatory purge window for uploaded decks and all derived artifacts? → A: No purge window
- Q: What file format must the exportable brief use? → A: PDF
- Q: What is the minimum confidence score required for commitment/tone insights to be auto-promoted (not flagged for manual review)? → A: 95%
- Q: For offline exports, must evidence snapshots (cropped images of cited content) be embedded, or are citations alone sufficient? → A: Citations alone are sufficient
