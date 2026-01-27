

Stagehand – Deterministic UI Contracts

Purpose

Reduce structural and ambiguous failures by formalizing assumptions about the UI into explicit, verifiable contracts.
The goal is to fail early, clearly, and intentionally when the UI no longer matches expectations, rather than discovering incompatibilities mid-workflow.

⸻

Scope

Covered capabilities:
	•	Defining UI contracts for critical pages and transitions
	•	Validating UI assumptions before executing dependent steps
	•	Distinguishing structural UI changes from transient rendering issues
	•	Making UI-related failures deterministic and diagnosable

Out of scope:
	•	Cost modeling and budget enforcement (planned later)
	•	Parallel execution strategies (planned later)

⸻

UI Contract Definition

What a UI Contract Specifies

A UI contract defines the minimum viable structure required for a step to proceed, including:
	•	Required text anchors (headers, section titles, labels)
	•	Expected navigation context (URL patterns, active tabs)
	•	Required interactive elements (buttons, inputs, tables)
	•	Known blocking states that must not be present (error banners, empty states)

Contracts describe what must be true, not how the page is visually rendered.

⸻

Stable Anchor Strategy

Preferred anchors:
	•	Visible text with semantic meaning
	•	Navigation labels
	•	Section headers
	•	ARIA roles where available

Avoided anchors:
	•	Absolute positions
	•	Dynamic class names
	•	Layout-dependent selectors
	•	Pixel-based assumptions

This reduces sensitivity to cosmetic UI changes.

⸻

Contract Validation Step

Validation Timing

Each major navigation or page transition now includes:
	•	URL sanity check
	•	Presence validation of required anchors
	•	Blocking-state detection

Validation runs before any extraction or interaction logic.

⸻

Failure Behavior

On contract violation:
	•	Immediate abort (no retries)
	•	Capture diagnostic artifacts (screenshot + DOM snapshot if available)
	•	Emit a structural failure with explicit contract name

This prevents wasted retries on unrecoverable UI changes.

⸻

Contract Versioning
	•	UI contracts are versioned per workflow
	•	Contract updates are explicit, reviewable changes
	•	Cache reuse is tied to contract version consistency

This ensures cached runs are only reused when UI assumptions remain valid.

⸻

Interaction with Failure Taxonomy

UI contracts resolve ambiguity from the previous day’s failure classification:
	•	Violated contract → Structural failure
	•	Contract valid but element temporarily unavailable → Transient failure

This sharpens recover vs abort decisions.

⸻

Observability Improvements

Run reports now include:
	•	Contract name per step
	•	Contract validation pass/fail status
	•	Contract version used for the run

This makes UI-related breakages immediately traceable.

⸻

Outcomes
	•	Structural UI changes fail fast and predictably
	•	Reduced retries caused by misclassified UI issues
	•	Clear separation between recoverable UI delays and unrecoverable layout changes
	•	Improved confidence in replayability and cache correctness

⸻

Issues / Open Questions
	•	Some pages exhibit A/B layout variants that require multiple acceptable contracts; these will be handled via explicitly enumerated contract variants.
	•	Overly strict contracts may increase maintenance cost; contracts must remain minimal and intent-focused.

