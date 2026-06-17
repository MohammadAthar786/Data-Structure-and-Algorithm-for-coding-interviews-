# 2 Pointers Sheet

<!-- DSA_SHEET_V2_LAYER -->


Philosophy: Core Pattern -> Sub-pattern -> Problem ladder -> Bridge to the next problem. The goal is not to collect maximum problems. The goal is to recognize the idea, know the invariant/state, and modify a base template for variants.

## Labels

- MUST DO: high-return interview problem; do not skip if time is short.
- Variant: one important twist after the base problem.
- Advanced: solve after the base and variants are comfortable.
- Cross-link: useful connection, but the problem belongs mainly in another sheet.

# Two Pointers – Pattern-Based Sheet

## Category 1: Opposite End Pointers (Inward Movement)

### Core Idea

Start one pointer at the beginning and one at the end.

Move pointers inward based on some condition or optimization goal.

Typical signals:

* Sorted array
* Pair/triplet sum problems
* Palindrome checking
* Maximization/minimization using two boundaries

---

| # | Problem                   | Difficulty | Priority    | Core Pattern                    |
| - | ------------------------- | ---------- | ----------- | ------------------------------- |
| 1 | Two Sum II (Sorted Array) | Easy       | ⭐ MUST DO   | Compare sum and move inward     |
| 2 | 3Sum                      | Medium     | ⭐ MUST DO   | Fix one element + two pointers  |
| 3 | 3Sum Closest              | Medium     | Recommended | Track closest sum               |
| 4 | 4Sum                      | Medium     | Recommended | Two fixed + two pointers        |
| 5 | Container With Most Water | Medium     | ⭐ MUST DO   | Move smaller height             |
| 6 | Trapping Rain Water       | Hard       | ⭐ MUST DO   | Move side with smaller boundary |

### Generic Template

```python
left = 0
right = len(arr) - 1

while left < right:

    if condition:
        left += 1

    elif condition:
        right -= 1

    else:
        process_answer()
```

### Duplicate Skipping (3Sum / 4Sum)

```python
while left < right and nums[left] == nums[left - 1]:
    left += 1

while left < right and nums[right] == nums[right + 1]:
    right -= 1
```

Use AFTER processing a valid combination.

---

## Category 2: Builder + Explorer (Same Direction)

### Core Idea

Both pointers move left → right.

One pointer explores.

One pointer builds/maintains the valid portion.

Typical signals:

* In-place array modification
* Remove elements
* Compact data
* Stable rearrangement

---

| # | Problem                             | Difficulty | Priority  | Core Pattern            |
| - | ----------------------------------- | ---------- | --------- | ----------------------- |
| 7 | Remove Duplicates from Sorted Array | Easy       | ⭐ MUST DO | Slow builds, fast scans |
| 8 | Move Zeroes                         | Easy       | ⭐ MUST DO | Compact non-zero values |
| 9 | Remove Element                      | Easy       | ⭐ MUST DO | Build valid result      |

### Generic Template

```python
slow = 0

for fast in range(len(nums)):

    if valid(nums[fast]):
        nums[slow] = nums[fast]
        slow += 1
```

---

## Category 3: Floyd Cycle Detection (Fast & Slow)

### Core Idea

Two pointers move at different speeds.

If they meet, a cycle exists.

Typical signals:

* Cycle detection
* Repeated states
* Duplicate represented as graph/cycle

---

| #  | Problem               | Difficulty | Priority  | Core Pattern                |
| -- | --------------------- | ---------- | --------- | --------------------------- |
| 10 | Happy Number          | Easy       | ⭐ MUST DO | Cycle detection in sequence |
| 11 | Find Duplicate Number | Medium     | ⭐ MUST DO | Floyd cycle detection       |

### Generic Template

```python
slow = start
fast = start

while True:

    slow = move_one_step(slow)

    fast = move_one_step(
            move_one_step(fast)
          )

    if slow == fast:
        break
```

---

## Category 4: String Two Pointers

### Core Idea

Use string properties such as:

* Palindrome
* Reversal
* Character skipping
* Character swapping

Typical signals:

* Compare from both ends
* Reverse in-place
* Ignore specific characters

---

| #  | Problem                    | Difficulty  | Priority    | Core Pattern            |
| -- | -------------------------- | ----------- | ----------- | ----------------------- |
| 12 | Valid Palindrome           | Easy        | ⭐ MUST DO   | Ignore non-alphanumeric |
| 13 | Valid Palindrome II        | Easy        | ⭐ MUST DO   | Allow one deletion      |
| 14 | Reverse String             | Easy        | ⭐ MUST DO   | Swap from ends          |
| 15 | Reverse Vowels of a String | Easy-Medium | Recommended | Swap vowels only        |
| 16 | Backspace String Compare   | Easy        | Recommended | Process from end        |

### Generic Template

```python
left = 0
right = len(s) - 1

while left < right:

    if condition:
        left += 1

    elif condition:
        right -= 1

    else:
        process()
```

---

## Category 5: Array Partitioning

### Core Idea

Partition array into regions.

Common in:

* QuickSort
* QuickSelect
* Dutch National Flag

Typical signals:

* Rearrangement
* Pivot
* Segmentation
* In-place sorting

---

| #  | Problem                           | Difficulty  | Priority    | Core Pattern              |
| -- | --------------------------------- | ----------- | ----------- | ------------------------- |
| 17 | Sort Colors (Dutch National Flag) | Medium      | ⭐ MUST DO   | Three-way partition       |
| 18 | Move Negatives to One Side        | Easy        | Recommended | Two-way partition         |
| 19 | QuickSelect (Kth Largest Element) | Medium      | ⭐ MUST DO   | Partition + selection     |
| 20 | Partition Around Pivot            | Easy-Medium | Recommended | QuickSort partition logic |

### Generic Partition Template

```python
pivot = nums[right]

i = left

for j in range(left, right):

    if nums[j] <= pivot:
        nums[i], nums[j] = nums[j], nums[i]
        i += 1

nums[i], nums[right] = nums[right], nums[i]
```

---

# Placement Shortlist (If Time Is Limited)

Solve these first:
```python
⭐ Two Sum II
⭐ 3Sum
⭐ Container With Most Water
⭐ Trapping Rain Water
⭐ Remove Duplicates
⭐ Move Zeroes
⭐ Remove Element
⭐ Happy Number
⭐ Find Duplicate Number
⭐ Valid Palindrome
⭐ Valid Palindrome II
⭐ Reverse String
⭐ Sort Colors
⭐ QuickSelect
```
These cover roughly 80–90% of classic Two Pointer interview patterns.
