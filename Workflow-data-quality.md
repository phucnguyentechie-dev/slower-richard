
Stagehand – Day 5 Report: End-to-End Workflow + Data Quality

Purpose

Convert the advanced patterns (auth, multi-page flows, retries) into a complete business workflow that produces a usable artifact (JSON/CSV/file) and a clear execution outcome (pass/fail).

⸻

Scope

Covered capabilities:
	•	Designing a real automation “job” with inputs/outputs
	•	Enforcing data quality via schema + sanity checks
	•	Building guardrails (preconditions, postconditions, and invariants)
	•	Producing artifacts and a run report for audit/debug
	•	Structuring code for maintainability (step modules, shared utilities)

⸻

Workflow Design

Workflow Definition
An end-to-end workflow is a Stagehand automation that:
	1.	Accepts validated inputs
	2.	Executes a deterministic sequence of actions across pages
	3.	Produces a structured output
	4.	Emits a machine-readable run report (success/failure per step)

Inputs
	•	targetUserEmail / queryKey / dateRange (example)
	•	Optional flags:
	•	dryRun (no destructive actions)
	•	maxRetries
	•	headless

Outputs
	•	Primary artifact: output.json (or output.csv)
	•	Secondary artifact: run-report.json containing:
	•	step status
	•	timing
	•	retry counts
	•	error snapshots (message + page URL)

⸻

Execution Mechanics

Step Contract
Each workflow step follows a consistent contract:
	•	Preconditions: what must be true before running the step
	•	Action: Stagehand actions (act, extract, navigation)
	•	Postconditions: validations that must pass
	•	On failure: bounded retries, then bubble up with context

Step Instrumentation
	•	Step-level timing (start/end)
	•	Persist:
	•	current URL
	•	last successful checkpoint
	•	(optional) screenshot on failure

⸻

Data Extraction Quality

Schema Validation
	•	Extracted data must match a defined schema:
	•	required fields present
	•	correct types
	•	non-empty constraints for critical fields

Sanity Checks
Examples:
	•	Count checks: “at least 1 record”
	•	Range checks: “dates within requested window”
	•	Consistency checks: “sum totals match visible UI totals”

If checks fail:
	•	mark workflow as failed
	•	include structured reason in report

⸻

Reliability Enhancements Applied

Guarded Navigation
	•	After each navigation or major interaction:
	•	verify URL pattern
	•	verify expected page landmark (header/title/main component)

Targeted Retries
	•	Retries applied only to:
	•	extraction steps with transient UI loading
	•	click/type actions on unstable elements
	•	No retries for:
	•	authentication failures
	•	authorization/permission errors

Fallback Logic
	•	Alternate interaction path when UI element is not found:
	•	open menu → search → direct navigation
	•	use a different entry point to the same page

