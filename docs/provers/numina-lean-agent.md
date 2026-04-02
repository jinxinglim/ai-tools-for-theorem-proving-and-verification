# Numina-Lean-Agent

**Developer:** Project Numina
**Type:** Agent-based theorem prover (uses Claude as backend)
**Proof Assistant:** Lean 4
**License:** MIT
**GitHub:** [project-numina/numina-lean-agent](https://github.com/project-numina/numina-lean-agent)
**Paper:** [arXiv:2601.14027](https://arxiv.org/abs/2601.14027)

---

## Overview

Numina-Lean-Agent is an AI-powered agent system for formal theorem proving in Lean 4. Rather than being a standalone neural model, it is an orchestration layer built on Claude Code that iteratively works through proof problems — generating proof attempts, receiving compiler feedback, and refining until the proof is complete or a timeout is reached.

Its most notable achievement is proving all 12 problems from the Putnam 2025 mathematics competition, as well as completing a paper-level formalization of "Effective Brascamp-Lieb inequalities."

---

## Methodology

Numina-Lean-Agent operates as an agentic loop:

1. **Problem analysis** — Claude reads the theorem statement and surrounding context
2. **Proof generation** — Claude generates a candidate proof in Lean 4 tactic mode
3. **Error detection** — the Lean LSP (Language Server Protocol) checks the proof and returns errors
4. **Iterative refinement** — Claude revises based on error messages, repeating until success or timeout

Key architectural components:
- **Claude Code** as the reasoning engine (configurable model, e.g., `claude-opus-4.5`)
- **numina-lean-lsp-mcp** for Lean LSP communication via MCP (Model Context Protocol)
- **Leandex** for semantic search over Lean codebases (finding relevant lemmas)
- **lean4-skills** — Claude Code skills specialized for Lean 4 operations

The agent has no custom-trained model weights — all mathematical reasoning comes from the Claude foundation model, enhanced by Lean-specific tooling and the iterative feedback loop.

---

## Benchmark Results

| Achievement | Result |
|-------------|--------|
| Putnam 2025 | 12/12 problems proved |
| Brascamp-Lieb formalization | Paper-level formalization completed |

No detailed quantitative results on standard benchmarks (miniF2F, ProofNet, etc.) are reported in the documentation, though the repo includes a config for running on miniF2F (`config/config_minif2f.yaml`).

---

## Setup & Running

**Installation:**
```bash
git clone https://github.com/project-numina/numina-lean-agent
cd numina-lean-agent/tutorial
./setup.sh YOUR_PROJECT_NAME
cd ..
uv python install && uv sync
source .venv/bin/activate
```

**Running on a single file:**
```bash
python -m scripts.run_claude run <file.lean> \
  --prompt-file prompts/prompt_complete_file.txt \
  --max-rounds 5
```

**Batch processing (e.g., miniF2F):**
```bash
python -m scripts.run_claude batch config/config_minif2f.yaml
```

**Requirements:**
- Python 3 with `uv` package manager
- Lean 4
- Claude Code with authentication (`ANTHROPIC_AUTH_TOKEN` or Claude login)
- No GPU needed (all compute is via Claude API)

**Cost consideration:** Each proof attempt involves multiple rounds of Claude API calls. Running across large benchmarks (e.g., FormalMATH with 5,560 problems) could incur significant API costs.

---

## References

- GitHub: [https://github.com/project-numina/numina-lean-agent](https://github.com/project-numina/numina-lean-agent)
- Paper: [https://arxiv.org/abs/2601.14027](https://arxiv.org/abs/2601.14027)
- Demo: [demo.projectnumina.ai](https://demo.projectnumina.ai)
