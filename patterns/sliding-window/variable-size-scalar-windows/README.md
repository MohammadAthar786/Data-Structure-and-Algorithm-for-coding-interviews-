# Variable-Size Scalar Windows

## When to recognize this pattern

Use this when the window grows and shrinks based on a simple numeric condition.

Common signals:
- longest valid range
- shortest valid range
- limited violations
- positive-sum shrinking

## Problem list

| Order | Problem | Label | Maintained State |
| --- | --- | --- | --- |
| 1 | [Max Consecutive Ones (LC 485)](https://leetcode.com/problems/max-consecutive-ones/) | MUST DO | Consecutive Count |
| 2 | [Max Consecutive Ones II (LC 487)](https://leetcode.com/problems/max-consecutive-ones-ii/) | MUST DO | Zero Count <= 1 |
| 3 | [Longest Subarray of 1s After Deleting One Element (LC 1493)](https://leetcode.com/problems/longest-subarray-of-1s-after-deleting-one-element/) | Variant | One Deletion Allowance |
| 4 | [Minimum Size Subarray Sum (LC 209)](https://leetcode.com/problems/minimum-size-subarray-sum/) | MUST DO | Running Sum >= Target |
| 5 | [Maximum Points You Can Obtain from Cards (LC 1423)](https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards/) | Advanced | Complement Window Sum |