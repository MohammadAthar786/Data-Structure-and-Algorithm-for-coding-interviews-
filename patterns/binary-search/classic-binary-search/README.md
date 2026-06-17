# Classic Binary Search

# Category: Classic Binary Search

## Core Idea

Find an exact target inside a sorted search space.

## Recognition Signals

- Input is sorted.
- Question asks whether a target exists or asks for its index.
- Each comparison with `mid` tells which half can be ignored.

## Invariant

```text
If the target exists, it is inside [left, right].
```

## Problem Ladder

| Order | Problem | Label | Concept |
| :--- | :--- | :--- | :--- |
| 1 | Binary Search (LC 704) | MUST DO | Standard binary search |

## What Makes This Category Different

You are searching for an exact value, not a boundary or answer range.

## Common Mistakes

- Using `mid = (left + right) / 2` without overflow awareness.
- Forgetting `left <= right` for exact search.
- Returning `left` when the problem needs exact target existence.

## Problem List

- LC 704 - Binary Search

## Solutions

See [solutions.md](solutions.md).