# Aristotle

**Developer:** Harmonic
**Type:** Automated mathematical theorem prover
**Proof Assistant:** Lean 4
**Paper:** [Aristotle: IMO-level Automated Theorem Proving](https://arxiv.org/html/2510.01346v1)

---

## Overview

Aristotle is an AI system developed by the Harmonic team that combines formal verification with informal mathematical reasoning. Its primary goal is automated mathematical theorem proving at competition level — specifically, producing formally verified Lean 4 proofs for problems from the International Mathematical Olympiad (IMO) and other advanced mathematical contexts.

The system made headlines by achieving gold-medal-equivalent performance on the 2025 IMO, producing formally verified Lean 4 solutions for five of six problems. Unlike systems that output informal proofs, Aristotle's proofs are machine-checked through Lean's kernel, ensuring absolute rigor.

---

## Methodology

Aristotle is built around three tightly integrated components:

### 1. Lean Proof Search

A Monte Carlo Graph Search (MCGS) algorithm drives the formal proof search. A 200B+ parameter transformer model serves as both the policy (generating tactic candidates) and the value function (estimating proof progress). The search conditions on the current proof state, proof history, and informal reasoning generated alongside the formal steps.

Key design choices:
- States are treated as equivalent when goals and local contexts match, allowing efficient graph reuse and avoiding circular reasoning
- Exploration follows a PUCT-based strategy weighted by the prior policy
- The proof graph has an AND/OR minimax structure to handle multi-goal proofs (where all subgoals must be closed)

### 2. Lemma-based Informal Reasoning

An informal reasoning pipeline generates natural language proofs, decomposes them into lemmas, formalizes the lemma statements, and iterates based on feedback from Lean. This pipeline converts high-level mathematical intuition into structured intermediate goals that guide the formal search.

### 3. Geometry Solver (Yuclid)

Yuclid is a C++ deductive database and algebraic reasoning engine optimized for geometric problems. It is approximately 500x faster than AlphaGeometry-1, achieved through numerical rule matching and optimized AR table data structures.

### Training

The model is trained on a large mathematical dataset (open-source plus proprietary), with proof data filtered to include only non-trivial examples. The value function is trained on both proven states and nearby unproven states. Test-time training (Expert Iteration) allows the model to improve from inference attempts during a run.

---

## Architecture & Implementation

Aristotle interacts with Lean via a REPL-based interface. The REPL processes Lean code blocks and replaces `sorry` placeholders with completed proofs. The system runs across stateless CPU machines coordinated via Google Kubernetes Engine (GKE), with infrastructure that scales to thousands of parallel proof attempts.

The generative policy produces both formal Lean tactics and informal annotations (interleaved comments and chain-of-thought reasoning), which serve as both a reasoning aid and a training signal.

Statement autoformalization: the system converts natural language problem statements into formal Lean 4 statements, validated through the Lean REPL before proof search begins.

---

## Benchmark Results

| Setting | Result |
|---------|--------|
| IMO 2025 | 5/6 problems solved (gold-medal equivalent) |
| Mathlib contributions | Novel theorems: Niven's theorem, Gauss-Lucas theorem, eigenvalue-characteristic polynomial relationship |
| Textbook verification | Identified 4 errors in Terence Tao's *Analysis I* |

Problems solved at IMO 2025:
- **Problem 1** (Sunny Lines): Algebraic proof via derangements
- **Problem 3** (Bonza Functions): Characterized functions with novel auxiliary set definitions
- **Problem 4** (Sums of Three Divisors): Formalization caught subtle errors in the informal reasoning
- **Problem 5** (Inekoalaty Game): Identified optimal strategies and non-obvious auxiliary definitions

The system demonstrated favorable scaling: performance improves with larger models and more parallel search.

---

## Lean Integration

Aristotle is built natively around Lean 4. Specific integration points:

- **REPL-based interaction**: Lean code blocks are submitted incrementally; the REPL returns proof states after each tactic
- **Mathlib compatibility**: Solutions are compatible with Lean 4's Mathlib library and require no unsound axioms (e.g., `sorryAx`)
- **Ecosystem contributions**: Pull requests submitted to Mathlib and the Polynomial Freiman-Ruzsa formalization
- **Kernel checking**: All final proofs are verified through Lean's type-checking kernel for well-typedness and axiom usage

---

## Limitations

- **Focus on pure mathematics**: Aristotle is designed for mathematical theorem proving (olympiad-style), not for code verification or program correctness proofs
- **Infrastructure scale**: Achieving top performance requires significant compute (200B+ parameter model, thousands of parallel REPL instances)
- **Human scaffolding for complex projects**: For large-scale formalization, high-level human guidance remains valuable
- **Not publicly available**: Aristotle is a research system from Harmonic; no public API or open weights are available as of the paper's publication

---

## References

- Paper: [Aristotle: IMO-level Automated Theorem Proving](https://arxiv.org/html/2510.01346v1)
- Organization: [Harmonic](https://harmonic.fun)