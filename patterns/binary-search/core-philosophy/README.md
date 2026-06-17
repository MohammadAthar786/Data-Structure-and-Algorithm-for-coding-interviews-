# Core Philosophy: Binary Search

Binary search is not only "find target in sorted array."

It is the pattern of finding a target, boundary, peak, pivot, or feasible answer inside a monotonic search space.

## The Three Questions

| Question | What You Must Identify |
| :--- | :--- |
| What is the search space? | Indexes, values, answer range, rows/columns, or partitions |
| What is monotonic? | A sorted order, true/false boundary, slope, or feasibility condition |
| What discards half? | Comparison, condition check, sorted-half check, or `can(mid)` |

## Applicability Test

Binary search works when:

- The search space has order.
- A monotonic property exists.
- One check lets you eliminate half the space.

## Common Signals

| Signal | Usually Means |
| :--- | :--- |
| sorted array | Classic binary search |
| first / last occurrence | Boundary search |
| rotated sorted array | Modified sorted array |
| peak / mountain | Slope-based search |
| minimum feasible / maximum feasible | Binary search on answer |
| capacity / speed / days / distance | Feasibility search |
| matrix with sorted rows/columns | 2D binary search |

## Base Loop Mental Model

```cpp
while (left <= right) {
    int mid = left + (right - left) / 2;

    if (condition(mid)) {
        // keep one half
    } else {
        // keep the other half
    }
}
```

## Interview Rule

Before coding, say:

> I will define the search space, prove the monotonic condition, then maintain an invariant that the answer is never discarded.

## Visual Intuition

No dedicated core-philosophy visual was found. Binary Search on Answer has the closest search-space visual in [assets/binary-search-on-answer](../assets/binary-search-on-answer/).
