# Opposite End Pointers

## When to recognize this pattern

Use this when one pointer starts at the beginning and one at the end, then both move inward based on a comparison or optimization rule.

Common signals:
- sorted array
- pair, triplet, or quadruplet sum
- palindrome-style comparison
- maximizing or minimizing using two boundaries

## Problem list

| Order | Problem | Difficulty | Priority | Core Pattern |
| --- | --- | --- | --- | --- |
| 1 | [Two Sum II - Input Array Is Sorted (LC 167)](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) | Easy | MUST DO | Compare sum and move inward |
| 2 | [3Sum (LC 15)](https://leetcode.com/problems/3sum/) | Medium | MUST DO | Fix one element + two pointers |
| 3 | [3Sum Closest (LC 16)](https://leetcode.com/problems/3sum-closest/) | Medium | Recommended | Track closest sum |
| 4 | [4Sum (LC 18)](https://leetcode.com/problems/4sum/) | Medium | Recommended | Two fixed + two pointers |
| 5 | [Container With Most Water (LC 11)](https://leetcode.com/problems/container-with-most-water/) | Medium | MUST DO | Move smaller height |
| 6 | [Trapping Rain Water (LC 42)](https://leetcode.com/problems/trapping-rain-water/) | Hard | MUST DO | Move side with smaller boundary |

## Notes

The original written solutions include Two Sum II-style two pointers, 3Sum Closest, 4Sum, Container With Most Water, and Trapping Rain Water. A standalone 3Sum solution was not found in the combined solution file.