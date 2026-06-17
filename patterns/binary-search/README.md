# Binary Search

Binary Search is the pattern of using order or monotonicity to discard half of a search space at every step. The search space may be array indexes, boundary positions, rotated halves, answer values, slopes, matrix values, or partitions.

## How to recognize Binary Search

Use Binary Search when:

- The search space has an ordering.
- A condition around `mid` tells you which side cannot contain the answer.
- You can define a monotonic true/false boundary, sorted half, slope direction, or feasibility predicate.

## Sub-patterns

| Sub-pattern | Use when | Link |
| --- | --- | --- |
| Core Philosophy | You need the mental model, monotonicity, and loop rules | [core-philosophy](core-philosophy/) |
| Decision Tree | You need to choose the right binary-search pattern | [decision-tree](decision-tree/) |
| Classic Binary Search | Exact target search in a sorted 1D array | [classic-binary-search](classic-binary-search/) |
| Lower / Upper Bound | First/last position, first true, last true, boundary counting | [lower-upper-bound](lower-upper-bound/) |
| Search in Rotated Array | Sorted array has been rotated, with or without duplicates | [search-in-rotated-array](search-in-rotated-array/) |
| Binary Search on Answer | Minimum feasible or maximum feasible answer search | [binary-search-on-answer](binary-search-on-answer/) |
| Peak / Valley Search | Slope, peak, mountain, or local optimum logic | [peak-valley-search](peak-valley-search/) |
| Unsorted | Preserved notes whose original category is outside this target tree | [unsorted](unsorted/solutions.md) |

## Revision strategy

1. Start with [core philosophy](core-philosophy/) and say the three things out loud: search space, monotonic rule, discard rule.
2. Use the [decision tree](decision-tree/) before choosing a template.
3. Solve classic and boundary search first.
4. Move to rotated arrays and peak/mountain search.
5. Finish with answer-space search, because it requires proving `can(mid)` is monotonic.
6. Review unsorted notes separately; they came from extra categories in the original Binary Search roadmap.

## Common mistakes

- Coding before defining the search space.
- Not knowing whether the problem is exact search, first true, or last true.
- Losing the answer when updating `left` or `right`.
- Using `while (left <= right)` where a boundary template needs `while (left < right)`.
- Overflowing `mid` instead of using `left + (right - left) / 2`.
- Forgetting duplicate handling in rotated arrays.
- Not proving that `can(mid)` is monotonic in answer-space problems.
- Accessing `mid + 1` in peak search without using a loop condition that keeps it valid.

## Visual assets

See [assets/README.md](assets/README.md).