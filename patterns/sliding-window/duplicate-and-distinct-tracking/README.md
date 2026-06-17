# Duplicate and Distinct Tracking

## When to recognize this pattern

Use this when window validity depends on duplicates or the number of distinct values.

Common signals:
- repeating characters
- at most k distinct
- distinct-count constraints

## Problem list

| Order | Problem | Label | Maintained State |
| --- | --- | --- | --- |
| 1 | [Longest Substring Without Repeating Characters (LC 3)](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | MUST DO | Duplicate Prevention |
| 2 | [Longest Substring with At Most K Distinct Characters (LC 340)](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/) | MUST DO | Distinct Count <= K |
| 3 | [Fruit Into Baskets (LC 904)](https://leetcode.com/problems/fruit-into-baskets/) | MUST DO | At Most 2 Distinct |