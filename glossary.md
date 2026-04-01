# ECT Glossary

A reference for all formal terms used across the Epistemic Computation Theory framework.

---

## A

**Asymmetric Unknowable**
A proposition `P` that is in `∂E(S₁, O₁)` but not in `∂E(S₂, O₂)` — unknowable to one system but reachable by another with a different observer context. See ECT-3.

---

## C

**Computational Unknowability**
A proposition `P` is computationally unknowable for system `S` if `P ∈ ∂E(S)` and resolving `P` requires `S` to achieve a self-modeling fixed point that is computationally unreachable within `S`'s resource bounds.

**Complete Self-Model `Σ(S)`**
The total representation of system `S`, including its memory model, transition function, resource bound, and full epistemic state history. Computing `Σ(S)` is the central challenge of ECT-1.

**Context Composition `O₁ ⊕ O₂`**
The merged observer context produced when two systems with contexts `O₁` and `O₂` interact. Conjecture: combined contexts shrink the epistemic boundary.

---

## E

**ECT**
Epistemic Computation Theory. The unified research framework studying the fundamental limits of knowledge within computational systems.

**ECT-1**
The Epistemic Incompleteness of Computation conjecture. States that every sufficiently complex system has a non-empty set of propositions it cannot verify due to self-modeling cost exceeding its resource bound.

**ECT-2**
The Epistemic Irreversibility conjecture. States that every genuine epistemic update destroys prior epistemic content irreversibly.

**ECT-3**
The Observer Epistemic Relativity conjecture. States that epistemic boundaries are observer-relative — identical systems with different contexts have different unknowable sets.

**Epistemic Boundary `∂E(S)`**
The set of well-formed, decidable propositions that system `S` cannot verify without achieving an unreachable self-model. The formal object of study in ECT-1.

**Epistemic Collapse**
The event that occurs when `S` attempts to verify a proposition requiring complete self-knowledge, and the attempt itself destroys the epistemic state needed to complete the verification. A computational paradox.

**Epistemic Irreversibility**
See ECT-2. The property that epistemic state transitions cannot be undone without cost exceeding the original computation.

**Epistemic Residue `Δ(C)`**
The set of propositions lost from the epistemic state after computation `C` is applied. `Δ(C) = E(S,t) \ E(S, t+1)`. ECT-2 claims this is always non-empty for non-trivial computations.

**Epistemic State `E(S, t)`**
The complete set of propositions that system `S` has verified as true at time `t`. The dynamic object that ECT tracks across computations.

---

## O

**Observer Context `O`**
A tuple `(P_prior, F_input, Γ_interaction)` describing the prior beliefs, input filter, and interaction history that shapes a system's epistemic capabilities. The central object of ECT-3.

**Observer Epistemic Relativity**
See ECT-3. The property that epistemic boundaries are not absolute but relative to the observer context of the system.

---

## R

**Resource Bound `R(S)`**
The computational resource constraint on system `S` — time, space, or a combined measure. ECT-1 argues that `cost(Σ(S)) > R(S)` for sufficiently complex systems.

---

## S

**Self-Referential Proposition**
A proposition `P` whose verification `V_S(P)` requires access to some component of `S`'s own complete self-model `Σ(S)`. The core object of study in ECT-1.

**Shared Unknowable**
A proposition in `∂E(S₁, O₁) ∩ ∂E(S₂, O₂)` — unknowable to all systems regardless of observer context. ECT-3 claims these exist.

---

## U

**`U(S)` (Unknowable Set)**
The non-empty set of propositions that system `S` cannot verify, as defined by ECT-1. Analogous to the set of undecidable propositions in classical computability, but strictly distinct — elements of `U(S)` are decidable in principle, just unreachable for `S`.

---

## V

**Verification Procedure `V_S(P)`**
The computational procedure by which system `S` attempts to verify proposition `P`. ECT-1 studies the cases where `V_S(P)` cannot complete.

---

*Last updated: April 1, 2026. Glossary is a living document.*
