# Example Pipeline: Rule-Driven System Review

This is a minimal, mechanical example of Cognitive Task Partitioning applied to a rule-driven system.

The goal is to separate **creative exploration** (human + LLM) from **mechanical verification** (deterministic tooling).

---

## Inputs

- A structured artifact (example):
  - `module.toml` (module metadata + entry points)
  - `rules.toml` (rules expressed as Trigger → Conditions → Effects)
  - `state.schema.json` (bounded world state schema)

---

## Stage A — Design Exploration (Human + LLM)

Output: a **draft artifact**, not “final truth”.

Typical activities:

- propose rule sets and constraints
- enumerate likely edge cases and failure modes
- produce initial module + rules artifacts
- produce a short “expected behaviors” list

Deliverables:

- `rules.toml`
- `module.toml`
- `expected_behaviors.md` (10–20 bullet expectations)

---

## Stage B — Deterministic Validation (Tooling)

Purpose: structural correctness.

Suggested checks:

- schema validation of module + rules
- contract checks (required fields, IDs, references)
- determinism checks (no unsupported randomness, no hidden IO)
- dependency graph sanity (unknown references, cycles where disallowed)

Output:

- `validate_report.md`
- non-zero exit on failure

---

## Stage C — Analysis (Tooling)

Purpose: mechanical reasoning.

Suggested checks:

- reachability analysis (dead/unreachable rules)
- trigger coverage (events with no handlers, handlers with no events)
- gate satisfiability (gates that can never become true)
- conflict detection (mutually exclusive effects, contradictory invariants)

Output:

- `analysis_report.md`
- machine-readable summary (`analysis.json`)

---

## Stage D — Simulation / Search (Tooling)

Purpose: explore behavior under many paths.

Options:

- bounded state exploration (small-state exhaustive runs)
- Monte Carlo simulation (large-state statistical runs)
- property checks (“pressure should not increase without sinks”, etc.)

Outputs:

- `sim_report.md`
- `traces/` (reproducible seed + action trace files)
- `metrics.json` (time-to-threshold distributions, etc.)

---

## Stage E — Evidence Review (Human, optionally assisted by LLM)

Humans review **evidence**, not guesses:

- what failed?
- is the failure acceptable (intended constraint) or a bug?
- what change resolves it with minimal collateral impact?

LLMs can help by:

- summarizing large reports
- proposing candidate fixes
- generating new targeted tests

But fixes do not ship until the deterministic tooling is green.

---

## Release Gate

Release requires:

- `validate` green
- `analyze` green (or explicitly waived with rationale)
- simulation thresholds met (or explicitly waived with rationale)
- reproducible run command captured in `REPRO.md`

---

## Why this matters

This pipeline prevents the “LLM did it, ship it” failure mode.

It turns AI-assisted exploration into a reliable engineering process by forcing outputs through
verification and simulation.
