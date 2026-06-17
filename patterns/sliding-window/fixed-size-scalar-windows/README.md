# Fixed-Size Scalar Windows

## When to recognize this pattern

Use this when the window length is fixed and the maintained state is a simple scalar like sum, average, or count.

Common signals:
- fixed window size k
- moving average
- fixed-length subarray

## Problem list

| Order | Problem | Label | Maintained State |
| --- | --- | --- | --- |
| 1 | [Maximum Average Subarray I (LC 643)](https://leetcode.com/problems/maximum-average-subarray-i/) | MUST DO | Running Sum |