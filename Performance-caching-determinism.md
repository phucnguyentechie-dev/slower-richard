Stagehand – Cost, Speed, and Deterministic Replay

Purpose

Optimize end-to-end workflow for fast repeatability and lower LLM cost by introducing caching, reducing model calls, and hardening the flow into a more deterministic “production-grade” runner.

⸻

Scope

Covered capabilities:
	•	Reducing unnecessary model calls (cost control)
	•	Introducing caching and replay-friendly execution patterns
	•	Converting exploratory “agent-like” steps into deterministic steps
	•	Adding execution metrics (time, retries, LLM calls) for observability
	•	Improving stability under rate limits and slow UI

⸻

Model Call Optimization

Hotspots Identified
	•	Repeated “interpret UI” actions in loops (pagination, table rows)
	•	Re-discovering the same elements across steps
	•	Overusing flexible instructions where deterministic logic works better

Applied Techniques
	•	Consolidated actions: bundle related interactions into fewer Stagehand calls
	•	Prefer stable anchors: use consistent page landmarks (header text, nav labels) to reduce repeated “search”
	•	Avoid re-extraction: store extracted data once and reuse across steps instead of re-reading the page

⸻

Caching and Replay Strategy

Goals
	•	First run can be “expensive” (discovery)
	•	Subsequent runs should be cheap + fast
	•	Reruns should behave similarly even when minor UI changes happen

Implementation Pattern
	•	Enabled a cache directory for repeated runs (where supported in your setup)
	•	Added a “checkpoint” model:
	•	each step writes checkpoint.json (step name, URL, key state)
	•	failures can resume from the last successful checkpoint (optional)

Replay-Friendly Design
	•	Keep each step pure (inputs → output) as much as possible
	•	Move non-deterministic logic to dedicated “recovery” helpers
	•	Ensure outputs are versioned per run: outputs/<runId>/...

⸻

Performance Instrumentation

Metrics Collected Per Run
	•	Total runtime
	•	Runtime per step
	•	Retry counts per step
	•	Failure category (timeout, not-found, auth, permission)
	•	(If available) estimated model call count / cache hit indicators

Run Report Enhancements
run-report.json now includes:
	•	step timings
	•	last known URL per step
	•	error stack + screenshot path on failure
	•	summary block: successRate, slowestStep, mostRetriedStep

⸻

Stability Improvements for Long Runs

Rate Limits / Throttling
	•	Added exponential backoff for known transient failures
	•	Inserted a lightweight “cooldown” only when the workflow detects throttling signals

Dynamic UI Handling
	•	Replaced fixed sleeps with state-based readiness checks
	•	Added a reusable helper: dismissInterruptions() (cookie banners, modals, announcements)
	•	For paginated extraction: checkpoint per page to avoid full restart

⸻

Hands-on Implementation

“Before vs After” Outcome
	•	Second run is significantly faster due to fewer repeated interpretive actions + caching
	•	Failures are more diagnosable due to step-level metrics + artifacts
	•	Workflow is now closer to “CI runnable” quality (predictable, report-driven)

⸻

Issues / Findings
	•	Some flows still require a small amount of flexible interpretation when UI variants appear (A/B layouts). These are now isolated behind a single fallback step, not scattered across the workflow.
	•	Cache usefulness depends on keeping steps consistent; frequent prompt changes reduce cache hit rates.

