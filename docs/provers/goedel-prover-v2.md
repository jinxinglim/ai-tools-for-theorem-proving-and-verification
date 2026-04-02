# Goedel-Prover-V2

**Developer:** Goedel-LM
**Type:** Open-weight neural theorem prover
**Proof Assistant:** Lean 4
**Model Sizes:** 8B / 32B
**License:** Apache 2.0
**GitHub:** [Goedel-LM/Goedel-Prover-V2](https://github.com/Goedel-LM/Goedel-Prover-V2)
**Weights:** [Goedel-Prover-V2-8B](https://huggingface.co/Goedel-LM/Goedel-Prover-V2-8B) · [Goedel-Prover-V2-32B](https://huggingface.co/Goedel-LM/Goedel-Prover-V2-32B)
**Paper:** [arXiv:2508.03613](https://arxiv.org/abs/2508.03613)

---

## Overview

Goedel-Prover-V2 is an open-source language model for automated theorem proving in Lean 4. It generates whole-proof completions: given a theorem statement with `sorry`, it produces a complete tactic proof. The 8B model matches DeepSeek-Prover-V2-671B on miniF2F while being ~100x smaller, and the 32B model with self-correction currently leads the PutnamBench leaderboard.

Based on the Qwen3 architecture, Goedel-Prover-V2 is fully open (Apache 2.0) with weights, training code, and inference scripts all publicly available.

---

## Methodology

Three key innovations:

### 1. Scaffolded Data Synthesis

Rather than training on a fixed dataset, the system generates synthetic proof tasks of progressively increasing difficulty. This curriculum-style approach allows the model to build competence incrementally and tackle harder problems as training advances.

### 2. Verifier-Guided Self-Correction

The model learns to revise its own proofs using feedback from Lean's compiler. When a proof attempt fails, the model receives the error message and generates a corrected proof. This iterative loop (generate → verify → correct) runs at both training and inference time.

### 3. Model Averaging

Multiple training checkpoints are combined (averaged) to improve robustness and reduce variance in proof generation quality.

---

## Benchmark Results

| Benchmark | 8B (Pass@32) | 32B (Pass@32) | 32B + self-correction (Pass@32) |
|-----------|-------------|---------------|--------------------------------|
| **miniF2F-test** | 84.6% | 88.0% | 90.4% |
| **PutnamBench** | — | — | 57 (Pass@32), 86 (Pass@192) |

The 32B model with self-correction surpasses DeepSeek-Prover-V2-671B's 47 problems on PutnamBench (at Pass@1024), achieving 86 problems at only Pass@192.

---

## Setup & Running

**Requirements:** Linux, Python 3.10, Lean 4 (v4.9), Mathlib4, CUDA GPU

**Installation:**
```bash
git clone --recurse-submodules https://github.com/Goedel-LM/Goedel-Prover-V2.git
conda env create -f goedelv2.yml
cd Mathlib4 && lake exe cache get && lake build
```

**Quick inference:**
```python
from transformers import AutoModelForCausalLM, AutoTokenizer
model = AutoModelForCausalLM.from_pretrained("Goedel-LM/Goedel-Prover-V2-32B")
```

**Batch inference with self-correction:**
```bash
bash scripts/pipeline.sh
```

**Compute requirements:**
- 8B model: ~16GB VRAM (fp16), single GPU
- 32B model: ~64GB VRAM (fp16), multi-GPU or A100 80GB

---

## References

- GitHub: [https://github.com/Goedel-LM/Goedel-Prover-V2](https://github.com/Goedel-LM/Goedel-Prover-V2)
- Paper: [https://arxiv.org/abs/2508.03613](https://arxiv.org/abs/2508.03613)
- Weights (32B): [https://huggingface.co/Goedel-LM/Goedel-Prover-V2-32B](https://huggingface.co/Goedel-LM/Goedel-Prover-V2-32B)
- Weights (8B): [https://huggingface.co/Goedel-LM/Goedel-Prover-V2-8B](https://huggingface.co/Goedel-LM/Goedel-Prover-V2-8B)
