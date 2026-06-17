# Builder + Explorer (Same Direction)

## When to recognize this pattern

Use this when both pointers move left to right: one scans the input and one builds the valid portion in-place.

Common signals:
- in-place array modification
- remove or compact values
- stable write pointer
- return the new logical length

## Problem list

| Order | Problem | Difficulty | Priority | Core Pattern |
| --- | --- | --- | --- | --- |
| 1 | [Remove Duplicates from Sorted Array (LC 26)](https://leetcode.com/problems/remove-duplicates-from-sorted-array/) | Easy | MUST DO | Slow builds, fast scans |
| 2 | [Move Zeroes (LC 283)](https://leetcode.com/problems/move-zeroes/) | Easy | MUST DO | Compact non-zero values |
| 3 | [Remove Element (LC 27)](https://leetcode.com/problems/remove-element/) | Easy | MUST DO | Build valid result |

## Additional matched solutions

The combined file also contains LC 443 String Compression and LC 80 Remove Duplicates from Sorted Array II. They fit this same builder/explorer idea, so they are kept here.