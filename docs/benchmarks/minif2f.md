# miniF2F

**Type:** Mathematical theorem proving benchmark
**Level:** Olympiad / high-school / undergraduate
**Problems:** 488 (244 test + 244 validation)
**Proof Assistant:** Lean 4 (ported from Lean 3)
**Created by:** OpenAI (Zheng, Han, Polu, 2021)
**GitHub (Lean 4 port):** [yangky11/miniF2F-lean4](https://github.com/yangky11/miniF2F-lean4)
**GitHub (original):** [openai/miniF2F](https://github.com/openai/miniF2F)
**Paper:** [arXiv:2109.00110](https://arxiv.org/abs/2109.00110)

---

## Overview

miniF2F is the most widely used benchmark for evaluating AI theorem provers. It consists of 488 formally stated mathematical problems drawn from AMC, AIME, IMO, and undergraduate math courses, split evenly into test and validation sets. Each problem is a theorem statement with a `sorry` placeholder that must be replaced with a valid proof.

Originally formalized in Lean 3, Metamath, Isabelle, and HOL Light, a Lean 4 port is maintained at [yangky11/miniF2F-lean4](https://github.com/yangky11/miniF2F-lean4).

---

## Task Format

Each task is a Lean 4 theorem with `sorry`:

```lean
theorem amc12a_2021_p7 (x y : Real) (hx : x ≠ 0) (hy : y ≠ 0)
    (h : x + 1 / y = 4 * y + 1 / x) : ... := by
  sorry
```

The prover must fill in `sorry` with a valid tactic proof.

---

## Problem Sources

| Source | Description |
|--------|-------------|
| AMC 10/12 | American Mathematics Competitions |
| AIME | American Invitational Mathematics Examination |
| IMO | International Mathematical Olympiad |
| Math courses | High-school and undergraduate problems |

Domains: algebra, number theory, inequalities, and some combinatorics/geometry.

---

## Known Results

| Prover | miniF2F-test | Pass@K |
|--------|-------------|--------|
| BFS-Prover-V2-32B + Planner | 95.08% | @8192 |
| Goedel-Prover-V2-32B + self-correction | 90.4% | @32 |
| DeepSeek-Prover-V2-671B | 88.9% | @8192 |
| Kimina-Prover-72B | 84.0% | @8192 |
| Goedel-Prover-V2-8B | 84.6% | @32 |
| Aristotle (Harmonic) | 83% | combined |
| BFS-Prover-V2-7B | 82.4% | — |

---

## Caveats

- **Near-saturated:** Top systems score 90%+, reducing the benchmark's ability to discriminate between strong provers. A recent paper ([arXiv:2511.03108](https://arxiv.org/abs/2511.03108)) reviews limitations and charts a path forward.
- **Lean 4 port maintenance:** The Lean 4 port includes a warning that it is not regularly maintained. Users should verify compatibility with their Lean/Mathlib version.
- **Small size:** At 488 problems (244 test), statistical significance of small differences can be limited.

---

## Setup

```bash
git clone https://github.com/yangky11/miniF2F-lean4
cd miniF2F-lean4
lake exe cache get
lake build
```

---

## References

- Lean 4 port: [https://github.com/yangky11/miniF2F-lean4](https://github.com/yangky11/miniF2F-lean4)
- Original: [https://github.com/openai/miniF2F](https://github.com/openai/miniF2F)
- Paper: [https://arxiv.org/abs/2109.00110](https://arxiv.org/abs/2109.00110)
- HuggingFace: [hoskinson-center/minif2f-lean4](https://huggingface.co/datasets/hoskinson-center/minif2f-lean4)
