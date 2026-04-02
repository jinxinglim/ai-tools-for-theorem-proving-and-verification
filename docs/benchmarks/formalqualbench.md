# FormalQualBench

**Type:** Mathematical theorem proving benchmark
**Level:** PhD qualifying exam
**Problems:** 23
**Proof Assistant:** Lean 4 + Mathlib
**Created by:** Math, Inc.
**GitHub:** [math-inc/FormalQualBench](https://github.com/math-inc/FormalQualBench)

---

## Overview

FormalQualBench is a curated set of 23 graduate-level theorem statements in Lean 4, designed to simulate the difficulty of PhD qualifying exams in mathematics. Each problem is a small Lean module with a theorem stub marked `by sorry`.

It was created by Math, Inc. (the developers of Gauss/OpenGauss) and uses the Lean FRO's Comparator tool for stricter validation than simple compilation — proofs are checked for axiom usage and theorem correctness.

---

## Mathematical Domains

Known problem areas include:
- Topology (Borsuk-Ulam, Jordan Cycle, Schauder Fixed Point)
- Number Theory (Ternary Goldbach, Erdos Discrepancy)
- Functional Analysis (Banach-Stone, Von Neumann Double Commutant)
- Combinatorics (Paris-Harrington)
- Algebraic Structures (Quillen-Suslin, Burnside's theorem)
- Graph Theory (De Bruijn-Erdos)

---

## Evaluation

Proofs are validated using:
- **leanprover/comparator** (v4.28.0) — verifies proofs against challenge modules, checking axiom usage
- **landrun** (v0.1.14)

Steps:
1. Write proofs in `Solution.lean`
2. Copy original stubs to `Challenge.lean`
3. Configure `config.json` with theorem names and permitted axioms
4. Run: `lake env comparator/.lake/build/bin/comparator config.json`

---

## Known Results

| Prover | Score |
|--------|-------|
| OpenGauss | 8/23 |
| Claude Code (Skills) | 5/23 |
| opencode (Opus) | 5/23 |
| Codex | 5/23 |
| Claude Code | 4/23 |

---

## Caveats

- **Very small:** 23 problems means each solve/miss changes the score by 4.3%. Results should be interpreted with this in mind.
- **Created by OpenGauss developers:** Math, Inc. built both the benchmark and OpenGauss, which could introduce bias in problem selection. However, OpenGauss only scores 8/23, suggesting the benchmark is genuinely challenging.
- **Graduate-level difficulty:** Most problems are significantly harder than miniF2F or PutnamBench. Low absolute scores are expected.

---

## References

- GitHub: [https://github.com/math-inc/FormalQualBench](https://github.com/math-inc/FormalQualBench)
