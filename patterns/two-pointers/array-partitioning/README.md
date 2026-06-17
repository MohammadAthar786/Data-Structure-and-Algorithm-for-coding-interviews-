# Array Partitioning

## When to recognize this pattern

Use this when the array must be rearranged into regions, usually in-place.

Common signals:
- partition around a pivot
- group values by condition
- Dutch National Flag style regions
- QuickSort or QuickSelect partition logic

## Problem list

| Order | Problem | Difficulty | Priority | Core Pattern |
| --- | --- | --- | --- | --- |
| 1 | [Sort Colors (LC 75)](https://leetcode.com/problems/sort-colors/) | Medium | MUST DO | Three-way partition |
| 2 | Move Negatives to One Side | Easy | Recommended | Two-way partition |
| 3 | [Kth Largest Element in an Array / QuickSelect (LC 215)](https://leetcode.com/problems/kth-largest-element-in-an-array/) | Medium | MUST DO | Partition + selection |
| 4 | Partition Around Pivot | Easy-Medium | Recommended | QuickSort partition logic |

## Notes

The original written solutions include Move Negatives to One Side, Sort Colors, and QuickSort partition logic. A complete QuickSelect solution was not found in the combined file.