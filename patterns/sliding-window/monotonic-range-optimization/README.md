# Monotonic Range Optimization

## When to recognize this pattern

Use this when the moving range needs efficient access to max, min, or best candidates.

Common signals:
- sliding maximum or minimum
- max-min range validity
- candidate optimization inside a window

## Problem list

| Order | Problem | Label | Maintained State |
| --- | --- | --- | --- |
| 1 | [Sliding Window Maximum (LC 239)](https://leetcode.com/problems/sliding-window-maximum/) | MUST DO | Monotonic Decreasing Deque |
| 2 | [Longest Continuous Subarray with Absolute Diff <= Limit (LC 1438)](https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/) | MUST DO | Min + Max Deques |
| 3 | [Shortest Subarray with Sum >= K (LC 862)](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/) | Advanced | Monotonic Prefix Candidates |
| 4 | [Jump Game VI (LC 1696)](https://leetcode.com/problems/jump-game-vi/) | Advanced | DP Candidate Deque |

## Notes

The original written solutions include LC 239 and LC 1438. LC 862 and LC 1696 are listed in the sheet but were not found in the combined solution file.