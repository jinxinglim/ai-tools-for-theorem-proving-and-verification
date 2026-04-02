# FormalMATH

**Type:** Mathematical theorem proving benchmark
**Level:** High-school olympiad to undergraduate
**Problems:** 5,560
**Proof Assistant:** Lean 4
**Created by:** SphereLab / M-A-P (Yu Zhouliang, Peng Ruotian, Ding Keyi et al., 2025)
**GitHub:** [Sphere-AI-Lab/FormalMATH-Bench](https://github.com/Sphere-AI-Lab/FormalMATH-Bench)
**HuggingFace:** [SphereLab](https://huggingface.co/SphereLab)
**Paper:** [arXiv:2505.02735](https://arxiv.org/abs/2505.02735)
**License:** Apache 2.0

---

## Overview

FormalMATH is the largest curated theorem proving benchmark in Lean 4, with 5,560 formally verified mathematical statements spanning high-school olympiad to undergraduate level. Built via a human-in-the-loop autoformalization pipeline, it provides broad coverage across multiple mathematical domains and difficulty levels.

Two variants are available:
- **FormalMATH-All** — the full 5,560 problems
- **FormalMATH-Lite** — a curated subset for faster evaluation

The benchmark is far from saturated: the best model achieves 28.31% on FormalMATH-All (Pass@32) and 61.88% on FormalMATH-Lite (Pass@3200).

---

## Mathematical Domains

The benchmark covers: algebra, applied mathematics, calculus, number theory, discrete mathematics, and other traditional mathematical fields. A domain distribution visualization is available in the repository.

---

## Evaluation Pipeline

The repo provides an end-to-end evaluation system:

1. **Generation** (`generate_answers.py`) — run the prover model on benchmark problems
2. **Verification** (`verify_answers.py`) — check generated proofs using the Lean REPL
3. **Evaluation** (`evaluate_results.py`) — compute pass rates and statistics

All three stages can be run via the unified script `FoMA_Eval.py`.

The system uses a specific Lean REPL commit (`adbbfcb9d4e61c12db96c45d227de92f21cc17dd`) for reproducible verification.

---

## Known Results

| Prover | FormalMATH-All (Pass@32) | FormalMATH-Lite |
|--------|--------------------------|-----------------|
| DeepSeek-Prover-V2-671B | 28.31% | 61.88% (Pass@3200) |
| Most capable models | 16.46% | — |

---

## Caveats

- **Largest benchmark — longest to run:** At 5,560 problems, running all provers across FormalMATH will be the most time-consuming and expensive part of the experiment. Consider using FormalMATH-Lite for initial runs.
- **Human-in-the-loop construction:** Problems were autoformalized and then verified by humans, which is more scalable than manual formalization but may introduce autoformalization artifacts.
- **Difficulty spread:** The benchmark spans a wide range from high-school to undergraduate, meaning aggregate pass rates blend easy and hard problems.

---

## Setup

```bash
git clone https://github.com/Sphere-AI-Lab/FormalMATH-Bench
cd FormalMATH-Bench
# Datasets are automatically downloaded to ./data when requested
```

**Dependencies:** Lean 4, Mathlib4, Python with transformers/vLLM for model inference.

---

## References

- GitHub: [https://github.com/Sphere-AI-Lab/FormalMATH-Bench](https://github.com/Sphere-AI-Lab/FormalMATH-Bench)
- Paper: [https://arxiv.org/abs/2505.02735](https://arxiv.org/abs/2505.02735)
- HuggingFace: [https://huggingface.co/SphereLab](https://huggingface.co/SphereLab)
- Website: [https://spherelab.ai/FormalMATH/](https://spherelab.ai/FormalMATH/)
