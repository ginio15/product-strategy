<!--
Sync Impact Report
- Version change: — → 1.0.0
- Modified principles: N/A (initial draft)
- Added sections: Operational Constraints; Delivery Workflow & Quality Gates; Governance guidance populated
- Removed sections: None
- Templates requiring updates: ✅ .specify/templates/plan-template.md (no change required); ✅ .specify/templates/spec-template.md (no change required); ✅ .specify/templates/tasks-template.md (no change required); ✅ .specify/templates/checklist-template.md (no change required); ⚠ .specify/templates/commands (directory absent — create command templates that reference governing gates)
- Follow-up TODOs: None
-->

# Olympia BoD Analyzer Constitution

## Core Principles

### Principle 1 — Confidential Data Stays Local
- All Board decks, parsed assets, embeddings, and outputs MUST remain within Olympia-controlled
	infrastructure; no third-party logging or telemetry is allowed.
- Processing pipelines MUST encrypt data at rest and in transit inside the environment and purge
	transient artifacts within 24 hours of session completion.
- Any new integration MUST include a privacy impact statement recorded in the Execution Plan before
	Phase 0 work begins.

**Rationale**: Portfolio decks contain strategic and financial information whose leakage would cause
immediate harm; the product exists to accelerate insight without compromising secrecy.

### Principle 2 — Evidence-First Insights
- Every surfaced insight MUST cite its source slide ID, page number, and content hash so reviewers
	can verify claims instantly.
- Numeric variances MUST be reproducible from the structured extraction layer (JSON tables), and the
	system MUST display raw figures alongside computed deltas.
- Narrative summaries MUST embed hyperlinks or hover metadata that return analysts to the canonical
	evidence within two clicks.

**Rationale**: The Board demands verifiable differences; evidence-first outputs prevent speculation
and reduce reconciliation time.

### Principle 3 — Accuracy & Coverage Guarantees
- Extraction accuracy MUST maintain ≥90% coverage for text content and ≥80% correctness for table
	cell values on regression samples; when thresholds fall below limits, releases are blocked.
- Diff and narrative modules MUST expose automated tests that measure accuracy against reference
	decks before every merge.
- Commitments, tone shifts, and omissions MUST carry confidence scores; anything below 0.6 MUST be
	flagged for manual review rather than auto-promoted.

**Rationale**: The product promises ≥80% alignment with manual findings; explicit thresholds and
automated checks enforce that contract.

### Principle 4 — Time-to-Insight Commitment
- The end-to-end comparison workflow MUST deliver a review-ready dashboard in ≤15 minutes for two
	100-page decks on the reference hardware baseline specified in the plan.
- UI interactions (section switch, citation reveal) MUST respond within 3 seconds at p95; teams MUST
	profile and document hotspots in the plan’s Technical Context.
- Background jobs MAY queue, but user-facing progress indicators MUST make latency transparent and
	provide fallback exports when time limits risk breach.

**Rationale**: The analyzer exists to cut preparation time by ≥60%; performance obligations keep that
promise measurable and defensible.

### Principle 5 — Explainable AI Workflow
- LLM-generated narratives MUST include structured metadata: cited sections, numeric references, and
	model configuration, stored alongside the output for audit trails.
- Prompt templates and guardrails MUST be version-controlled; any change requires regression runs and
	reviewer sign-off captured in the plan checklist.
- The UI MUST expose why tone or confidence tags were applied, including the linguistic patterns or
	modal verb shifts detected.

**Rationale**: Board members will only trust AI insights when they understand why conclusions were
reached and can trace them back to deterministic signals.

## Operational Constraints

- Maintain bilingual (Greek/English) extraction support; fallback OCR MUST be invoked automatically
	on image-heavy slides.
- Financial calculations MUST align with IFRS formatting; currency conversions MUST reference the
	rate logged in plan.md for the reporting period.
- Data retention policies MUST be documented in each deployment’s runbook; default retention is
	30 days for aggregated metrics, 0 days for raw decks.
- Security reviews MUST be recorded before connecting to any external LLM endpoint; offline mode is
	the default and MUST remain viable.

## Delivery Workflow & Quality Gates

- Constitution Check (Plan Phase 0) MUST confirm: privacy impact documented, evidence citation
	mechanism specified, accuracy test suite enumerated, and performance baseline declared.
- Every specification MUST decompose user stories so each represents a 15-minute-review deliverable
	slice, enabling independent validation as codified in the tasks template.
- Task lists MUST group work by user story and include explicit test or evaluation tasks enforcing
	accuracy, citation completeness, and performance sampling.
- Before shipping, teams MUST run: extraction regression tests, diff accuracy evaluation, UI latency
	smoke test, and export integrity check; results MUST be linked in the release checklist.

## Governance

- This constitution supersedes conflicting process documents; all plans, specs, and tasks MUST
	reference these principles during their Constitution Check stage.
- Amendments require consensus from the product lead, engineering lead, and data/privacy steward,
	documented in the repository with rationale and expected impact on accuracy, latency, or privacy.
- Versioning follows semantic rules: MAJOR for principle overhaul, MINOR for new enforceable clauses,
	PATCH for clarifications; each amendment MUST update the Sync Impact Report.
- Compliance reviews occur quarterly and after every pilot; findings MUST be logged with remediation
	tasks tracked via the tasks template.

**Version**: 1.0.0 | **Ratified**: 2025-10-09 | **Last Amended**: 2025-10-09