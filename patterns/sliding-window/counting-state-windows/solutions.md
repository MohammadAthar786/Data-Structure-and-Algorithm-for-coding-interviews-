## LC 930 — Binary Subarrays With Sum

https://youtu.be/5Quv9nnZs34?si=sCLyf5lUARJC3KC8

Now we enter **Counting-State Windows**.

Problem:

```text
Given binary array nums and goal,
count number of non-empty subarrays whose sum equals goal.
```

---

## Core Pattern Recognition

Clues:

```text
binary array
subarrays
count number
sum equals goal
```

Important shift:

For counting exact sum subarrays, direct sliding window can be tricky because:

```text
sum == goal
```

needs counting **all possible valid subarrays**, not just longest/minimum.

The common trick is:

```text
count subarrays with sum exactly goal
=
count subarrays with sum at most goal
-
count subarrays with sum at most goal - 1
```

So:

```text
exact(goal) = atMost(goal) - atMost(goal - 1)
```

This works nicely because nums contains only:

```text
0 and 1
```

So window sum never decreases when expanding right.

---

## Naive Approach

Try every subarray and calculate sum.

Example:

```text
nums = [1, 0, 1, 0, 1]
goal = 2
```

Subarrays with sum 2:

```text
[1, 0, 1]
[1, 0, 1, 0]
[0, 1, 0, 1]
[1, 0, 1]
```

Answer:

```text
4
```

Naive idea:

```text
for every i:
    sum = 0
    for every j from i to n-1:
        sum += nums[j]
        if sum == goal:
            count++
```

Time:

```text
O(n²)
```

---

## Intuition Shift

Instead of counting exact sum directly, count:

```text
How many subarrays ending at right have sum <= goal?
```

For each `right`, after shrinking invalid window:

```text
window = [left ... right]
```

If this whole window has sum <= goal, then every subarray ending at `right` and starting from:

```text
left, left+1, left+2, ..., right
```

is also valid.

Number of such subarrays:

```text
right - left + 1
```

That is the counting trick.

---

## Helper: atMost(k)

`atMost(k)` counts subarrays whose sum is less than or equal to `k`.

Concept:

```text
left = 0
sum = 0
count = 0

for right from 0 to n - 1:
    sum += nums[right]

    while sum > k:
        sum -= nums[left]
        left++

    count += right - left + 1
```

Then:

```text
answer = atMost(goal) - atMost(goal - 1)
```

---

## Human C++ Code

```cpp
class Solution {
public:
    int atMost(vector<int>& nums, int goal) {
        if (goal < 0) {
            return 0;
        }

        int left = 0;
        int sum = 0;
        int count = 0;

        for (int right = 0; right < nums.size(); right++) {
            sum += nums[right];

            while (sum > goal) {
                sum -= nums[left];
                left++;
            }

            count += right - left + 1;
        }

        return count;
    }

    int numSubarraysWithSum(vector<int>& nums, int goal) {
        return atMost(nums, goal) - atMost(nums, goal - 1);
    }
};
```

---

## Dry Run Idea

```text
nums = [1, 0, 1, 0, 1]
goal = 2
```

We compute:

```text
atMost(2) = count of subarrays with sum 0, 1, or 2
atMost(1) = count of subarrays with sum 0 or 1
```

Subtract:

```text
only subarrays with sum exactly 2 remain
```

Answer:

```text
4
```

---

## Edge Cases

```text
goal = 0
```

Then:

```text
answer = atMost(0) - atMost(-1)
```

That is why helper has:

```cpp
if (goal < 0) return 0;
```

Example:

```text
nums = [0, 0, 0], goal = 0
```

All subarrays are valid.

Number of subarrays:

```text
3 * 4 / 2 = 6
```

---

## Complexity

```text
Time: O(n)
Space: O(1)
```

Technically helper runs twice:

```text
O(2n) = O(n)
```

## LC 1248 — Count Number of Nice Subarrays

https://youtu.be/JpXlsCAD1kg?si=lKj4zmTDDARavudw

A **nice subarray** means:

```text
subarray with exactly k odd numbers
```

This is very similar to **LC 930**.

LC 930:

```text
count subarrays with sum exactly goal
```

LC 1248:

```text
count subarrays with exactly k odd numbers
```

Convert each number mentally:

```text
odd number  -> 1
even number -> 0
```

Then the problem becomes:

```text
count subarrays whose binary sum is exactly k
```

---

## Core Pattern Recognition

Clues:

```text
count number of subarrays
exactly k
odd numbers
```

Whenever you see:

```text
count subarrays with exactly k something
```

think:

```text
exactly(k) = atMost(k) - atMost(k - 1)
```

Here, “something” is:

```text
number of odd values
```

---

## Naive Approach

Try every subarray and count odds.

Example:

```text
nums = [1, 1, 2, 1, 1]
k = 3
```

Valid nice subarrays:

```text
[1, 1, 2, 1]
[1, 2, 1, 1]
```

Answer:

```text
2
```

Naive way:

```text
for every start:
    oddCount = 0
    for every end:
        if nums[end] is odd:
            oddCount++
        if oddCount == k:
            count++
```

Time:

```text
O(n²)
```

---

## Intuition Shift

Instead of counting exactly `k` directly:

```text
exactly(k) = atMost(k) - atMost(k - 1)
```

Why?

```text
atMost(k)     = subarrays with 0, 1, 2, ..., k odds
atMost(k - 1) = subarrays with 0, 1, 2, ..., k-1 odds
```

Subtract them:

```text
only subarrays with exactly k odds remain
```

---

## Helper: atMost(k)

`atMost(k)` counts subarrays with at most `k` odd numbers.

For every `right`, after shrinking invalid windows:

```text
[left ... right]
```

has at most `k` odd numbers.

Then all subarrays ending at `right` and starting from:

```text
left, left+1, ..., right
```

are valid.

Number added:

```text
right - left + 1
```

---

## Human C++ Code

```cpp
class Solution {
public:
    int atMost(vector<int>& nums, int k) {
        if (k < 0) {
            return 0;
        }

        int left = 0;
        int oddCount = 0;
        int count = 0;

        for (int right = 0; right < nums.size(); right++) {
            if (nums[right] % 2 == 1) {
                oddCount++;
            }

            while (oddCount > k) {
                if (nums[left] % 2 == 1) {
                    oddCount--;
                }
                left++;
            }

            count += right - left + 1;
        }

        return count;
    }

    int numberOfSubarrays(vector<int>& nums, int k) {
        return atMost(nums, k) - atMost(nums, k - 1);
    }
};
```

---

## Dry Run Idea

```text
nums = [1, 1, 2, 1, 1]
k = 3
```

Compute:

```text
atMost(3) = subarrays with 0, 1, 2, or 3 odds
atMost(2) = subarrays with 0, 1, or 2 odds
```

Subtract:

```text
only subarrays with exactly 3 odds remain
```

Answer:

```text
2
```

---

## Edge Cases

```text
nums = [2, 4, 6], k = 1
```

No odd numbers.

Answer:

```text
0
```

```text
nums = [2, 4, 6], k = 0
```

All subarrays have zero odd numbers.

Answer:

```text
6
```

```text
nums = [1], k = 1
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

Helper runs twice, but still:

```text
O(2n) = O(n)
```

## LC 992 — Subarrays with K Different Integers

https://youtu.be/uJJSGxfzix8?si=fOlFDU8ByZGpgdY8

This is the same counting trick as LC 930 and LC 1248, but now the state is a **frequency map**.

Problem:

```text
Count subarrays with exactly k distinct integers.
```

---

## Core Pattern Recognition

Clues:

```text
count subarrays
exactly k
different integers / distinct integers
```

Whenever you see:

```text
count subarrays with exactly k distinct
```

think:

```text
exactly(k) = atMost(k) - atMost(k - 1)
```

So:

```text
answer = subarraysWithAtMostKDistinct(k)
       - subarraysWithAtMostKDistinct(k - 1)
```

---

## Naive Approach

Try every subarray and count distinct values.

Example:

```text
nums = [1, 2, 1, 2, 3]
k = 2
```

Valid subarrays:

```text
[1, 2]
[1, 2, 1]
[1, 2, 1, 2]
[2, 1]
[2, 1, 2]
[1, 2]
[2, 3]
```

Answer:

```text
7
```

Naive method:

```text
for every start:
    freq = empty map
    for every end:
        add nums[end]
        if freq.size() == k:
            count++
```

Time:

```text
O(n²)
```

---

## Intuition Shift

Directly counting exactly `k` is hard.

But counting **at most k distinct** is easy with sliding window.

For each `right`, after shrinking:

```text
window = [left ... right]
```

has at most `k` distinct integers.

Then every subarray ending at `right` and starting from:

```text
left, left+1, ..., right
```

also has at most `k` distinct integers.

Number added:

```text
right - left + 1
```

Then subtract:

```text
atMost(k) - atMost(k - 1)
```

This removes all subarrays with fewer than `k` distinct values, leaving exactly `k`.

---

## Helper: atMost(k)

```text
left = 0
freq = empty map
count = 0

for right from 0 to n - 1:
    add nums[right] to freq

    while freq.size() > k:
        decrease freq[nums[left]]
        if freq[nums[left]] == 0:
            erase nums[left]
        left++

    count += right - left + 1
```

---

## Human C++ Code

```cpp
class Solution {
public:
    int atMost(vector<int>& nums, int k) {
        if (k < 0) {
            return 0;
        }

        unordered_map<int, int> freq;

        int left = 0;
        int count = 0;

        for (int right = 0; right < nums.size(); right++) {
            freq[nums[right]]++;

            while (freq.size() > k) {
                freq[nums[left]]--;

                if (freq[nums[left]] == 0) {
                    freq.erase(nums[left]);
                }

                left++;
            }

            count += right - left + 1;
        }

        return count;
    }

    int subarraysWithKDistinct(vector<int>& nums, int k) {
        return atMost(nums, k) - atMost(nums, k - 1);
    }
};
```

---

## Dry Run Idea

```text
nums = [1, 2, 1, 2, 3]
k = 2
```

Compute:

```text
atMost(2) = subarrays with 1 or 2 distinct values
atMost(1) = subarrays with 1 distinct value
```

Subtract:

```text
only subarrays with exactly 2 distinct values remain
```

Answer:

```text
7
```

---

## Edge Cases

```text
nums = [1, 1, 1], k = 1
```

All subarrays are valid.

Answer:

```text
6
```

```text
nums = [1, 2, 3], k = 2
```

Valid:

```text
[1,2], [2,3]
```

Answer:

```text
2
```

```text
k = 0
```

No non-empty subarray has 0 distinct integers.

Answer:

```text
0
```

---

## Complexity

```text
Time: O(n)
Space: O(k)
```

Helper runs twice, but still:

```text
O(2n) = O(n)
```
