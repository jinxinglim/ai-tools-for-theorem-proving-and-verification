# BFS-Prover-V2

**Developer:** ByteDance Seed
**Type:** Open-weight step-level theorem prover
**Proof Assistant:** Lean 4
**Model Sizes:** 7B / 32B
**License:** Apache 2.0
**GitHub:** [ByteDance-Seed/BFS-Prover-V2](https://github.com/ByteDance-Seed/BFS-Prover-V2)
**Weights:** [HuggingFace collection](https://huggingface.co/collections/ByteDance-Seed/bfs-prover-68db961a5fdf9de045440230)
**Ollama:** `zeyu-zheng/BFS-Prover-V2-7B` · `zeyu-zheng/BFS-Prover-V2-32B`
**Paper:** [arXiv:2509.06493](https://arxiv.org/abs/2509.06493)
**Homepage:** [bfs-prover.github.io/V2](https://bfs-prover.github.io/V2/)

---

## Overview

BFS-Prover-V2 is a step-level (tactic-by-tactic) theorem prover for Lean 4, currently holding the highest reported score on miniF2F-test at 95.08%. Unlike whole-proof generators (Goedel-Prover-V2, DeepSeek-Prover-V2), BFS-Prover-V2 generates one tactic at a time and uses tree search to explore the proof space, guided by a planner agent that decomposes theorems into subgoals.

The system is available on Ollama for easy deployment and integrates with LLMLean for use directly within VS Code.

---

## Methodology

### Training: Multi-Stage Expert Iteration

An adaptive tactic-level data filtering and periodic retraining framework that overcomes performance plateaus observed during extended post-training. The model is iteratively trained on successful proof trajectories, with filtering criteria that become stricter as training progresses.

### Inference: Planner-Enhanced Multi-Agent Tree Search

A hierarchical reasoning system with two components:

1. **Planner agent** — decomposes a theorem statement into a sequence of intermediate subgoals (lemmas)
2. **Prover agents** — multiple parallel agents perform best-first tree search to prove each subgoal, sharing results through a common cache

This allows the system to tackle complex proofs by breaking them into manageable steps, with multiple agents working in parallel and benefiting from each other's discoveries.

---

## Benchmark Results

| Model | miniF2F-test | miniF2F-valid | ProofNet-test |
|-------|-------------|---------------|---------------|
| BFS-Prover-V2-7B | 82.4% | — | — |
| BFS-Prover-V2-32B | 86.1% | 85.5% | 41.4% |
| BFS-Prover-V2-32B + Planner | **95.08%** | **95.5%** | — |

The 95.08% on miniF2F-test is the current state of the art for step-level provers.

---

## Setup & Running

### Option 1: Ollama + LLMLean (easiest, for interactive use)

```bash
ollama pull zeyu-zheng/BFS-Prover-V2-7B:q8_0
```

Then configure `~/.config/llmlean/config.toml` and use `llmstep` in Lean proofs.

### Option 2: Full proof search pipeline

```bash
git clone https://github.com/ByteDance-Seed/BFS-Prover-V2.git
cd BFS-Prover-V2
pip install .
```

Configure `src/plan/config.yaml`, then:
```bash
bash src/plan/run_local_plan.sh    # run planner
bash src/search/run_local_search.sh # run proof search
```

**Requirements:** Python 3.11, Lean 4 (v4.10.0), CUDA GPU

**Compute requirements:**
- 7B model: ~14GB VRAM (fp16), single GPU
- 32B model: ~64GB VRAM (fp16), multi-GPU or A100 80GB
- Planner + multi-agent search benefits from multiple GPUs for parallelism

---

## References

- GitHub: [https://github.com/ByteDance-Seed/BFS-Prover-V2](https://github.com/ByteDance-Seed/BFS-Prover-V2)
- Paper: [https://arxiv.org/abs/2509.06493](https://arxiv.org/abs/2509.06493)
- Homepage: [https://bfs-prover.github.io/V2/](https://bfs-prover.github.io/V2/)
- HuggingFace: [https://huggingface.co/collections/ByteDance-Seed/bfs-prover-68db961a5fdf9de045440230](https://huggingface.co/collections/ByteDance-Seed/bfs-prover-68db961a5fdf9de045440230)
- LLMLean integration: [https://github.com/cmu-l3/llmlean](https://github.com/cmu-l3/llmlean)
