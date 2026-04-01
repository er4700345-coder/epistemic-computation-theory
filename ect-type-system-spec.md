# ECT Type System Specification for SLIME

**Document:** ECT-TYPES-SPEC-v0.1  
**Status:** Draft  
**Author:** Divine Sunday Ofuowoicho  
**Date:** April 1, 2026  
**Target:** SLIME Language (slime-ng) compiler

---

## 1. Overview

This document specifies the **Epistemic Type System (ETS)** — an extension to the SLIME language's type system that encodes the epistemic status of values at compile time.

Standard type systems encode *what kind of data* a value is. The ETS encodes *how certain we are about that data* — and formally tracks that certainty through the computation.

This is a direct implementation of ECT-3's Observer Context principle: the epistemic status of a value is not absolute. It is relative to the context in which it was computed.

---

## 2. Epistemic Types

### 2.1 Type Constructors

The ETS introduces five new type constructors over any existing SLIME type `T`:

```slime
certain<T>        // Fully verified. Classical type behavior.
uncertain<T>      // Estimated. Carries a confidence bound.
contradictory<T>  // Internally inconsistent. Cannot be resolved without external input.
collapsed<T>      // Epistemically destroyed. Value existed but is no longer recoverable.
observer<T, O>    // Context-relative. Valid only within observer context O.
```

### 2.2 Type Hierarchy

```
        certain<T>
            |
        uncertain<T>
            |
    ┌───────┴───────┐
contradictory<T>  collapsed<T>
            |
        observer<T, O>
```

- `certain<T>` is the most restrictive: full verification required.
- `uncertain<T>` allows probabilistic bounds.
- `contradictory<T>` signals a logical conflict that must be handled.
- `collapsed<T>` signals epistemic destruction — the value cannot be used.
- `observer<T, O>` is context-scoped — valid only within context `O`.

### 2.3 Subtyping Rules

```
certain<T>  <:  uncertain<T>
uncertain<T>  <:  observer<T, O>   // for any O that includes the current context
contradictory<T>  requires explicit resolution
collapsed<T>  cannot be used as any other type
```

---

## 3. Syntax

### 3.1 Variable Declarations

```slime
let x: certain<i64> = 42;
let y: uncertain<f64> = estimate(sensor_read(), confidence: 0.95);
let z: observer<bool, ctx_network> = verify_in_context(network_state, ctx_network);
```

### 3.2 Type Annotations on Functions

```slime
fn verify_balance(account_id: u64) -> uncertain<f64> {
    // Returns a balance estimate — certainty depends on sync state
}

fn collapse_value<T>(x: uncertain<T>) -> collapsed<T> {
    // Explicitly collapses an uncertain value
    // After this, x is unusable
}
```

### 3.3 Epistemic Guards

```slime
match x {
    certain(val) => { /* use val freely */ }
    uncertain(val, confidence) => {
        if confidence > 0.9 { /* proceed */ }
        else { /* request fresh verification */ }
    }
    contradictory(val_a, val_b) => {
        // Must resolve contradiction before proceeding
        resolve_contradiction(val_a, val_b)
    }
    collapsed => {
        // Cannot proceed — epistemic destruction
        panic!("value epistemically collapsed")
    }
}
```

### 3.4 Context Scoping

```slime
context ctx_local {
    // Values computed here are tagged observer<T, ctx_local>
    // They cannot escape this context without explicit promotion
    let x: observer<bool, ctx_local> = local_check();
}

// Promoting an observer value — requires explicit proof of context equivalence
let y: uncertain<bool> = promote(x, proof: ctx_equivalence(ctx_local, ctx_global));
```

---

## 4. Compiler Behavior

### 4.1 Epistemic Type Inference

The SLIME compiler infers epistemic types where possible:

```slime
// If sensor_read() is marked uncertain<f64> in its signature,
// all downstream computations propagate uncertainty:
let temp = sensor_read();         // uncertain<f64>
let adjusted = temp * 1.05;       // uncertain<f64> — uncertainty propagates
let rounded = floor(adjusted);    // uncertain<i64> — still uncertain
```

### 4.2 Certainty Propagation Rules

| Operation | Input Types | Output Type |
|-----------|------------|-------------|
| `+`, `-`, `*`, `/` | `certain<T>` × `certain<T>` | `certain<T>` |
| `+`, `-`, `*`, `/` | `certain<T>` × `uncertain<T>` | `uncertain<T>` |
| `+`, `-`, `*`, `/` | `uncertain<T>` × `uncertain<T>` | `uncertain<T>` (widened bounds) |
| any op | involves `contradictory<T>` | `contradictory<T>` |
| any op | involves `collapsed<T>` | **compile error** |
| any op | `observer<T,O>` outside `O` | **compile error** |

### 4.3 Compile-Time Errors

```
ECT-E001: Cannot use collapsed<T> value
ECT-E002: observer<T, O> escaping context O without promotion
ECT-E003: contradictory<T> used without resolution
ECT-E004: Certainty downgrade without explicit annotation
```

### 4.4 Warnings

```
ECT-W001: uncertain<T> used in branch condition — consider certainty guard
ECT-W002: Confidence bound below 0.5 — value may be unreliable
ECT-W003: Context promotion loses observer binding — verify equivalence
```

---

## 5. Runtime Behavior

### 5.1 Epistemic Values at Runtime

Epistemic types carry metadata at runtime:

```
certain<T>        → { value: T }
uncertain<T>      → { value: T, confidence: f64, bounds: (T, T) }
contradictory<T>  → { value_a: T, value_b: T, conflict_reason: String }
collapsed<T>      → { tombstone: true, last_known: Option<T> }
observer<T, O>    → { value: T, context_id: ContextId }
```

### 5.2 Epistemic Collapse at Runtime

If an operation would produce an invalid epistemic state, the runtime triggers an **epistemic collapse event**:

```slime
// Runtime collapse handler
on_epistemic_collapse(|event: CollapseEvent| {
    log!("ECT collapse: {:?}", event.reason);
    // Handle or propagate
});
```

---

## 6. Standard Library Extensions

### 6.1 `ect::verify`

```slime
use ect::verify;

// Attempt to promote uncertain<T> to certain<T>
// Returns Result<certain<T>, VerificationError>
let result = verify::promote(uncertain_value, verifier: my_verifier);
```

### 6.2 `ect::context`

```slime
use ect::context;

// Create a new observer context
let ctx = context::new("my_context");

// Check if two contexts are equivalent
let equiv = context::equivalent(ctx_a, ctx_b);

// Merge contexts (implements O₁ ⊕ O₂ from ECT-3)
let merged = context::compose(ctx_a, ctx_b);
```

### 6.3 `ect::boundary`

```slime
use ect::boundary;

// Check if a proposition is at the epistemic boundary
// Returns EpistemicStatus::Reachable | EpistemicStatus::Boundary | EpistemicStatus::Collapsed
let status = boundary::probe(proposition, context: current_ctx);
```

---

## 7. Implementation Plan

### Phase 1 — Type Checker (Current Target)
- [ ] Add epistemic type constructors to SLIME's type grammar
- [ ] Implement certainty propagation rules in type checker
- [ ] Implement compile-time errors ECT-E001 through ECT-E004

### Phase 2 — Runtime
- [ ] Implement epistemic value metadata structs
- [ ] Implement runtime collapse event system
- [ ] Add epistemic guard pattern matching

### Phase 3 — Standard Library
- [ ] Implement `ect::verify`
- [ ] Implement `ect::context`
- [ ] Implement `ect::boundary`

### Phase 4 — Tooling
- [ ] Epistemic type visualizer in SLIME LSP
- [ ] Certainty flow graph for debugging
- [ ] ECT-TYPES documentation generator

---

## 8. Design Rationale

### Why embed epistemic types in the language?

ECT-3 claims epistemic boundaries are observer-context-relative. A type system that encodes context-relative epistemic status at compile time makes this a first-class programming primitive — not an afterthought.

The result: **programs that know what they don't know, and prove it at compile time.**

### Why not just use Result<T, E>?

`Result<T, E>` models success/failure at runtime. The ETS models epistemic status at compile time — the difference between *a value that might fail* and *a value whose certainty is provably bounded*.

### Connection to ECT-1

A `collapsed<T>` value is the runtime manifestation of epistemic collapse as defined in ECT-1: the system attempted to verify a proposition, and the attempt destroyed the epistemic state needed to complete it.

---

*Specification v0.1 — Living document. Updated as SLIME compiler evolves.*  
*See: github.com/er4700345-coder/slime-ng and github.com/er4700345-coder/epistemic-computation-theory*
