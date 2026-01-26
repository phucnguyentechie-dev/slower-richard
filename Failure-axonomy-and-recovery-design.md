Stagehand – Failure Taxonomy and Recovery Design

Purpose

Increase reliability and operator confidence by making failures classifiable, predictable, and recoverable instead of ad-hoc.
The goal is to ensure that the same failure never requires fresh interpretation more than once.

⸻

Scope

Covered capabilities:
	•	Systematic failure classification across the workflow
	•	Clear recover vs abort decisions per failure type
	•	Idempotent recovery steps to enable safe retries
	•	Consistent failure fingerprints for diagnosis and metrics

Out of scope:
	•	UI contract validation (planned next day)
	•	Cost budgeting (planned later)

⸻

Failure Analysis

Failure Categories Identified
	•	Transient
	•	Network timeouts
	•	Rate limiting / throttling
	•	Slow UI rendering
	•	Structural
	•	Missing or renamed UI elements
	•	Layout shifts breaking selectors
	•	State-related
	•	Auth/session expiration
	•	Stale navigation context
	•	Data integrity
	•	Partial extraction
	•	Inconsistent pagination results

Each failure is now mapped to a primary category to avoid ambiguous handling.

⸻

Recovery Strategy Design

Recover vs Abort Rules

Defined explicit handling per category:
	•	Transient → retry with backoff
	•	State-related → rehydrate state (re-auth / re-nav) once, then retry
	•	Structural → abort early with diagnostic artifacts
	•	Data integrity → abort to avoid corrupt outputs

This prevents infinite retries and unclear failure loops.

⸻

Idempotent Recovery Steps

Key Principle

Recovery steps must be safe to run multiple times without changing final outcomes.

Applied patterns:
	•	Navigation recovery always starts from a known anchor URL
	•	Re-auth clears session state before retry
	•	Pagination recovery resumes from last checkpointed page

This allows retries without accumulating hidden side effects.

⸻

Failure Fingerprinting

Implementation
	•	Each failure now emits:
	•	failureCategory
	•	stepName
	•	lastKnownURL
	•	rootCauseHint (best-effort)
	•	Fingerprint = (category + step + signal)

Benefits
	•	Identical failures are grouped across runs
	•	Metrics and alerts are based on causes, not symptoms
	•	Reduces time spent diagnosing “new” failures that are actually repeats

⸻

Observability Improvements
	•	Run reports now include:
	•	failureCategory distribution
	•	first-failure vs repeated-failure indicators
	•	Retries are labeled with reason (not just count)

This makes instability trends visible over time.

⸻

Outcomes
	•	Failures are now intentional states, not surprises
	•	Retry behavior is bounded and explainable
	•	Workflow reliability improved without increasing model usage
	•	Sets a foundation for deterministic UI contracts in the next iteration

⸻

Issues / Open Questions
	•	Some UI-driven failures still blur the line between transient and structural; these will be resolved by explicit UI contracts.
	•	Over-classification risk: failure categories must remain stable to be useful.
