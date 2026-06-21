## Problem: Maximum Average Subarray I — LC 643

https://youtu.be/56TxHMG0qhQ?si=yHXcdQ-9z1qpALpP

### 1. Core Pattern Recognition

This is a **Fixed-Size Scalar Window** problem.

Clues:

The problem says:

> Find a contiguous subarray of length `k` with maximum average.

Important keywords:

**contiguous subarray**
**length k fixed**
**maximum average / maximum sum**

Since average of length `k` is:

```text
sum / k
```

and `k` is fixed, maximizing average is the same as maximizing the window sum.

So the real problem becomes:

```text
Find maximum sum of any subarray of size k
```

That is classic **fixed-size sliding window**.

---

### 2. Naive Starting Point

Brute force idea:

For every starting index `i`, calculate the sum of the next `k` elements.

Example:

```text
nums = [1, 12, -5, -6, 50, 3], k = 4
```

Windows:

```text
[1, 12, -5, -6]   sum = 2
[12, -5, -6, 50]  sum = 51
[-5, -6, 50, 3]   sum = 42
```

Naive approach:

```text
for every i:
    calculate nums[i] + nums[i+1] + ... + nums[i+k-1]
```

Problem:

Each window repeats most of the previous calculation.

For example:

```text
[1, 12, -5, -6]
[12, -5, -6, 50]
```

The second window reuses:

```text
12, -5, -6
```

But brute force sums them again.

Time complexity:

```text
O((n-k+1) * k) ≈ O(nk)
```

---

### 3. Intuition Shift

The “Aha!” moment:

When the window moves one step right:

```text
old window: [1, 12, -5, -6]
new window: [12, -5, -6, 50]
```

Only two things change:

```text
remove 1
add 50
```

So instead of recalculating the full sum:

```text
new_sum = old_sum - outgoing_element + incoming_element
```

Example:

```text
old_sum = 1 + 12 - 5 - 6 = 2

new_sum = 2 - 1 + 50 = 51
```

This is the main sliding window idea:

```text
Keep the useful work.
Only update the changed part.
```

---

### 4. Conceptual Algorithm — No Code

Steps:

```text
1. Calculate the sum of the first k elements.
2. Store it as current_sum and max_sum.
3. Move the window from left to right.
4. For every new element entering the window:
      subtract the element leaving the window
      add the new element
      update max_sum
5. Return max_sum / k
```

Pointer movement:

```text
Initial window:
[1, 12, -5, -6]  50  3
 L           R

Slide:
 1 [12, -5, -6, 50] 3
    L           R

Slide:
 1 12 [-5, -6, 50, 3]
       L          R
```

Window size always remains exactly `k`.

---

### 5. Edge Cases & Complexity

Important edge cases:

```text
k == 1
```

Every single element is its own window.

```text
k == len(nums)
```

Only one possible window.

```text
negative numbers
```

Do not initialize max_sum as 0, because all values may be negative.

Example:

```text
nums = [-5, -2, -3], k = 2
```

Best sum is `-5`, not `0`.

Complexity:

```text
Time: O(n)
Space: O(1)
```

Because each element enters and leaves the window at most once.

```cpp
class Solution {
public:
    double findMaxAverage(vector<int>& nums, int k) {
        int n = nums.size();

        double windowSum = 0;

        // first window sum
        for (int i = 0; i < k; i++) {
            windowSum += nums[i];
        }

        double maxSum = windowSum;

        // slide the window
        for (int right = k; right < n; right++) {
            windowSum += nums[right];        // add new element
            windowSum -= nums[right - k];    // remove old element

            maxSum = max(maxSum, windowSum);
        }

        return maxSum / k;
    }
};
```

## LC 1423 — Maximum Points You Can Obtain from Cards

https://youtu.be/pBWCOCS636U?si=JC0F0DQQlmsMieDY

You can pick exactly `k` cards from either:

```text
left end
or
right end
```

At first it looks like recursion/backtracking, but there is a clean sliding window trick.

---

## Core Pattern

This is a **fixed-size sliding window variant**.

Important clue:

```text
Pick k cards from either end
```

That means after picking `k` cards, the cards you did **not pick** must be one continuous middle subarray.

So instead of maximizing picked cards:

```text
maximize picked sum
```

we can think:

```text
minimize the sum of the remaining middle window
```

Total cards = `n`
Picked cards = `k`
Remaining cards = `n - k`

So answer:

```text
totalSum - minimum sum of subarray of size n - k
```

---

## Naive Approach

Try all combinations of taking from left and right.

Example:

```text
cardPoints = [1, 2, 3, 4, 5, 6, 1]
k = 3
```

Possible choices:

```text
3 left, 0 right -> 1 + 2 + 3 = 6
2 left, 1 right -> 1 + 2 + 1 = 4
1 left, 2 right -> 1 + 1 + 6 = 8
0 left, 3 right -> 1 + 6 + 5 = 12
```

Answer:

```text
12
```

This is already `O(k)`, but the deeper sliding-window view is:

```text
Which middle subarray of size n-k should I leave behind?
```

---

## Intuition Shift

Original array:

```text
[1, 2, 3, 4, 5, 6, 1]
```

If `k = 3`, then we pick 3 cards.

So remaining cards length:

```text
n - k = 7 - 3 = 4
```

If we leave behind this middle window:

```text
[1, 2, 3, 4]
```

Picked cards:

```text
[5, 6, 1]
```

If we leave behind:

```text
[2, 3, 4, 5]
```

Picked cards:

```text
[1] + [6, 1]
```

If we leave behind:

```text
[3, 4, 5, 6]
```

Picked cards:

```text
[1, 2] + [1]
```

If we leave behind:

```text
[4, 5, 6, 1]
```

Picked cards:

```text
[1, 2, 3]
```

To maximize picked score, leave the minimum-sum window of length `n-k`.

---

## Conceptual Algorithm

```text
1. Find total sum of all cards.
2. Window size = n - k.
3. Find minimum sum of any contiguous subarray of this size.
4. Answer = total sum - minimum window sum.
```

Sliding part:

```text
currentSum = sum of first window of size n-k
minWindowSum = currentSum

for each next index:
    add incoming element
    remove outgoing element
    update minWindowSum
```

---

## Human C++ Code

```cpp
class Solution {
public:
    int maxScore(vector<int>& cardPoints, int k) {
        int n = cardPoints.size();

        int totalSum = 0;
        for (int x : cardPoints) {
            totalSum += x;
        }

        int windowSize = n - k;

        if (windowSize == 0) {
            return totalSum;
        }

        int windowSum = 0;

        for (int i = 0; i < windowSize; i++) {
            windowSum += cardPoints[i];
        }

        int minWindowSum = windowSum;

        for (int right = windowSize; right < n; right++) {
            windowSum += cardPoints[right];
            windowSum -= cardPoints[right - windowSize];

            minWindowSum = min(minWindowSum, windowSum);
        }

        return totalSum - minWindowSum;
    }
};
```

---

## Alternative Human Intuition

You can also start by taking all `k` cards from the left.

Then one by one, replace a left card with a right card.

```text
left sum of first k cards
then move:
remove from left side
add from right side
```

That also works.

But the **minimum middle window** approach is more pattern-friendly.

---

## Complexity

```text
Time: O(n)
Space: O(1)
```
