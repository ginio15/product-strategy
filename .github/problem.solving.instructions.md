
---
description: Framework for GitHub Copilot problem-solving inside this repository (independent of Codacy)
applyTo: '**'
---

# GitHub Copilot Problem-Solving & Execution Framework
> Purpose: Provide a lightweight, disciplined, iterative approach so Copilot delivers correct, simple, maintainable solutions quickly-especially with incomplete context.

## 1. Core Principles
1. Bias for Simplicity: Prefer the smallest coherent change that solves the immediate requirement.
2. Context Before Action: Never modify or generate expansive code without first gathering/confirming relevant context.
3. Explicit Assumptions: If something is unknown and not critical—declare the assumption inline (`ASSUMPTION:`) and proceed. If critical—ask once, clearly.
4. Iterative Refinement: Try → Observe → Adjust model/assumptions → Retry from a new angle.
5. Multi-Angle Thinking: For hard problems, deliberately enumerate alternative hypotheses/attack paths (at least 2) before diving deep.
6. Evidence > Intuition: Reference concrete file locations, error traces, test failures, or logs before concluding.
7. Non‑Persistence of Error: A failed attempt must change something (data gathered, assumption revised, strategy updated) before re‑attempting.
8. Avoid Over‑Engineering: Defer abstractions until duplication/volatility justifies them.
9. Local First: Prefer local utilities, existing patterns, and current dependencies before introducing new ones.
10. Reversibility: Prefer solutions that are easy to revert or adjust if new constraints appear.
11. Risk Minimization: Plan and execute in a way least likely to break existing behavior—probe impact first, then change narrowly.
12. Probabilistic Reasoning: Treat debugging & failure analysis as ranking hypotheses by likelihood; gather evidence to re-rank.
13. Small Cohesive Commits: Each logical change should be atomic and testable.
14. Preserve Invariants: Identify and protect critical data and behavioral invariants before edits.
15. Tests as Contract: Never knowingly reduce existing test coverage without explicit rationale.

## 2. Standard Problem-Solving Loop
```
1. Clarify Goal -> State success criteria (bullet list)
2. Gather Context -> Read/search only what’s needed (APIs, tests, related modules)
3. Identify Constraints -> Performance? Security? Interfaces? External contracts?
4. List Approaches -> A (baseline), B (alternative), C (fallback) – keep concise
5. Choose Minimal Viable Approach (justify briefly)
6. Implement in Small Steps -> Validate after each meaningful change
7. Run Quality Gates (lint/tests/build) -> Interpret results
8. If Failure -> Capture error, classify (logic, environment, dependency, assumption)
9. Adjust Strategy/Assumptions -> Loop back to step 5 or 2 as necessary
10. Summarize Outcome -> Map to success criteria
```

## 3. Context Acquisition Checklist
Use only what’s relevant; stop when diminishing returns:
- Search for symbol/function/class names you plan to touch.
- Read related tests first (they define expected behavior).
- Check configuration (env, settings, dependency manifests) if behavior seems indirect.
- Inspect failure output verbatim—don’t paraphrase until recorded.

## 4. Assumption Management
- Mark inline: `# ASSUMPTION: <statement>` when coding.
- Replace assumptions with facts as soon as verified.
- If an assumption cascades into a design decision, document a micro-note near the change.

## 5. Debugging Protocol (Deep & Multi-Perspective)
When stuck:
1. Reproduce Minimally (strip to smallest failing input or path).
2. Classify Failure Type: (interface mismatch | state mutation | race | data shape | environment | dependency | performance).
3. Generate At Least 2 Hypotheses.
4. Design Fast Discriminators (tiny prints, focused test, assert, or isolated snippet).
5. Run discriminators → Eliminate hypotheses.
6. Escalate Tools: tracing/logging → instrumentation → binary search through commits → dependency isolation.
7. Record: `CAUSE:` once root found; add preventative test if feasible.

## 6. Perspective Shift Prompts
Use these to break fixation:
- What invariant should hold here—and does code enforce it?
- What if the input is empty, None, or extreme size?
- Could caching/state retention be leaking across tests?
- Are time, locale, randomness, or ordering affecting outcome?
- Did a silent exception or swallowed error occur earlier?

## 7. Decision Microformat
When making a non‑obvious call, add a brief block (comment or PR note):
```
DECISION: <summary>
ALTERNATIVES: A(why rejected), B(why rejected)
TRADEOFF: <risk vs benefit>
REVIST IF: <condition>
```

## 8. When To Ask the User
Ask only if ALL are true:
- Critical ambiguity blocks correctness.
- Not inferable from patterns in current code/tests.
- A reasonable assumption would risk rework or data loss.
Else: proceed with explicit assumption.

## 9. Anti‑Patterns (Avoid)
- Premature abstraction / generic frameworks for one-off logic.
- Large unvalidated batch edits.
- Silent swallowing of exceptions during exploration.
- Re-running full test suite repeatedly when a narrow test suffices.
- Adding dependencies to "solve" trivial utility logic.

## 10. Performance & Efficiency Guardrails
- First: Confirm performance is actually a user-visible problem (metrics or reproducible slowness).
- Prefer algorithmic clarity over micro-optimizations.
- Introduce caching only after measuring (note scope + invalidation rule).
- For DB/IO: batch where practical; avoid N+1 patterns—add a test to freeze improved pattern.

## 11. Quality Gates (Always Before Declaring Done)
- Syntax / Lint clean (if tooling present).
- All affected tests pass; add at least one test if behavior changed or bug fixed.
- No dead/commented-out experimental code left behind.
- Public interface changes documented (README / docstring / CHANGELOG if exists).
 - (If type hints present) No new type errors; add/adjust annotations for new public functions.

## 12. Failure Handling Contract
If an attempt fails:
```
LOG: exact error or mismatch
DIAGNOSIS: current best hypothesis
DELTA CHANGE: what’s different next attempt
```
Never retry identical conditions expecting different outcome.

## 13. Minimal Test Addition Heuristic
Add a test when:
- Fixing a bug (test reproduces prior failure).
- Adding a branch in logic not previously covered.
- Introducing an assumption about edge-case input.
Tests should:
- Be short
- Assert one main behavior
- Use descriptive names reflecting intent

## 14. Documentation Light-Touch
Only add docs proportionate to complexity:
- One-liner docstring for new public functions.
- Comment only for non-obvious invariants or rationale—avoid restating code.

## 15. Security & Safety Mindset
- Treat input as untrusted unless validated upstream—validate shape & type early.
- Avoid leaking secrets (never echo env vars in logs).
- Fail fast on invariant violations with clear messages.

## 16. Escalation Ladder (If Still Blocked)
1. Re-run minimal reproduction confirming still failing.
2. Re-check assumptions list—remove or validate one.
3. Fresh search for related symbols (maybe missed alt path).
4. Isolate module in a scratch harness.
5. Bisect recent changes (if regression).
6. Request user clarification (present distilled findings + options).

## 17. Output & Reporting Style
When responding:
- Start with current goal & status.
- Provide only deltas since last action.
- Map actions -> success criteria explicitly at end.

## 18. Example Lightweight Flow (Template)
```
GOAL: Implement X
SUCCESS CRITERIA: a,b,c
CONTEXT GATHERED: files A,B; found existing util C
PLAN: Step1 small change -> Step2 test -> Step3 refine
ACTION: <what was done>
RESULT: <tests/output>
NEXT: <tight next step>
```

## 19. Refactoring Trigger Points
Refactor only when one of:
- Same logic duplicated 3+ times.
- Function exceeds clear cognitive chunk (subjective but ~40-60 LOC unless algorithmic).
- Invariant enforcement scattered.
- Naming no longer matches responsibility.

## 20. Done Definition
A task is Done when:
- Success criteria satisfied.
- Quality gates passed.
- Assumptions either validated or explicitly documented.
- No open diagnostics/hypotheses remain.
- User-facing change (if any) communicated.

## 21. Tool & Workspace Access Permissions
Copilot operating under this framework is explicitly authorized to:
- Proactively invoke ANY available workspace tools (file search, semantic search, file reads, edits, test runs, linters, analyzers, terminal tasks) whenever they materially advance progress—no need to wait for explicit user re-authorization each time.
- Access and read ANY file in the repository at any time for context gathering (treat potential secrets cautiously; never echo raw secret values back unless user explicitly asks and risk is stated).
- Perform small, reversible edits that align with declared goals; for broader refactors, summarize intent first.
- Batch multiple read-only tool calls when exploring; state a concise purpose before the batch.

Environment initialization (new project or fresh backend setup):
1. Detect if a virtual environment exists (`.venv/`, `env/`, or Poetry/uv config). If not present, create one before ANY dependency installation or code generation.
2. Recommended Python flow:
	- `python -m venv .venv` (or `uv venv` / `python -m uv venv` if uv is standard here)
	- Activate and record Python version.
	- Generate or update `requirements.txt` only with minimal, justified additions.
3. Verify environment by importing critical packages (e.g., FastAPI, SQL-related libs) via a short snippet.
4. Log (in summary) the environment actions taken; avoid repeating if already done.

Tool usage guardrails:
- Always state a one-line purpose before a batch of tool calls.
- Avoid redundant re-reads of unchanged files—cache mental model and only re-open if something changed.
- Prefer targeted test runs over full-suite runs when iterating.
- If a tool fails unexpectedly, classify (transient vs structural) and retry once with adjusted parameters before escalating.

Failure to solve a problem must trigger a change in: gathered evidence, assumptions, or the chosen tactic—never identical retries.

## 22. Safe Change Strategy (Least-Likelihood-of-Breakage Execution)
1. Classify Change Type: (bug fix | feature addition | refactor | infra/config | data migration).
2. Determine Blast Radius: Which modules, DB tables, endpoints, or UI components could be impacted? List them briefly.
3. Baseline Behavior: Run/inspect only the most relevant tests & (optionally) a quick smoke (e.g., key endpoint) BEFORE modifying code.
4. Choose Minimal Surface: Prefer adding code alongside existing logic (feature-flag or conditional path) before rewriting.
5. Preserve Interfaces: Avoid signature changes unless required; if changed, update all call sites + tests in same pass.
6. Add Before Remove: Introduce new structure, switch usage, then remove obsolete code after confidence.
7. Validate Incrementally: After each discrete edit, re-run only the smallest meaningful test subset.
8. Rollback Plan: Ensure each commit/patch can be reverted without side-effects (no partial migrations without guards).
9. Migration Safety: Make data/schema migrations idempotent & backward-compatible (add columns nullable first, then enforce).
10. Logging & Metrics: Add temporary diagnostic logging only if required for investigation; remove once solved.

## 23. Probabilistic Debugging & Diagnosis
Maintain an ordered hypothesis list with an approximate confidence (qualitative: High/Med/Low or % estimate). Process:
1. Enumerate 2–5 plausible causes quickly (avoid rabbit hole on first guess).
2. Rank by Prior Probability (based on recent edits, flaky dependencies, historical issues, complexity hotspots).
3. Design the Lowest-Cost Discriminator (fast test, print/assert, isolated snippet) targeting the top hypothesis.
4. Execute → Update probabilities (raise/lower or eliminate) and record DELTA rationale.
5. If all High eliminated, broaden search (include environment, race conditions, stale caches, test pollution).
6. Stop when one hypothesis exceeds ~80% confidence and evidence is concrete; label `CAUSE:` and implement fix + prevention test.
7. After fix, run the failing scenario first, then a narrow regression set, then (if risk warranted) a broader suite.

Suggested Probability Triggers:
- High (≥60%): Investigate immediately.
- Medium (25–59%): Queue after Highs or batch with another discriminator.
- Low (<25%): Defer unless all higher eliminated.

## 24. Coding Best Practices & Methods
General:
- Prefer explicitness over cleverness; code should be obvious to a future maintainer.
- Keep functions cohesive; if doing more than one conceptual task, split.
- Favor pure / side-effect-light helpers where feasible; isolate IO at boundaries.
- Input validation early; fail fast with actionable error messages.
- Never silently ignore exceptions—either handle meaningfully or propagate.
- Avoid broad `except Exception:` unless adding context then re-raising.

State & Data:
- Minimize mutable shared state; pass explicit parameters.
- Document invariants where corruption would be costly.
- Use immutable structures for configuration constants.

Testing:
- Write the regression test first for a bug (Red → Green → Refactor).
- Prefer deterministic tests: control time, randomness, external services via injection/mocks.
- Name tests by behavior (`test_archiver_handles_empty_input`) not implementation.

Dependencies:
- Introduce new dependency only if: (a) materially reduces complexity, (b) well-maintained, (c) license compatible.
- Avoid “utility sprawl”—prefer extending existing local helpers.

Performance:
- Measure before optimizing; add micro-bench (optional) for hotspots.
- Replace O(n^2) with O(n log n) only when inputs plausibly large or evidence of slowness.

Logging & Observability:
- Use appropriate levels (DEBUG for dev detail, INFO for lifecycle events, WARNING for recoverable anomalies, ERROR for failures requiring attention).
- No secrets (keys, tokens, PII) in logs.
- Correlate multi-step flows with an id if already supported.

Error Handling Pattern:
```
try:
	critical_op()
except SpecificError as e:
	# add context, maybe transform
	raise DomainError(f"<context>: {e}") from e
```

Refactoring Guidelines:
- Only refactor adjacent to touched code (opportunistic) unless a dedicated refactor task.
- Defer large restructures until pain is recurring & documented.

Documentation:
- Add/adjust docstrings for new public-facing functions/classes (purpose, params, returns, side-effects).
- Use comments to explain WHY, not WHAT.

Security:
- Treat external input as untrusted; validate type, range, and format.
- Sanitize/escape where rendering or executing dynamic content.

Git / Commit Hygiene:
- Commit message: <scope>: <imperative summary>. Body includes rationale if non-trivial.
- Avoid mixing unrelated changes in one commit.

## 25. Progressive Risk Assessment (Before Executing a Plan)
Before implementing, quickly rate:
```
RISK VECTOR      LEVEL    MITIGATION
Data Integrity   Low/Med/High   (tests, backups, migration dry-run)
API Contract     Low/Med/High   (contract tests, versioning)
Performance      Low/Med/High   (add timing probe)
Security         Low/Med/High   (input validation, auth checks)
Maintainability  Low/Med/High   (avoid premature abstraction)
```
If any vector ≥ High, narrow scope or introduce a guard (feature flag / toggle) before rollout.

## 26. Quick Ops Checklist (Fast Reference)
Use this when executing a typical change/fix. Skip items that are clearly N/A.

Planning:
- [ ] Define goal + success criteria (2-5 bullets)
- [ ] Identify change type (bug, feature, refactor, infra, data)
- [ ] List potential blast radius (modules/endpoints/db tables)
- [ ] Choose minimal viable approach

Context:
- [ ] Read related tests
- [ ] Scan symbols to be modified
- [ ] Confirm invariants / contracts

Environment (new project / missing venv):
- [ ] Ensure virtual environment exists
- [ ] Minimal dependencies only

Implementation Loop (repeat small steps):
- [ ] Make atomic edit
- [ ] Run targeted tests / lint
- [ ] Update hypothesis list if failing

Before Commit:
- [ ] All modified tests pass
- [ ] Added regression/edge test if needed
- [ ] No dead code or debug prints
- [ ] Public interface changes documented

Safety:
- [ ] Migration (if any) backward-compatible
- [ ] Feature flag or reversible path (if high risk)

After Fix/Feature:
- [ ] Verify success criteria
- [ ] Summarize decisions & assumptions
- [ ] Remove temporary logging

## 27. Machine-Readable Summary (For Automation)
The following JSON block summarizes key operational rules for tooling integration.

```json
{
	"version": 1,
	"sections": {
		"core_principles": [
			"simplicity_first",
			"context_before_action",
			"explicit_assumptions",
			"iterative_refinement",
			"multi_angle_thinking",
			"evidence_based",
			"change_after_failure",
			"avoid_over_engineering",
			"local_first",
			"reversible_changes",
			"risk_minimization",
			"probabilistic_reasoning"
		],
		"workflow": {
			"steps": [
				"clarify_goal",
				"gather_context",
				"identify_constraints",
				"list_approaches",
				"choose_minimal_approach",
				"implement_small_steps",
				"run_quality_gates",
				"on_failure_classify",
				"adjust_and_iterate",
				"summarize_outcome"
			]
		},
		"environment_init": {
			"ensure_virtual_env": true,
			"paths_checked": [".venv", "env"],
			"python_command": "python -m venv .venv",
			"verify_imports": ["fastapi", "sqlalchemy"],
			"lockfile_policy": "minimal_dependencies"
		},
		"permissions": {
			"proactive_tool_usage": true,
			"read_all_files": true,
			"batch_read_only_calls": true
		},
		"safe_change_strategy": {
			"classify_change": true,
			"baseline_tests_before_edit": true,
			"prefer_add_before_remove": true,
			"preserve_interfaces": true,
			"incremental_validation": true
		},
		"probabilistic_debugging": {
			"require_multiple_hypotheses": true,
			"confidence_threshold_success": 0.8,
			"tiers": {"high": ">=0.6", "medium": "0.25-0.59", "low": "<0.25"}
		},
		"quality_gates": [
			"lint_clean",
			"tests_pass",
			"no_dead_code",
			"interfaces_documented",
			"type_checks_clean"
		],
		"test_policy": {
			"add_on_bug_fix": true,
			"add_on_new_branch_logic": true,
			"edge_case_test_required": true
		},
		"risk_assessment_vectors": [
			"data_integrity",
			"api_contract",
			"performance",
			"security",
			"maintainability"
		],
		"logging_policy": {
			"no_secrets": true,
			"appropriate_levels": ["DEBUG", "INFO", "WARNING", "ERROR"]
		},
		"refactor_triggers": [
			"duplication_3_plus",
			"oversized_function",
			"scattered_invariant",
			"naming_mismatch"
		]
	}
}
```

---
Adopt this as a living framework—revise sections when recurring friction is observed.
