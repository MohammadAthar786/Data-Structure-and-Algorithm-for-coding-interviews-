# Lower / Upper Bound

# Category: Boundary Search

## Core Idea

Find the first or last position where a condition changes.

```text
False False False True True True
                  ^
               boundary
```

## Recognition Signals

- First occurrence, last occurrence, lower bound, upper bound.
- First bad, first valid, first true.
- Count values satisfying a condition.

## Invariant

```text
The answer lies at the transition point, and the loop must not discard it.
```

## Problem Ladder

| Order | Problem | Label | Concept |
| :--- | :--- | :--- | :--- |
| 1 | First Bad Version (278) | MUST DO | First True |
| 2 | Find First and Last Position (34) | MUST DO | Lower/Upper Bound |
| 3 | Count Negative Numbers in Sorted Matrix (1351) | Variant | Boundary per row |

## What Makes This Category Different

You usually do not stop when `condition(mid)` is true. You save or keep the candidate and continue searching toward the boundary.

## Common Mistakes

- Returning immediately on match when first/last position is required.
- Mixing first-true and last-true movement rules.
- Infinite loops from `while (left < right)` with wrong midpoint bias.

## Problem List

- LC 278 - First Bad Version
- LC 34 - Find First and Last Position
- LC 1351 - Count Negative Numbers in Sorted Matrix

## Solutions

See [solutions.md](solutions.md).