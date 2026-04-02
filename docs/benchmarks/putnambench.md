# PutnamBench

**Type:** Mathematical theorem proving benchmark
**Level:** Undergraduate competition (Putnam)
**Problems:** 1,724 total (672 in Lean 4)
**Proof Assistant:** Lean 4, Isabelle, Coq
**Created by:** George Tsoukalas, Jasper Lee, John Jennings et al. (UT Austin / Trishul Lab)
**GitHub:** [trishullab/PutnamBench](https://github.com/trishullab/PutnamBench)
**Paper:** NeurIPS 2024 Datasets & Benchmarks Track
**License:** Apache 2.0 (Lean 4 / Isabelle), MIT (Coq)

---

## Overview

PutnamBench contains 1,724 manually crafted formalizations of 640 theorems from the William Lowell Putnam Mathematical Competition (1962–2025). With 672 Lean 4 problems, it is significantly larger and harder than miniF2F, making it better suited for evaluating frontier theorem provers.

A key feature is that PutnamBench includes "factored solutions" — problems that require exhibiting a numerical answer in addition to proving its correctness. This supports two evaluation modes: proving with the answer pre-specified, or deriving the answer alongside the proof.

---

## Mathematical Domains

| Domain | Problems |
|--------|----------|
| Algebra | 253 |
| Analysis | 229 |
| Number Theory | 113 |
| Geometry | 71 |
| Linear Algebra | 53 |
| Combinatorics | 33 |
| Abstract Algebra | 28 |
| Probability | 10 |
| Set Theory | 8 |

Problems may fall under multiple categories.

---

## Known Results

| Prover | Problems Solved | Notes |
|--------|----------------|-------|
| Seed-Prover 1.5 (ByteDance) | ~88% | Closed system |
| Goedel-Prover-V2-32B | 86 (Pass@192) | Open weights |
| Numina-Lean-Agent | 12/12 Putnam 2025 | Claude-based agent |
| DeepSeek-Prover-V2-671B | 49/658 | Pass@1024 |
| GPT-4o (few-shot baseline) | 1 | Original baseline |

---

## Caveats

- **Not saturated:** Unlike miniF2F, top systems solve well under 100% of PutnamBench, making it a more discriminating benchmark.
- **Multi-language:** The 672 Lean 4 problems are a subset of the full 1,724. We use only the Lean 4 subset.
- **Difficulty range:** Putnam problems range from accessible (A1/B1) to extremely hard (A6/B6). Expect most provers to solve only the easier problems.

---

## References

- GitHub: [https://github.com/trishullab/PutnamBench](https://github.com/trishullab/PutnamBench)
- Website: [https://trishullab.github.io/PutnamBench/](https://trishullab.github.io/PutnamBench/)
