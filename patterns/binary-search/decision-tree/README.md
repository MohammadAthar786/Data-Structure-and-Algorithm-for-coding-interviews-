# Binary Search Decision Tree

Use this before choosing a template.

## Quick Classifier

| Question | If Yes | Go To |
| :--- | :--- | :--- |
| Is the input a normally sorted 1D array and you need exact target? | Standard comparison with `nums[mid]` | Classic Binary Search |
| Do you need first/last occurrence or first valid value? | Search a transition point | Boundary Search |
| Does the problem depend on parity, pair positions, or nearby indexes? | Index behavior matters | Index Twist |
| Is the array sorted but rotated? | One half remains sorted | Rotated Sorted Array |
| Does the problem mention peak, mountain, or slope? | Compare `mid` with neighbor | Peak / Mountain Search |
| Are you minimizing/maximizing a value like speed, capacity, days, distance, or time? | Search answer space with `can(mid)` | Binary Search on Answer |
| Is the input a sorted matrix? | Use row/column/value elimination | 2D Binary Search |
| Are there two sorted arrays and median in log time? | Search partition | Median of Two Sorted Arrays |

## Flow

```text
Start
 |
 |-- Exact target in sorted array?
 |      -> Classic Binary Search
 |
 |-- First / last / lower bound / upper bound?
 |      -> Boundary Search
 |
 |-- Sorted array but index relationship is special?
 |      -> Index Twist
 |
 |-- Rotated sorted array?
 |      -> Rotated Sorted Array
 |
 |-- Peak or mountain?
 |      -> Peak / Mountain Search
 |
 |-- "Minimum possible" or "maximum possible" answer?
 |      -> Binary Search on Answer
 |
 |-- Sorted matrix?
 |      -> 2D Binary Search
 |
 |-- Two sorted arrays + median?
 |      -> Special Partition Search
```

## Template Selection

| Category | Loop Style | Return |
| :--- | :--- | :--- |
| Classic | `while (left <= right)` | exact index or `-1` |
| First True | `while (left < right)` | `left` |
| Last True | `while (left < right)` with upper-mid bias | `left` |
| Rotated Search | `while (left <= right)` | target index or pivot |
| Peak Search | `while (left < right)` | peak index |
| Answer Search | `while (left < right)` | minimum/maximum feasible answer |

## Interview One-Liner

> I am not guessing midpoints; I am using monotonicity to prove one side cannot contain the answer.
