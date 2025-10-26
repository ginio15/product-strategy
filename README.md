# Olympia BoD Analyzer — Real-World Product Definition Example

This repository showcases a complete, real-world product definition process for an AI-powered Olympia Board of Directors (BoD) Analyzer MVP. It demonstrates  AI product ownership: problem framing, governance by a product constitution, testable specifications, plan and research artifacts, traceability, and execution readiness.

Use this repo to see how a product is specified, clarified, planned, and decomposed into tasks with measurable outcomes.

## Quick Start (3-minute tour)

1. Read the executive overview in Product-docks:
   - PRD: Product intent and MVP scope →
     `Product-docks/PRD.md`
   - Vision deck: Narrative framing →
     `Product-docks/vision-deck.md`
   - MVP backlog: Scope at a glance →
     `Product-docks/MVP-backlog.md`
2. Review the governed spec and plan:
   - Feature spec →
     `specs/001-olympia-bod-analyzer/spec.md`
   - Implementation plan →
     `specs/001-olympia-bod-analyzer/plan.md`
3. Inspect diligence and readiness:
   - Research log (Phase 0) →
     `specs/001-olympia-bod-analyzer/research.md`
   - Task plan (by user story) →
     `specs/001-olympia-bod-analyzer/tasks.md`
   - Requirements quality checklists →
     `specs/001-olympia-bod-analyzer/checklists/`

## Repository Map (what to explore)

### 1) specs/001-olympia-bod-analyzer
Core, business-readable artifacts that are testable and traceable.

- `spec.md` — Feature Specification
  - Prioritized user stories, edge cases, functional and non-functional requirements
  - Measurable success criteria and explicit assumptions
  - Clarifications log captured as decisions
- `plan.md` — Implementation Plan
  - Technical context, project structure, constitution gates (privacy, evidence, accuracy, latency, explainability)
  - Phase 0 (research) and Phase 1 (design/contracts/quickstart) deliverables
- `research.md` — Phase 0 Research Log
  - Decisions, rationale, alternatives, and outstanding clarifications
- `tasks.md` — Execution Plan
  - Ordered, independently testable tasks grouped by user story with progress checkboxes
- `checklists/` — “Unit tests for requirements writing”
  - `requirements.md` (spec completeness/quality)
  - `peer-review.md` (PR-ready requirement quality gate)

### 2) .specify (method, governance, templates)
The operating system for specification work—repeatable, auditable, and toolable.

- `memory/constitution.md` — Product Constitution
  - Non-negotiable principles that govern the product (privacy, evidence-first, accuracy thresholds, latency commitments, explainability)
- `templates/` — Authoring templates
  - `spec-template.md`, `plan-template.md` (structure and standard sections)
- `.github/prompts/` (referenced by flows)
  - Command prompts that coordinate specify/clarify/plan/tasks/analyze/implement workflows

### 3) Product-docks (stakeholder-facing framing)
Narratives and artifacts for business stakeholders.

- `PRD.md` — Finalized MVP PRD (North Star, scope, success)
- `vision-deck.md` — Product vision slides (problem, value, storyline)
- `MVP-backlog.md` — High-level items and priorities

## How the process hangs together

- Governance first: The Constitution defines non-negotiables (privacy, evidence, accuracy, latency, explainability).
- Specify → Clarify → Plan → Research → Design → Tasks:
  - Specification sets testable business requirements.
  - Clarifications resolve ambiguities with documented decisions.
  - Plan translates requirements to structure and gates.
  - Research de-risks unknowns and locks decisions.
  - Design produces data models and API contracts.
  - Tasks decompose work by user story for independent delivery.

## What this demonstrates 

- Evidence-backed requirements with measurable outcomes
- Explicit scope, edge cases, and acceptance criteria
- Governance-aligned gates (constitution) used as release criteria
- Traceability from problem framing (PRD) to plans, research, and tasks
- Risk management via clarifications, assumptions, and checklists
- Delivery readiness with parallelizable, testable increments

## Status and Branch

- Active feature branch: `001-olympia-bod-analyzer`
- Phase 0 research drafted; plan/spec/checklists aligned; tasks organized by story with tracking

## Notes

- Dot-prefixed folders are visible locally for transparency but may be excluded from remote pushes via `.gitignore`.
- No proprietary content included; examples are structured for demonstration.
