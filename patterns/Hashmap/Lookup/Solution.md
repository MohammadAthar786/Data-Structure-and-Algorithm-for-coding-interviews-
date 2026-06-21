# LC 217 contains Duplicate ( Easy )

https://youtu.be/0LIctkgJ2hQ?si=3whVavHzp3jnP5va

**Problem**: Given an array of integers, determine if any value appears at least twice. Return true if so, false if every element is distinct.

**Pattern**: "Has duplicate / has seen before" → think **hash set**. Whenever a problem asks "does X repeat" or "have I seen this before," a set giving O(1) lookup is the trigger.

**Core idea**: Walk through the array once. Keep a set of numbers seen so far. For each number, check if it's already in the set — if yes, you found a duplicate, return true. If no, add it to the set and move on. If you finish the whole array without a repeat, return false.

**Invariant**: At the moment you're processing index `i`, the set contains exactly the distinct values from indices `0` to `i-1`. So checking the set before inserting always tells you "have I seen this exact value earlier in the array."

**Dry run**: `nums = [1, 2, 3, 1]`

| i   | nums[i] | in set? | action      | set after |
| --- | ------- | ------- | ----------- | --------- |
| 0   | 1       | no      | add         | {1}       |
| 1   | 2       | no      | add         | {1,2}     |
| 2   | 3       | no      | add         | {1,2,3}   |
| 3   | 1       | **yes** | return true | —         |

**Edge cases**:

- Empty array or single element → no duplicates possible, return false.
- All elements identical → duplicate found on the second element.
- Negative numbers / zero → no special handling needed, hashing works the same.

**Complexity**: O(n) time, O(n) space.

**Alternative**: sort the array first, then check adjacent elements for equality — O(n log n) time, O(1) extra space (if in-place sort allowed). Worth mentioning in an interview as a space-time tradeoff.

**Recall hook**: "Repeat detection in one pass → set."

```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        unordered_set<int> seen;
        for (int num : nums) {
            if (seen.count(num)) {
                return true;       // already seen this value -> duplicate
            }
            seen.insert(num);
        }
        return false;              // finished array, no repeats found
    }
};
```

## Follow up LC 219

# LC 128

## LC 128 — Longest Consecutive Sequence

https://youtu.be/oO5uLE7EUlM?si=BYx6EfnV76JKV3Ai

**Pattern:** HashMap / HashSet

### 1. Core Pattern Recognition

Problem asks:

> Find longest sequence of consecutive numbers, but array is unsorted.

Example:

```text
nums = [100, 4, 200, 1, 3, 2]
answer = 4
because 1,2,3,4 is longest consecutive sequence
```

Clues that suggest **HashSet**:

```text
Unsorted array
Need to check whether num + 1 exists
Need O(N), so sorting O(N log N) is not ideal
Fast lookup needed
```

Main idea:

```text
Store all numbers in a set
Then check in O(1) whether next number exists
```

---

### 2. Naive Starting Point

For every number, try to build a sequence from it.

```text
nums = [1, 2, 3, 4]

start from 1 -> checks 2,3,4
start from 2 -> checks 3,4
start from 3 -> checks 4
start from 4 -> checks nothing
```

Problem:
We are repeating the same work again and again.

### Naive C++ snippet

```cpp
int longestConsecutive(vector<int>& nums) {
    int longest = 0;

    for (int num : nums) {
        int current = num;
        int length = 1;

        while (find(nums.begin(), nums.end(), current + 1) != nums.end()) {
            current++;
            length++;
        }

        longest = max(longest, length);
    }

    return longest;
}
```

### Why inefficient?

`find()` takes O(N), and we may call it many times.

So worst case:

```text
Time = O(N^2)
```

---

### 3. Intuition Shift

The “aha” moment:

> Do not start counting from every number.
> Only start from the beginning of a sequence.

How to know a number is the beginning?

```text
num is start if num - 1 does not exist
```

Example:

```text
nums = [100, 4, 200, 1, 3, 2]

set = {100, 4, 200, 1, 3, 2}
```

Check each number:

```text
100 -> 99 not present, start sequence: 100
4   -> 3 present, skip
200 -> 199 not present, start sequence: 200
1   -> 0 not present, start sequence: 1,2,3,4
3   -> 2 present, skip
2   -> 1 present, skip
```

So we only fully explore:

```text
1 -> 2 -> 3 -> 4
100
200
```

Not from `2`, `3`, and `4` again.

---

### 4. Conceptual Algorithm

```text
Put all numbers into a HashSet

longest = 0

For every num in set:
    If num - 1 is not in set:
        This num is the start of a sequence

        current = num
        length = 1

        While current + 1 exists in set:
            current++
            length++

        Update longest

Return longest
```

Core invariant:

```text
Only expand sequences from their first number.
```

That is why the algorithm stays O(N).

---

### 5. Edge Cases & Complexity

Important edge cases:

```text
nums = []                  answer = 0
nums = [1]                 answer = 1
nums = [1,2,0,1]           answer = 3 because 0,1,2
nums = [-1,0,1,2]          answer = 4
nums = [5,5,5]             answer = 1
```

Complexity:

```text
Time: O(N)
Space: O(N)
```

---

## Beginner-Friendly C++ Code

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> st;

        for (int num : nums) {
            st.insert(num);
        }

        int longest = 0;

        for (int num : st) {
            // Start only if num is the first number of sequence
            if (st.find(num - 1) == st.end()) {
                int current = num;
                int length = 1;

                while (st.find(current + 1) != st.end()) {
                    current++;
                    length++;
                }

                longest = max(longest, length);
            }
        }

        return longest;
    }
};
```

### Recall Line

```text
HashSet + start only when num - 1 is missing.
```
