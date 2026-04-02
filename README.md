# AI Tools for Theorem Proving and Verification

Formal theorem proving and verification — the process of mathematically proving that software or mathematical claims are correct — has historically required deep expertise and significant manual effort. Recent advances in large language models and AI-assisted reasoning are beginning to change this. A new class of tools can now generate, validate, and repair formal proofs in interactive proof assistants like Lean 4, with some systems already matching or exceeding human expert performance on challenging benchmarks.

This project evaluates and compares recent AI theorem provers on their ability to produce formally verified proofs in Lean 4. The task is pure theorem proving: given a mathematical statement with a `sorry` placeholder, generate a valid proof that Lean's kernel accepts. We run each prover against a collection of four public benchmarks spanning olympiad, competition, and graduate-level mathematics, producing a comparison matrix of results.

## Method

### AI Provers

We evaluate six provers representing three categories: proprietary systems, open-weight neural models, and agent-based orchestrators. Detailed summaries for each prover are in the [`docs/provers/`](docs/provers/) folder.

| Prover | Developer | Type | Access | Docs |
|--------|-----------|------|--------|------|
| [Aristotle](https://arxiv.org/html/2510.01346v1) | Harmonic | Proprietary theorem prover | Private access | [docs/provers/aristotle.md](docs/provers/aristotle.md) |
| [OpenGauss](https://www.math.inc/opengauss) | Math, Inc. | Agent orchestrator (uses Claude) | Open source | [docs/provers/gauss.md](docs/provers/gauss.md) |
| [Goedel-Prover-V2](https://github.com/Goedel-LM/Goedel-Prover-V2) | Goedel-LM | Open-weight model (8B / 32B) | Open weights (Apache 2.0) | [docs/provers/goedel-prover-v2.md](docs/provers/goedel-prover-v2.md) |
| [DeepSeek-Prover-V2](https://github.com/deepseek-ai/DeepSeek-Prover-V2) | DeepSeek AI | Open-weight model (7B / 671B) | Open weights | [docs/provers/deepseek-prover-v2.md](docs/provers/deepseek-prover-v2.md) |
| [BFS-Prover-V2](https://github.com/ByteDance-Seed/BFS-Prover-V2) | ByteDance Seed | Open-weight model (7B / 32B) | Open weights (Apache 2.0) | [docs/provers/bfs-prover-v2.md](docs/provers/bfs-prover-v2.md) |
| [Numina-Lean-Agent](https://github.com/project-numina/numina-lean-agent) | Project Numina | Agent orchestrator (uses Claude) | Open source (MIT) | [docs/provers/numina-lean-agent.md](docs/provers/numina-lean-agent.md) |

**A note on paradigms:** The provers above use fundamentally different approaches. The open-weight models (Goedel-Prover-V2, DeepSeek-Prover-V2, BFS-Prover-V2) run locally on GPUs and generate proofs directly — either whole-proof or tactic-by-tactic. The agent orchestrators (OpenGauss, Numina-Lean-Agent) call Claude via API and iterate on proofs using compiler feedback. Aristotle is a proprietary system combining a 200B+ parameter model with Monte Carlo Graph Search. These differences mean we are comparing not just model quality but also architecture and inference strategy.

**Lean 4 & Mathlib versions:** Each prover was trained or built against a specific Lean 4 and Mathlib environment. We run each prover in its native environment to avoid degrading results, and document all versions below for reproducibility.

| Prover | Lean 4 | Mathlib | Notes |
|--------|--------|---------|-------|
| Aristotle | TBD | TBD | Proprietary; versions not publicly disclosed |
| OpenGauss | Flexible | Flexible | Agent-based; adapts to target project's toolchain |
| Goedel-Prover-V2 | v4.9.0-rc1 | Custom fork (`xinhjBrant/mathlib4`, branch `deepseek`, commit `2f65ba7`) | Shares environment with DeepSeek-Prover-V1.5 |
| DeepSeek-Prover-V2 | v4.9.0-rc1 | Custom fork (`xinhjBrant/mathlib4`, branch `deepseek`, commit `2f65ba7`) | Same environment as Goedel-Prover-V2 |
| BFS-Prover-V2 | v4.10.0 | Via LeanDojo 2.1.3 | Version compatibility follows LeanDojo |
| Numina-Lean-Agent | v4.26.0 | Mathlib v4.26.0 tag | Agent-based; bundled `leanproblems/` project pins this version |

### Benchmarks

We evaluate across four public benchmarks covering a range of mathematical difficulty levels. Detailed summaries for each benchmark are in the [`docs/benchmarks/`](docs/benchmarks/) folder.

| Benchmark | Problems | Level | Source | Docs |
|-----------|----------|-------|--------|------|
| [miniF2F](https://github.com/yangky11/miniF2F-lean4) | 488 | Olympiad (AMC/AIME/IMO) | OpenAI (2021) | [docs/benchmarks/minif2f.md](docs/benchmarks/minif2f.md) |
| [PutnamBench](https://github.com/trishullab/PutnamBench) | 672 (Lean 4) | Putnam competition | UT Austin (2024) | [docs/benchmarks/putnambench.md](docs/benchmarks/putnambench.md) |
| [FormalQualBench](https://github.com/math-inc/FormalQualBench) | 23 | PhD qualifying exam | Math, Inc. | [docs/benchmarks/formalqualbench.md](docs/benchmarks/formalqualbench.md) |
| [FormalMATH](https://github.com/Sphere-AI-Lab/FormalMATH-Bench) | 5,560 | HS olympiad to undergrad | SphereLab (2025) | [docs/benchmarks/formalmath.md](docs/benchmarks/formalmath.md) |

**Benchmark rationale:** Using multiple benchmarks mitigates the risk of over-indexing on a single evaluation set. miniF2F is the most widely reported benchmark (enabling comparison with published results) but is near-saturated by top systems. PutnamBench and FormalMATH are harder and less saturated. FormalQualBench is small (23 problems) but includes PhD-level difficulty rarely tested elsewhere.

**Lean 4 & Mathlib versions per benchmark:** Each benchmark pins its own Lean 4 toolchain and Mathlib version. We use the version specified by each benchmark's repository to ensure problems compile correctly.

| Benchmark | Lean 4 | Mathlib | Notes |
|-----------|--------|---------|-------|
| miniF2F | v4.24.0 | v4.24.0 tag | Pinned in `lean-toolchain` and `lakefile.lean` |
| PutnamBench | v4.27.0 | v4.27.0 tag | Pinned in `lean4/lean-toolchain` and `lean4/lakefile.lean` |
| FormalQualBench | TBD | TBD | Not pinned upstream; to be determined during setup |
| FormalMATH | v4.11.0 | Via `leanprover-community/repl` at commit `adbbfcb` | REPL commit critical for reproducibility |

### Experiment Design

Each prover is run against all four benchmarks. Results are reported as **percentage of theorems solved (Pass@32)** — each prover gets 32 attempts per problem, and a problem is considered solved if any attempt produces a valid proof. We also report **Pass@1** (single-shot accuracy) as a secondary metric, extracted from the same runs at no additional cost.

**Why Pass@32:** It is the most widely reported K value in the literature (used by Goedel-Prover-V2, among others), enabling direct comparison with published results. Higher values like Pass@8192 would be prohibitively expensive for agent-based provers on larger benchmarks. Pass@32 provides enough attempts to differentiate provers meaningfully while keeping costs tractable.

The comparison matrix (Pass@32, % of theorems solved):

|  | Aristotle | OpenGauss | Goedel-Prover-V2 | DeepSeek-Prover-V2 | BFS-Prover-V2 | Numina-Lean-Agent |
|--|-----------|-----------|-------------------|---------------------|---------------|-------------------|
| **miniF2F** (488) | | | | | | |
| **PutnamBench** (672) | | | | | | |
| **FormalQualBench** (23) | | | | | | |
| **FormalMATH** (5,560) | | | | | | |

**Sequencing:** We will validate the full pipeline end-to-end on an agent-based prover (OpenGauss or Numina-Lean-Agent) against one benchmark (miniF2F) first, since these run locally without GPU infrastructure. Once the evaluation pipeline is proven out, we will scale to the GPU-dependent model-based provers and the remaining benchmarks. FormalMATH (the largest benchmark) will be run last.

## Infrastructure & Cost Estimation

### Compute Requirements

| Prover | Compute needed | Platform |
|--------|---------------|----------|
| Goedel-Prover-V2 (8B) | 1x GPU, ~16GB VRAM | RunPod (A100) |
| Goedel-Prover-V2 (32B) | 1x A100 80GB or multi-GPU | RunPod (A100 80GB) |
| DeepSeek-Prover-V2 (7B) | 1x GPU, ~14GB VRAM | RunPod (A100) |
| BFS-Prover-V2 (7B/32B) | 1x GPU / multi-GPU | RunPod (A100) |
| OpenGauss | No GPU (Claude API) | Local machine |
| Numina-Lean-Agent | No GPU (Claude API) | Local machine |
| Aristotle | TBD (private access) | TBD |

### Cost Estimates

**GPU costs** (RunPod, A100 80GB on-demand at ~$1.19–1.39/hr):

| Benchmark | Tasks | Est. time per model prover | Est. GPU cost per prover |
|-----------|-------|---------------------------|--------------------------|
| miniF2F | 488 | 2–6 hours | $2–8 |
| PutnamBench | 672 | 4–10 hours | $5–14 |
| FormalQualBench | 23 | <1 hour | ~$1 |
| FormalMATH | 5,560 | 12–30 hours | $14–42 |

Estimated total GPU cost for three model-based provers across all benchmarks: **$75–225**.

**API costs** (OpenGauss + Numina-Lean-Agent, using Claude API):

API costs depend heavily on the number of rounds per problem and the model used. Rough estimates:
- Smaller benchmarks (miniF2F, FormalQualBench): $50–150 per agent prover
- Larger benchmarks (PutnamBench, FormalMATH): $200–1,500+ per agent prover

Estimated total API cost for two agent-based provers: **$500–3,000+**.

**Recommendation:** Run a pilot on miniF2F first to calibrate actual costs before committing to FormalMATH.

### Cloud GPU Options

| Provider | A100 80GB (on-demand) | Notes |
|----------|----------------------|-------|
| [RunPod](https://www.runpod.io/pricing) | ~$1.19–1.39/hr | Per-second billing, container-based |
| [Lambda Labs](https://lambdalabs.com) | ~$2.49/hr | Zero data transfer fees |
| [Vast.ai](https://vast.ai) | ~$0.80–1.50/hr | P2P marketplace, cheapest but less reliable |

## Results

*To be updated after experiments are conducted.*

## Conclusion

*To be updated after experiments are conducted.*

## Future Plans

The current evaluation focuses on AI-assisted theorem proving for mathematical statements in Lean 4. A natural next step is to expand the scope to **program verification** — proving correctness properties of software systems and protocols. This would involve:

- **New benchmarks:** Incorporating program verification benchmarks that test properties such as functional correctness, memory safety, and protocol compliance, moving beyond purely mathematical theorems.
- **New tools:** Evaluating AI tools specialized in formal verification of programs and protocols, which may use different proof assistants, verification frameworks, or domain-specific techniques compared to the mathematical theorem provers studied here.
- **Multi-prover collaboration:** Currently each prover attempts theorems independently. We plan to explore a collaborative pipeline where, if a prover exhausts its attempts (e.g., all 32 tries under Pass@32), it marks the proof steps where it got stuck and hands off its partial progress to a different prover. The next prover can then pick up from the point of failure rather than starting from scratch. This could leverage the complementary strengths of different prover architectures and potentially solve theorems that no single prover can handle alone.
