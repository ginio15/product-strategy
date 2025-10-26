# Checklist — Olympia BoD Analyzer MVP Requirements Peer Review

**Purpose**: Validate that all MVP requirements (US1–US3 plus non-functional mandates) are complete, clear, and internally consistent before peer review.

**Created**: 2025-10-10

**Depth**: Peer review gate (Option B)

**Scope**: Full MVP coverage, including constitutional non-functional obligations.

---

## Requirement Completeness

- [ ] CHK001 Are upload session requirements explicit about shared authentication, two-deck constraint, and manual deletion triggers? [Completeness, Spec §FR-001; Spec §FR-010]
- [ ] CHK002 Do requirements enumerate how omissions are surfaced across alignment, dashboard, and export outputs? [Completeness, Spec §FR-003; Spec §FR-007; Spec §User Story 1]

## Requirement Clarity

- [ ] CHK003 Are accuracy thresholds (≥90% text, ≥80% numeric) mapped to concrete validation steps and datasets? [Clarity, Spec §FR-002; Spec §Success Criteria]
- [ ] CHK004 Is the definition of commitment statuses (Achieved/Ongoing/Dropped) described with decisive business rules? [Clarity, Spec §FR-005]

## Requirement Consistency

- [ ] CHK005 Is the 0.95 confidence rule consistent across commitments, tone, and success metrics? [Consistency, Spec §FR-005; Spec §Success Criteria]
- [ ] CHK006 Are citation format expectations identical for dashboard and PDF brief outputs? [Consistency, Spec §FR-011; Spec §FR-009; Spec §Clarifications]

## Acceptance Criteria Quality

- [ ] CHK007 Do US1 acceptance scenarios specify measurable outputs (e.g., top changes with citations) sufficient for pass/fail determination? [Acceptance Criteria, Spec §User Story 1]
- [ ] CHK008 Are success metrics (e.g., prep-time reduction, pilot deck counts) tied to verifiable measurement procedures? [Acceptance Criteria, Spec §Success Criteria]

## Scenario Coverage

- [ ] CHK009 Are requirements covering scenarios where sections are missing or newly introduced across decks? [Coverage, Spec §FR-003; Spec §FR-007; Spec §Edge Cases]
- [ ] CHK010 Are failover behaviors described for both backend processing and UI disclosure when GPT-4 is unavailable mid-run? [Coverage, Spec §FR-013; Plan §Constraints]

## Edge Case Coverage

- [ ] CHK011 Are multilingual and image-only slide handling expectations defined alongside OCR fallback quality bars? [Edge Case Coverage, Spec §FR-002; Spec §Edge Cases]
- [ ] CHK012 Are currency and number-format inconsistencies addressed with explicit conversion or normalization requirements? [Edge Case Coverage, Spec §FR-004; Spec §Edge Cases]

## Non-Functional Requirements

- [ ] CHK013 Do requirements specify how observability metrics (accuracy %, latency p95, deletion logs) are captured without storing raw deck data? [Non-Functional, Spec §NFR-001; Plan §Performance Goals]
- [ ] CHK014 Are performance targets (≤15 min pipeline, ≤3s UI, ≤30s export) linked to monitoring and acceptance gates? [Non-Functional, Spec §Success Criteria; Plan §Performance Goals]

## Dependencies & Assumptions

- [ ] CHK015 Are assumptions about reference hardware, pre-configured OCR models, and bilingual support validated or tracked? [Dependencies, Spec §Assumptions & Dependencies; Plan §Technical Context]
- [ ] CHK016 Are privacy impact documentation and manual deletion audit steps captured as preconditions? [Dependencies, Spec §FR-010; Plan §Phase 0]

## Ambiguities & Conflicts

- [ ] CHK017 Is the shared-login scope clearly limited to the MVP with explicit deferment of role-based access? [Ambiguity, Spec §FR-012]
- [ ] CHK018 Are offline export expectations (citations-only vs. evidence snapshots) unambiguous across requirements and clarifications? [Ambiguity, Spec §FR-009; Spec §Clarifications]
