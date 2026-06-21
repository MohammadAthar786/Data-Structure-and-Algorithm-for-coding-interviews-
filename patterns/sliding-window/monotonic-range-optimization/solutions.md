## LC 239 — Sliding Window Maximum

https://youtu.be/29OnjVQ-fk4?si=oxseeUVtGqemT7oM

This is your first **Monotonic Queue / Deque** sliding window problem.

Problem:

```text
Given nums and window size k,
return maximum element of every window of size k.
```

---

## Core Pattern Recognition

Clues:

```text
sliding window
maximum in every window
fixed size k
```

Normal fixed window can track sum easily.

But here we need:

```text
maximum of current window
```

When the maximum element leaves, we need to quickly know the next maximum.

That is why we use a **monotonic decreasing deque**.

---

## Naive Approach

For every window, scan all `k` elements and find max.

Example:

```text
nums = [1,3,-1,-3,5,3,6,7], k = 3
```

Windows:

```text
[1,3,-1]     max = 3
[3,-1,-3]    max = 3
[-1,-3,5]    max = 5
[-3,5,3]     max = 5
[5,3,6]      max = 6
[3,6,7]      max = 7
```

Naive time:

```text
O(n * k)
```

Because each window scans again.

---

## Intuition Shift

We need a structure that keeps the **best candidates for maximum**.

Suppose current window has:

```text
[1, 3, -1]
```

If `3` is present, then `1` can never be maximum for this window or future windows where `3` also exists.

So smaller elements before a bigger incoming element become useless.

Example:

```text
nums = [1, 3]
```

When `3` comes:

```text
1 is useless
```

because `3` is newer and bigger.

So we remove smaller elements from the back.

This creates a deque in decreasing order:

```text
front = maximum
back = smaller candidates
```

---

## What Deque Stores

Store **indices**, not values.

Why?

Because we need to know if an element has gone outside the window.

Window ending at `right` has left boundary:

```text
right - k + 1
```

Any index smaller than this is out of window.

---

## Conceptual Algorithm

```text
deque stores indices
values in deque are decreasing

for right from 0 to n - 1:

    1. Remove indices from front if they are outside current window.

    2. Remove indices from back while nums[back] <= nums[right].
       Because current nums[right] is better and newer.

    3. Push right index into deque.

    4. If we have formed at least one full window:
          answer.push_back(nums[deque.front()])
```

---

## Human C++ Code

```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        deque<int> dq;
        vector<int> ans;

        for (int right = 0; right < nums.size(); right++) {
            // remove elements that are out of this window
            if (!dq.empty() && dq.front() <= right - k) {
                dq.pop_front();
            }

            // remove smaller elements from the back
            while (!dq.empty() && nums[dq.back()] <= nums[right]) {
                dq.pop_back();
            }

            dq.push_back(right);

            // first full window starts when right >= k - 1
            if (right >= k - 1) {
                ans.push_back(nums[dq.front()]);
            }
        }

        return ans;
    }
};
```

---

## Dry Run

```text
nums = [1, 3, -1, -3, 5]
k = 3
```

```text
right = 0, value = 1
dq = [1]

right = 1, value = 3
remove 1 because 3 is bigger
dq = [3]

right = 2, value = -1
dq = [3, -1]
first window [1,3,-1]
max = 3

right = 3, value = -3
dq = [3, -1, -3]
window [3,-1,-3]
max = 3

right = 4, value = 5
remove -3
remove -1
remove 3
dq = [5]
window [-1,-3,5]
max = 5
```

---

## Edge Cases

```text
k = 1
```

Every element is its own max.

Answer:

```text
nums
```

```text
k = nums.size()
```

Only one window.

Answer:

```text
[max of full array]
```

```text
nums has duplicates
```

Using:

```cpp
nums[dq.back()] <= nums[right]
```

removes older equal values and keeps newer one.

That is fine because newer equal value survives longer.

---

## Complexity

```text
Time: O(n)
Space: O(k)
```

Each index enters deque once and leaves once.

## LC 1438 — Longest Continuous Subarray With Absolute Diff ≤ Limit

https://youtu.be/xZDbcGB2PQs?si=NVD4gpYI3kWZ460O

This is like LC 239, but now we need both:

```text
maximum of current window
minimum of current window
```

Valid condition:

```text
max(window) - min(window) <= limit
```

---

## Pattern Recognition

Clues:

```text
longest continuous subarray
absolute difference between any two elements <= limit
```

Important translation:

The maximum absolute difference inside a window is always:

```text
max element - min element
```

So the condition becomes:

```text
currentMax - currentMin <= limit
```

This is a **variable-size sliding window + monotonic deque** problem.

---

## Naive Approach

Try every subarray and calculate max/min.

Example:

```text
nums = [8, 2, 4, 7]
limit = 4
```

Subarrays:

```text
[8] valid
[8,2] invalid because 8 - 2 = 6
[2,4] valid
[2,4,7] invalid because 7 - 2 = 5
[4,7] valid
```

Answer:

```text
2
```

Naive problem:

For every subarray, repeatedly finding max/min costs too much.

Time:

```text
O(n²) or O(n² log n)
```

---

## Intuition Shift

We need to know current window’s:

```text
maximum quickly
minimum quickly
```

So we maintain two deques:

```text
maxDeque -> decreasing order
minDeque -> increasing order
```

For current window:

```text
max = nums[maxDeque.front()]
min = nums[minDeque.front()]
```

If:

```text
max - min > limit
```

window is invalid, so move `left`.

---

## Conceptual Algorithm

```text
left = 0
answer = 0

maxDeque stores indices in decreasing nums value
minDeque stores indices in increasing nums value

for right from 0 to n - 1:

    add nums[right] into maxDeque:
        remove from back while nums[back] < nums[right]
        push right

    add nums[right] into minDeque:
        remove from back while nums[back] > nums[right]
        push right

    while nums[maxDeque.front()] - nums[minDeque.front()] > limit:
        if maxDeque.front() == left:
            pop from maxDeque front

        if minDeque.front() == left:
            pop from minDeque front

        left++

    answer = max(answer, right - left + 1)
```

---

## Human C++ Code

```cpp
class Solution {
public:
    int longestSubarray(vector<int>& nums, int limit) {
        deque<int> maxDeque;
        deque<int> minDeque;

        int left = 0;
        int maxLen = 0;

        for (int right = 0; right < nums.size(); right++) {
            while (!maxDeque.empty() && nums[maxDeque.back()] < nums[right]) {
                maxDeque.pop_back();
            }
            maxDeque.push_back(right);

            while (!minDeque.empty() && nums[minDeque.back()] > nums[right]) {
                minDeque.pop_back();
            }
            minDeque.push_back(right);

            while (nums[maxDeque.front()] - nums[minDeque.front()] > limit) {
                if (maxDeque.front() == left) {
                    maxDeque.pop_front();
                }

                if (minDeque.front() == left) {
                    minDeque.pop_front();
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
nums = [8, 2, 4, 7]
limit = 4
```

```text
right = 0 -> 8
max = 8, min = 8
window [8], length = 1

right = 1 -> 2
max = 8, min = 2
diff = 6 invalid

move left:
remove 8
window [2], valid

right = 2 -> 4
max = 4, min = 2
diff = 2 valid
window [2,4], length = 2

right = 3 -> 7
max = 7, min = 2
diff = 5 invalid

move left:
remove 2
window [4,7], diff = 3 valid
length = 2
```

Answer:

```text
2
```

---

## Edge Cases

```text
limit = 0
```

Only equal elements can stay together.

Example:

```text
[4, 4, 4]
```

Answer:

```text
3
```

```text
nums has one element
```

Answer:

```text
1
```

Duplicates are okay because we store indices.

---

## Complexity

```text
Time: O(n)
Space: O(n)
```

Each index enters and leaves each deque at most once.
