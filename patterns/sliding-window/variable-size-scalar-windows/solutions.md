## LC 485 — Max Consecutive Ones

https://youtu.be/dizdXJ4Gtnk?si=9McZiKA5nK9XcsaP

### Pattern

This is a **Variable-Size Scalar Window** problem, but actually it is the simplest version.

You are asked:

```text
Find the maximum number of consecutive 1s
```

Clues:

```text
consecutive
subarray-like continuous segment
only tracking count/length
```

Here, we do not need hashmap or frequency.
We only track the current streak of `1`s.

---

## Intuition

Whenever we see `1`, the current streak grows.

Whenever we see `0`, the streak breaks.

Example:

```text
nums = [1, 1, 0, 1, 1, 1]
```

Walkthrough:

```text
1 -> streak = 1, max = 1
1 -> streak = 2, max = 2
0 -> streak = 0
1 -> streak = 1
1 -> streak = 2
1 -> streak = 3, max = 3
```

Answer:

```text
3
```

---

## Human C++ Code

```cpp
class Solution {
public:
    int findMaxConsecutiveOnes(vector<int>& nums) {
        int count = 0;
        int maxCount = 0;

        for (int num : nums) {
            if (num == 1) {
                count++;
                maxCount = max(maxCount, count);
            } else {
                count = 0;
            }
        }

        return maxCount;
    }
};
```

---

## Complexity

```text
Time: O(n)
Space: O(1)
```

Each element is checked once.

## LC 487 — Max Consecutive Ones II

https://youtu.be/3E4JBHSLpYk?si=wWw4T9gRCnkXockV

In this problem, you are allowed to flip **at most one `0`** into `1`.

So now the question becomes:

```text
Find the longest subarray that contains at most one 0.
```

That is why this is a **variable-size sliding window** problem.

---

## Intuition

For LC 485, one `0` breaks the streak.

But in LC 487, one `0` is allowed.

Example:

```text
nums = [1, 0, 1, 1, 0]
```

Valid windows:

```text
[1, 0, 1, 1]  -> contains one 0, length 4
[1, 1, 0]     -> contains one 0, length 3
```

Answer:

```text
4
```

The window is valid while:

```text
zeroCount <= 1
```

If zeroCount becomes `2`, shrink from the left until only one zero remains.

---

## Human C++ Code

```cpp
class Solution {
public:
    int findMaxConsecutiveOnes(vector<int>& nums) {
        int left = 0;
        int zeroCount = 0;
        int maxLen = 0;

        for (int right = 0; right < nums.size(); right++) {
            if (nums[right] == 0) {
                zeroCount++;
            }

            while (zeroCount > 1) {
                if (nums[left] == 0) {
                    zeroCount--;
                }
                left++;
            }

            maxLen = max(maxLen, right - left + 1);
        }

        return maxLen;
    }
};
```

---

## Dry Run

```text
nums = [1, 0, 1, 1, 0]
```

```text
right = 0 -> [1]
zeroCount = 0, maxLen = 1

right = 1 -> [1, 0]
zeroCount = 1, maxLen = 2

right = 2 -> [1, 0, 1]
zeroCount = 1, maxLen = 3

right = 3 -> [1, 0, 1, 1]
zeroCount = 1, maxLen = 4

right = 4 -> [1, 0, 1, 1, 0]
zeroCount = 2, invalid

move left until one zero is removed:

remove nums[0] = 1
window still has 2 zeroes

remove nums[1] = 0
zeroCount becomes 1

valid window = [1, 1, 0]
maxLen remains 4
```

Answer:

```text
4
```

---

## Complexity

```text
Time: O(n)
Space: O(1)
```

Each pointer moves forward only once.

## LC 1493 — Longest Subarray of 1s After Deleting One Element

https://youtu.be/SQ8tY9nxeZU?si=_023bqEDtJkkwU1r

This is very close to **LC 487**, but with one important difference.

LC 487:

```text
You may flip at most one 0.
```

LC 1493:

```text
You must delete exactly one element.
```

So the window can contain **at most one 0**, but answer is:

```text
window length - 1
```

because one element must be deleted.

---

## Intuition

We want the longest window that has at most one `0`.

Example:

```text
nums = [1, 1, 0, 1]
```

Window:

```text
[1, 1, 0, 1]
```

contains one zero.

Delete that zero:

```text
[1, 1, 1]
```

Answer:

```text
3
```

So:

```text
window size = 4
answer = 4 - 1 = 3
```

---

## Human C++ Code

```cpp
class Solution {
public:
    int longestSubarray(vector<int>& nums) {
        int left = 0;
        int zeroCount = 0;
        int maxLen = 0;

        for (int right = 0; right < nums.size(); right++) {
            if (nums[right] == 0) {
                zeroCount++;
            }

            while (zeroCount > 1) {
                if (nums[left] == 0) {
                    zeroCount--;
                }
                left++;
            }

            maxLen = max(maxLen, right - left);
        }

        return maxLen;
    }
};
```

Notice this line:

```cpp
maxLen = max(maxLen, right - left);
```

Not:

```cpp
right - left + 1
```

Because we must delete one element.

---

## Dry Run

```text
nums = [1, 1, 0, 1]
```

```text
right = 0
window = [1]
window size = 1
after deleting one = 0
maxLen = 0

right = 1
window = [1, 1]
window size = 2
after deleting one = 1
maxLen = 1

right = 2
window = [1, 1, 0]
window size = 3
delete 0
maxLen = 2

right = 3
window = [1, 1, 0, 1]
window size = 4
delete 0
maxLen = 3
```

Answer:

```text
3
```

---

## Important Edge Case

```text
nums = [1, 1, 1]
```

There is no zero.

But problem says delete **one element**.

So answer is:

```text
2
```

Our formula handles it:

```text
window size = 3
answer = 3 - 1 = 2
```

---

## Complexity

```text
Time: O(n)
Space: O(1)
```

## LC 209 — Minimum Size Subarray Sum

https://youtu.be/D2MbogiFXWU?si=a_HlPm9NVIXVXJJL

Problem idea:

```text
Given target and nums, find the minimum length of a contiguous subarray
whose sum is >= target.
```

---

## Pattern

This is a **Variable-Size Scalar Window** problem.

Clues:

```text
contiguous subarray
sum
minimum length
sum >= target
positive integers
```

Because all numbers are **positive**, when we expand the window, sum increases.

When sum becomes enough, we try shrinking from the left to get the smallest valid window.

---

## Naive Approach

Try every subarray.

```text
nums = [2, 3, 1, 2, 4, 3], target = 7
```

Check:

```text
[2]
[2,3]
[2,3,1]
[2,3,1,2] = 8 valid
[3]
[3,1]
[3,1,2]
[3,1,2,4] = 10 valid
...
```

Problem:

Many sums are recalculated again and again.

Time:

```text
O(n²)
```

---

## Intuition Shift

Instead of restarting every time, keep one window.

Example:

```text
nums = [2, 3, 1, 2, 4, 3]
target = 7
```

Expand until valid:

```text
[2, 3, 1, 2] sum = 8
```

Now window is valid.

But we want **minimum length**, so shrink:

```text
remove 2
[3, 1, 2] sum = 6
```

Now invalid, so expand again:

```text
[3, 1, 2, 4] sum = 10
```

Shrink again:

```text
remove 3 -> [1, 2, 4] sum = 7 valid
remove 1 -> [2, 4] sum = 6 invalid
```

Later:

```text
[4, 3] sum = 7 valid
```

Minimum length:

```text
2
```

---

## Conceptual Algorithm

```text
left = 0
sum = 0
minLen = infinity

for right from 0 to n - 1:
    add nums[right] to sum

    while sum >= target:
        update minLen using current window size
        remove nums[left] from sum
        move left forward

if minLen was never updated:
    return 0
else:
    return minLen
```

Important:

```text
while sum >= target
```

Not `if`.

Because after finding one valid window, there may be an even smaller valid window inside it.

---

## Human C++ Code

```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int left = 0;
        int sum = 0;
        int minLen = INT_MAX;

        for (int right = 0; right < nums.size(); right++) {
            sum += nums[right];

            while (sum >= target) {
                int windowSize = right - left + 1;
                minLen = min(minLen, windowSize);

                sum -= nums[left];
                left++;
            }
        }

        return minLen == INT_MAX ? 0 : minLen;
    }
};
```

---

## Edge Cases

```text
nums = [1, 1, 1], target = 10
```

No valid subarray.

Return:

```text
0
```

```text
nums = [7], target = 7
```

Answer:

```text
1
```

```text
nums = [8], target = 7
```

Answer:

```text
1
```

---

## Complexity

```text
Time: O(n)
Space: O(1)
```

Both `left` and `right` move forward only.
