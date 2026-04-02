# DeepSeek-Prover-V2

**Developer:** DeepSeek AI
**Type:** Open-weight neural theorem prover
**Proof Assistant:** Lean 4
**Model Sizes:** 7B / 671B (MoE)
**License:** Open
**GitHub:** [deepseek-ai/DeepSeek-Prover-V2](https://github.com/deepseek-ai/DeepSeek-Prover-V2)
**Weights:** [DeepSeek-Prover-V2-7B](https://huggingface.co/deepseek-ai/DeepSeek-Prover-V2-7B) · [DeepSeek-Prover-V2-671B](https://huggingface.co/deepseek-ai/DeepSeek-Prover-V2-671B)
**Paper:** [arXiv:2504.21801](https://arxiv.org/abs/2504.21801)

---

## Overview

DeepSeek-Prover-V2 is a neural theorem prover that combines informal mathematical reasoning (chain-of-thought) with formal proof construction in Lean 4. Its distinctive feature is a hybrid approach: it first generates a natural language reasoning chain that decomposes the problem into subgoals, then produces a formal Lean proof guided by that reasoning.

The 671B model is based on DeepSeek-V3-Base (Mixture-of-Experts architecture), while the 7B model is built on DeepSeek-Prover-V1.5-Base. Both weights are publicly available on HuggingFace with no gating.

---

## Methodology

A three-stage training pipeline:

### 1. Cold-Start Data Generation

DeepSeek-V3 (the base language model) decomposes theorems into subgoals using natural language reasoning, while a smaller 7B model proves each individual subgoal in Lean. This generates (theorem, reasoning chain, proof) triples as training data.

### 2. Synthetic Data Reconstruction

For problems where all subgoals were individually proved but the end-to-end proof was not found, the system reconstructs complete proofs by combining individual subgoal proofs with DeepSeek-V3's reasoning chains. This expands the training set beyond what pure search discovered.

### 3. Reinforcement Learning

The fine-tuned model undergoes RL training using binary correct/incorrect feedback from Lean's compiler as the reward signal. A proof either type-checks or it doesn't — no partial credit.

---

## Benchmark Results

| Benchmark | 7B | 671B |
|-----------|----|------|
| **miniF2F-test** | — | 88.9% (Pass@8192) |
| **PutnamBench** | — | 49/658 |
| **ProverBench** | — | Evaluated (325 problems) |

ProverBench is DeepSeek's own benchmark containing 15 formalized AIME 2024/2025 problems and 310 textbook problems.

---

## Setup & Running

**Quick inference:**
```python
from transformers import AutoModelForCausalLM, AutoTokenizer
model = AutoModelForCausalLM.from_pretrained(
    "deepseek-ai/DeepSeek-Prover-V2-7B",
    torch_dtype=torch.bfloat16,
    device_map="auto"
)
```

**Compute requirements:**
- 7B model: ~14GB VRAM (bf16), single GPU
- 671B model: MoE architecture, requires multi-node cluster with hundreds of GB of VRAM; not practical for local experiments

**Note:** DeepSeek also offers a hosted API at [platform.deepseek.com](https://platform.deepseek.com) which may be more practical for the 671B model.

---

## References

- GitHub: [https://github.com/deepseek-ai/DeepSeek-Prover-V2](https://github.com/deepseek-ai/DeepSeek-Prover-V2)
- Paper: [https://arxiv.org/abs/2504.21801](https://arxiv.org/abs/2504.21801)
- Weights (7B): [https://huggingface.co/deepseek-ai/DeepSeek-Prover-V2-7B](https://huggingface.co/deepseek-ai/DeepSeek-Prover-V2-7B)
- Weights (671B): [https://huggingface.co/deepseek-ai/DeepSeek-Prover-V2-671B](https://huggingface.co/deepseek-ai/DeepSeek-Prover-V2-671B)
- ProverBench dataset: [https://huggingface.co/datasets/deepseek-ai/DeepSeek-ProverBench](https://huggingface.co/datasets/deepseek-ai/DeepSeek-ProverBench)
