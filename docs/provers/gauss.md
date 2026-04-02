# Gauss & OpenGauss

**Developer:** Math, Inc.
**Type:** Autoformalization agent (Gauss) / Open-source Lean 4 orchestration harness (OpenGauss)
**Proof Assistant:** Lean 4
**Pages:** [Gauss](https://www.math.inc/gauss) · [OpenGauss](https://www.math.inc/opengauss)
**GitHub (OpenGauss):** [math-inc/OpenGauss](https://github.com/math-inc/OpenGauss)

---

## Overview

Math, Inc. develops two related but distinct systems for AI-assisted theorem proving and formalization in Lean 4:

- **Gauss** is a closed-source, commercial **autoformalization** agent. It translates existing human mathematical proofs into formally verified Lean 4 code, requiring high-level natural language scaffolding and expert guidance from human mathematicians. It is not publicly available.
- **OpenGauss** is the open-source counterpart — a multi-agent orchestration harness for Lean 4 workflows. Unlike Gauss, OpenGauss supports **both autoformalization and autonomous theorem proving** through separate workflow modes.

**Key distinction:** Gauss is purely an autoformalization tool (informal proof → formal proof). OpenGauss supports multiple modes, including `/autoprove` which performs autonomous theorem proving from formal statements alone — no informal proofs needed.

---

## Methodology

### Gauss

Gauss operates as an autonomous agent that can work for hours on large formalization tasks, though it currently requires high-level natural language scaffolding and expert guidance from human mathematicians to direct the formalization effort. The system decomposes large formalization projects into manageable chunks, handles complex analysis results that have previously obstructed progress, and iterates based on feedback from the Lean compiler.

The agent's approach is orchestration-heavy: rather than a single model pass, Gauss coordinates multiple concurrent sub-agents, each with its own Lean runtime. The underlying infrastructure (Trinity, developed with Morph Labs) scales to thousands of concurrent agents consuming multiple terabytes of cluster RAM.

### OpenGauss

OpenGauss functions as a project-scoped orchestration layer. It exposes five primary workflows via a unified CLI:

| Command | Purpose | Input |
|---------|---------|-------|
| `/autoprove` | Autonomous theorem proving | Lean files with `sorry` placeholders |
| `/prove` | Guided theorem proving | Lean files with `sorry` placeholders |
| `/autoformalize` | Autonomous formalization | Informal sources (PDFs, URLs, markdown) via `--source` |
| `/formalize` | Interactive formalization | Informal sources |
| `/draft` | Proof skeleton generation | Lean files |

Each command dispatches to the `lean4-skills` backend, which handles the mathematical reasoning. OpenGauss manages project state (`.gauss/project.yaml`), spawns and tracks child agent sessions, wires MCP/LSP protocol, and coordinates swarms of parallel agents.

**`/autoprove` vs `/autoformalize`:** These are fundamentally different tasks. `/autoprove` takes existing Lean theorem declarations with `sorry` placeholders and attempts to fill in valid proofs — this is pure theorem proving from formal statements. `/autoformalize` takes informal sources (PDFs, URLs, markdown) and performs end-to-end claim extraction, Lean stub drafting, and proving. For our experiment (given a theorem with `sorry`, generate a proof), `/autoprove` is the relevant mode.

---

## Architecture & Implementation

### Gauss

- **Infrastructure**: Trinity environments (developed with Morph Labs), supporting thousands of concurrent Lean runtimes
- **Interaction model**: Natural language scaffolding from human experts → autonomous agent execution → Lean compiler feedback loop
- **Deployment**: Commercial product, beta testing with working mathematicians; not publicly available

### OpenGauss

- **Architecture**: Orchestration layer over `cameronfreer/lean4-skills` for domain-specific reasoning
- **Cycle engine**: Both `/autoprove` and `/autoformalize` share a 6-phase cycle engine: Plan → Work → Checkpoint → Review → Replan → Continue/Stop
- **Project model**: State maintained in `.gauss/project.yaml`, discovered upward from working directory
- **Agent management**: Spawn, track, reattach, and cancel child agents via `/swarm`
- **Prerequisites**: Claude or Codex with authentication, `uv` package manager, `ripgrep`
- **Installation**: `./scripts/install.sh` (Linux/macOS) or `./scripts/install.ps1 -WithWorkspace` (Windows/WSL2)
- **Hosted option**: Available at `https://morph.new/opengauss`
- **License**: Permissive open-source license (users can inspect, modify, and improve)

---

## Benchmark Results

### Gauss (autoformalization)

| Task | Result |
|------|--------|
| Prime Number Theorem formalization | ~25,000 lines of Lean, 1,000+ theorems and definitions, completed in ~3 weeks |
| Human baseline (Tao & Kontorovich) | 18 months of effort before announcing intermediate progress |

The Prime Number Theorem formalization, completed as a challenge set by Fields Medallist Terence Tao and Alex Kontorovich, represents one of the largest singular formalization projects completed by an AI system. **Note:** This was an autoformalization task — Gauss was given the existing informal proof as input and converted it to formal Lean 4 code. This is not autonomous theorem proving from scratch.

### OpenGauss (autoproving on FormalQualBench)

| Benchmark | Result | Avg Cost/Solve | Avg Time/Solve |
|-----------|--------|---------------|----------------|
| FormalQualBench (4-hour timeout) | **8/23** | $24.93 | 1h 48m |

For comparison, other agents on the same benchmark:

| Agent | Solved |
|-------|--------|
| Claude Code (Skills) | 5/23 |
| Codex | 5/23 |
| Claude Code | 4/23 |

FormalQualBench contains 23 graduate-level theorem statements as Lean 4 files with `sorry` placeholders — no informal proofs or natural language hints are provided. The evaluation was specification-based: agents received only the expert-verified Lean statement and had full freedom to construct their own definitions, lemmas, and proof strategies. This confirms that OpenGauss used its **`/autoprove` mode** (not autoformalization) for this benchmark.

All proofs were verified with the [Lean FRO Comparator](https://github.com/leanprover/comparator) tool and [landrun](https://github.com/Zouuup/landrun) sandboxing, checking that proofs compile, prove the intended statement, and use only permitted axioms (`propext`, `Quot.sound`, `Classical.choice`).

---

## Lean Integration

Both systems are built exclusively around Lean 4:

- **Lean runtime**: Each agent session maintains its own Lean runtime with full access to Mathlib
- **LSP integration**: OpenGauss wires Language Server Protocol for real-time proof state feedback
- **MCP protocol**: Used for agent-to-tool communication within the orchestration layer
- **Lean FRO Comparator**: OpenGauss benchmarks use this tool to verify proof correctness beyond simple compilation
- **Mathlib-compatible**: All produced proofs target the Lean 4 Mathlib ecosystem

---

## Limitations

- **Gauss is autoformalization only**: The commercial Gauss product translates existing informal proofs into formal Lean code. It is not designed for autonomous theorem proving from scratch. Its benchmark results (e.g., Prime Number Theorem) reflect autoformalization performance, not proving capability.
- **Expert guidance required**: Gauss still relies on high-level human scaffolding for large projects; it is not fully autonomous
- **Gauss is closed-source**: The commercial Gauss product is not publicly accessible; OpenGauss provides open access but with a different scope
- **Infrastructure requirements**: Achieving the scale of the Prime Number Theorem formalization required terabyte-scale cluster RAM
- **Benchmark scope**: FormalQualBench (math qualifying exams) may not generalize to code/program verification tasks

## Suitability for Our Experiment

OpenGauss is suitable for our experiment. Its `/autoprove` mode accepts exactly our input format (Lean theorem statements with `sorry` placeholders) and performs autonomous theorem proving without requiring informal proofs. The FormalQualBench results demonstrate this capability. We should use the `/autoprove` workflow when running OpenGauss against our benchmarks.

---

## References

- Gauss page: [https://www.math.inc/gauss](https://www.math.inc/gauss)
- OpenGauss page: [https://www.math.inc/opengauss](https://www.math.inc/opengauss)
- OpenGauss GitHub: [https://github.com/math-inc/OpenGauss](https://github.com/math-inc/OpenGauss)
- FormalQualBench: [https://github.com/math-inc/FormalQualBench](https://github.com/math-inc/FormalQualBench)
- FormalQualBench evaluation: [https://www.math.inc/formalqualbench](https://www.math.inc/formalqualbench)
- Hosted environment: [https://morph.new/opengauss](https://morph.new/opengauss)
