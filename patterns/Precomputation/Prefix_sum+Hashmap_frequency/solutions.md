## **LeetCode 560: Subarray Sum Equals K**

https://youtu.be/-SWrz90jCUM?si=AdkSQsMAzq_lUEJ5

---

## 1. Core Pattern Recognition

### The Clues in the Problem Description

- **"Contiguous Subarrays":** This means you are dealing with unbroken slices of the array. Whenever you need to calculate sums of contiguous slices, your mind should instantly jump to **Prefix Sums**.
- **"Sum equals K":** You are looking for a exact target value.
- **Presence of Negative Numbers:** The description mentions that `nums[i]` can be negative. This is a massive clue. Because numbers can be negative, a sliding window approach (like Two Pointers) completely breaks down—expanding or shrinking the window doesn't monotonically increase or decrease the sum. When sliding window fails on a contiguous subarray sum problem, **Prefix Sum + Hash Map** is the definitive pattern.

---

## 2. The "Naive" Starting Point

The most straightforward way to solve this is to look at every single possible subarray and calculate its sum.

- **The Approach:** You pick a starting index `i` and an ending index `j`. You then loop from `i` to `j` to sum up the elements, checking if it equals `k`. Even if you optimize it slightly by accumulating the sum as `j` moves forward (reducing it from $O(N^3)$ to $O(N^2)$), you still require two nested loops.
- **Why it's inefficient:** For an array of size $N$, there are $O(N^2)$ possible subarrays. Checking all pairs takes $O(N^2)$ time.
- **The Redundant Work:** Imagine you have already calculated the sum of the subarray from index 2 to index 5. When you move to calculate the sum from index 2 to index 6, you are re-adding all those middle elements. More importantly, as you move your outer loop to a new starting position, you completely forget all the cumulative sums you calculated in the previous iterations and compute them all over again.

---

## 3. The Intuition Shift

Here is the fundamental mathematical trick to shift your perspective.

Recall from basic prefix sums that the sum of any subarray from index `i` to `j` can be written as:

$$\text{Subarray Sum} = \text{Prefix Sum up to } j - \text{Prefix Sum up to } (i-1)$$

We want this $\text{Subarray Sum}$ to equal our target $k$:

$$k = \text{Prefix Sum up to } j - \text{Prefix Sum up to } (i-1)$$

Let's rearrange this equation algebraically from the perspective of our current position $j$:

$$\text{Prefix Sum up to } (i-1) = \text{Prefix Sum up to } j - k$$

### The "Aha!" Moment

When you are standing at index `j`, you already know your current `Prefix Sum up to j`. You don't need to look backward to find a matching index `i-1`. Instead, you just calculate a target value: `Current Prefix Sum - k`.

If that exact value has appeared as a prefix sum earlier in your journey, it means the elements _between_ that old position and your current position must sum up exactly to $k$.

### Concrete Example: `nums = [1, 2, 3]`, `k = 3`

Let's track the running prefix sum:

- At index 0: `num = 1`, `Current Prefix Sum = 1`.
- We look for `1 - 3 = -2`. Have we seen `-2`? No.

- At index 1: `num = 2`, `Current Prefix Sum = 1 + 2 = 3`.
- We look for `3 - 3 = 0`. Have we seen `0`? Yes! (Before we started, the sum was 0). This tells us the subarray `[1, 2]` sums to 3.

- At index 2: `num = 3`, `Current Prefix Sum = 3 + 3 = 6`.
- We look for `6 - 3 = 3`. Have we seen a prefix sum of `3` before? Yes, we saw it at index 1! This means the elements after index 1 up to index 2 (which is just `[3]`) form a valid subarray.

By storing **how many times** each prefix sum has appeared in a Hash Map, we can instantly count our valid subarrays in $O(1)$ time per element.

---

## 4. Conceptual Algorithm (No Code)

We will use a single pass through the array, maintaining three things:

1. `current_prefix_sum`: An integer tracking the cumulative sum from the beginning.
2. `prefix_frequencies`: A Hash Map (`std::unordered_map<int, int>`) where the key is a past prefix sum, and the value is how many times it has been seen.
3. `count`: An integer tracking the total number of valid subarrays found.

### Step-by-Step Walkthrough:

- **Initialize** your Hash Map with the base condition `{0 : 1}`. This represents a prefix sum of 0 occurring exactly once before we process any elements (essential for subarrays that equal $k$ right from the start of the array).
- **Loop** through each number in the array:

1. Add the current number to `current_prefix_sum`.
2. Compute the old prefix sum we are searching for: `target = current_prefix_sum - k`.
3. Check if `target` exists in our `prefix_frequencies` map. If it does, add its frequency count directly to our global `count`.
4. Add/increment the `current_prefix_sum` into the `prefix_frequencies` map so that future elements can look back at it.

- Return `count`.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases:

- **Subarray starts at index 0:** If the first few elements sum up exactly to $k$, then `current_prefix_sum - k = 0`. Seeding our map with `{0 : 1}` ensures this is cleanly counted.
- **Negative Numbers / Negative K:** The frequency map approach handles fluctuations elegantly. A prefix sum might go up, drop down, and repeat itself. The map counts _every_ historical instance, ensuring all overlapping valid subarrays are captured.

### Target Complexities:

- **Time Complexity:** $O(N)$ — We iterate through the array exactly once. Each hash map lookup and insertion takes $O(1)$ on average.
- **Space Complexity:** $O(N)$ — In the worst-case scenario (all cumulative sums are unique), the hash map will grow to hold $N$ entries.

---

## The C++ Implementation

Here is the clean, efficient, and easy-to-follow C++ code that implements this conceptual framework:

```cpp
#include <vector>
#include <unordered_map>

class Solution {
public:
    int subarraySum(std::vector<int>& nums, int k) {
        // Hash map to store: [Prefix Sum -> Frequency of occurrence]
        std::unordered_map<int, int> prefix_frequencies;

        // Base case: A prefix sum of 0 has occurred 1 time (before starting)
        prefix_frequencies[0] = 1;

        int current_prefix_sum = 0;
        int count = 0;

        for (int num : nums) {
            // 1. Update the running prefix sum
            current_prefix_sum += num;

            // 2. Calculate what past prefix sum we need to find
            int target = current_prefix_sum - k;

            // 3. If that target exists in our history, add its frequency to our count
            if (prefix_frequencies.find(target) != prefix_frequencies.end()) {
                count += prefix_frequencies[target];
            }

            // 4. Record the current prefix sum into the map for future lookups
            prefix_frequencies[current_prefix_sum]++;
        }

        return count;
    }
};

```

---

## **LeetCode 560: Subarray Sum Equals K** step by step to build that

## 1. Core Pattern Recognition

### The Clues in the Problem Description

- **"Contiguous Subarrays":** This means you are dealing with unbroken slices of the array. Whenever you need to calculate sums of contiguous slices, your mind should instantly jump to **Prefix Sums**.
- **"Sum equals K":** You are looking for a exact target value.
- **Presence of Negative Numbers:** The description mentions that `nums[i]` can be negative. This is a massive clue. Because numbers can be negative, a sliding window approach (like Two Pointers) completely breaks down—expanding or shrinking the window doesn't monotonically increase or decrease the sum. When sliding window fails on a contiguous subarray sum problem, **Prefix Sum + Hash Map** is the definitive pattern.

---

## 2. The "Naive" Starting Point

The most straightforward way to solve this is to look at every single possible subarray and calculate its sum.

- **The Approach:** You pick a starting index `i` and an ending index `j`. You then loop from `i` to `j` to sum up the elements, checking if it equals `k`. Even if you optimize it slightly by accumulating the sum as `j` moves forward (reducing it from $O(N^3)$ to $O(N^2)$), you still require two nested loops.
- **Why it's inefficient:** For an array of size $N$, there are $O(N^2)$ possible subarrays. Checking all pairs takes $O(N^2)$ time.
- **The Redundant Work:** Imagine you have already calculated the sum of the subarray from index 2 to index 5. When you move to calculate the sum from index 2 to index 6, you are re-adding all those middle elements. More importantly, as you move your outer loop to a new starting position, you completely forget all the cumulative sums you calculated in the previous iterations and compute them all over again.

---

## 3. The Intuition Shift

Here is the fundamental mathematical trick to shift your perspective.

Recall from basic prefix sums that the sum of any subarray from index `i` to `j` can be written as:

$$\text{Subarray Sum} = \text{Prefix Sum up to } j - \text{Prefix Sum up to } (i-1)$$

We want this $\text{Subarray Sum}$ to equal our target $k$:

$$k = \text{Prefix Sum up to } j - \text{Prefix Sum up to } (i-1)$$

Let's rearrange this equation algebraically from the perspective of our current position $j$:

$$\text{Prefix Sum up to } (i-1) = \text{Prefix Sum up to } j - k$$

### The "Aha!" Moment

When you are standing at index `j`, you already know your current `Prefix Sum up to j`. You don't need to look backward to find a matching index `i-1`. Instead, you just calculate a target value: `Current Prefix Sum - k`.

If that exact value has appeared as a prefix sum earlier in your journey, it means the elements _between_ that old position and your current position must sum up exactly to $k$.

### Concrete Example: `nums = [1, 2, 3]`, `k = 3`

Let's track the running prefix sum:

- At index 0: `num = 1`, `Current Prefix Sum = 1`.
- We look for `1 - 3 = -2`. Have we seen `-2`? No.

- At index 1: `num = 2`, `Current Prefix Sum = 1 + 2 = 3`.
- We look for `3 - 3 = 0`. Have we seen `0`? Yes! (Before we started, the sum was 0). This tells us the subarray `[1, 2]` sums to 3.

- At index 2: `num = 3`, `Current Prefix Sum = 3 + 3 = 6`.
- We look for `6 - 3 = 3`. Have we seen a prefix sum of `3` before? Yes, we saw it at index 1! This means the elements after index 1 up to index 2 (which is just `[3]`) form a valid subarray.

By storing **how many times** each prefix sum has appeared in a Hash Map, we can instantly count our valid subarrays in $O(1)$ time per element.

---

## 4. Conceptual Algorithm (No Code)

We will use a single pass through the array, maintaining three things:

1. `current_prefix_sum`: An integer tracking the cumulative sum from the beginning.
2. `prefix_frequencies`: A Hash Map (`std::unordered_map<int, int>`) where the key is a past prefix sum, and the value is how many times it has been seen.
3. `count`: An integer tracking the total number of valid subarrays found.

### Step-by-Step Walkthrough:

- **Initialize** your Hash Map with the base condition `{0 : 1}`. This represents a prefix sum of 0 occurring exactly once before we process any elements (essential for subarrays that equal $k$ right from the start of the array).
- **Loop** through each number in the array:

1. Add the current number to `current_prefix_sum`.
2. Compute the old prefix sum we are searching for: `target = current_prefix_sum - k`.
3. Check if `target` exists in our `prefix_frequencies` map. If it does, add its frequency count directly to our global `count`.
4. Add/increment the `current_prefix_sum` into the `prefix_frequencies` map so that future elements can look back at it.

- Return `count`.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases:

- **Subarray starts at index 0:** If the first few elements sum up exactly to $k$, then `current_prefix_sum - k = 0`. Seeding our map with `{0 : 1}` ensures this is cleanly counted.
- **Negative Numbers / Negative K:** The frequency map approach handles fluctuations elegantly. A prefix sum might go up, drop down, and repeat itself. The map counts _every_ historical instance, ensuring all overlapping valid subarrays are captured.

### Target Complexities:

- **Time Complexity:** $O(N)$ — We iterate through the array exactly once. Each hash map lookup and insertion takes $O(1)$ on average.
- **Space Complexity:** $O(N)$ — In the worst-case scenario (all cumulative sums are unique), the hash map will grow to hold $N$ entries.

---

## The C++ Implementation

Here is the clean, efficient, and easy-to-follow C++ code that implements this conceptual framework:

```cpp
#include <vector>
#include <unordered_map>

class Solution {
public:
    int subarraySum(std::vector<int>& nums, int k) {
        // Hash map to store: [Prefix Sum -> Frequency of occurrence]
        std::unordered_map<int, int> prefix_frequencies;

        // Base case: A prefix sum of 0 has occurred 1 time (before starting)
        prefix_frequencies[0] = 1;

        int current_prefix_sum = 0;
        int count = 0;

        for (int num : nums) {
            // 1. Update the running prefix sum
            current_prefix_sum += num;

            // 2. Calculate what past prefix sum we need to find
            int target = current_prefix_sum - k;

            // 3. If that target exists in our history, add its frequency to our count
            if (prefix_frequencies.find(target) != prefix_frequencies.end()) {
                count += prefix_frequencies[target];
            }

            // 4. Record the current prefix sum into the map for future lookups
            prefix_frequencies[current_prefix_sum]++;
        }

        return count;
    }
};

```

## **LeetCode 523: Continuous Subarray Sum**.

https://youtu.be/wmn3c1mP0pY?si=PHG9y9EU9WDTetLF

---

## 1. Core Pattern Recognition

### The Clues in the Problem Description

- **"Good subarray":** It defines a valid subarray as one whose elements sum up to a **multiple of $k$** (i.e., $\text{Sum} = n \times k$, where $n$ can be 0, 1, 2, etc.).
- **"Length is at least two":** This is a strict constraint that you must explicitly track.
- **"Continuous Subarray":** The definitive keyword signaling that you will need to evaluate contiguous slices using **Prefix Sums**.

---

## 2. The "Naive" Starting Point

- **The Approach:** You try every possible contiguous subarray using nested loops (outer loop for start index `i`, inner loop for end index `j`). You compute the sum of elements from `i` to `j`. If `(j - i + 1) >= 2` and `sum % k == 0`, you return `true`.
- **Why it's inefficient:** Testing all pairs takes $O(N^2)$ time. With an array size up to $10^5$, an $O(N^2)$ solution will hit a _Time Limit Exceeded_ (TLE) failure.
- **The Redundant Work:** Just like the previous problems, the brute-force approach throws away prefix totals at every step, resetting the calculation across shifting left-boundaries instead of capitalizing on a global tracking system.

---

## 3. The Intuition Shift

Let's look at the math. The sum of a subarray from index `i` to `j` is:

$$\text{Subarray Sum} = \text{Prefix Sum up to } j - \text{Prefix Sum up to } (i-1)$$

The problem states that this $\text{Subarray Sum}$ must be a multiple of $k$. In modulo arithmetic terms, a number is a multiple of $k$ if its remainder when divided by $k$ is $0$:

$$(\text{Prefix Sum up to } j - \text{Prefix Sum up to } (i-1)) \pmod k = 0$$

According to the distributive properties of modulo arithmetic, this equation can be rewritten as:

$$\text{Prefix Sum up to } j \pmod k = \text{Prefix Sum up to } (i-1) \pmod k$$

### The "Aha!" Moment

> If two different prefix sums yield the **exact same remainder** when divided by $k$, then the subarray trapped between them must sum up to a perfect multiple of $k$.

Instead of tracking raw prefix sum values or their frequencies in our Hash Map, we only need to store **the remainder** (`prefix_sum % k`).

Furthermore, because the problem introduces a length constraint ($\ge 2$), our Hash Map shouldn't track _frequencies_ this time. It needs to store the **earliest index** where that specific remainder was first seen. When we encounter the same remainder again at a later index `j`, we check if `j - earliest_index >= 2`.

### Concrete Example: `nums = [23, 2, 4, 6, 7]`, `k = 6`

Let's track the remainders and the indices where they first appear:

- **Before starting:** Remainder 0 is at index `-1`. Map: `{0: -1}`.
- **Index 0 (`num = 23`):** `sum = 23`. `23 % 6 = 5`. Remainder 5 is new. Save it: `{0: -1, 5: 0}`.
- **Index 1 (`num = 2`):** `sum = 25`. `25 % 6 = 1`. Remainder 1 is new. Save it: `{0: -1, 5: 0, 1: 1}`.
- **Index 2 (`num = 4`):** `sum = 29`. `29 % 6 = 5`. Remainder 5 has been seen before!
- It was first seen at index `0`.
- Check the length constraint: $\text{Current Index } 2 - \text{Old Index } 0 = 2$.
- The length is $\ge 2$. We instantly return `true`. (The subarray is `[2, 4]` which sums to 6).

---

## 4. Conceptual Algorithm (No Code)

We will use a single pass through the array, maintaining:

1. `running_sum`: Accumulates the elements as we iterate.
2. `remainder_indices`: A Hash Map (`std::unordered_map<int, int>`) mapping a `remainder -> earliest_index_seen`.

### Step-by-Step Walkthrough:

- **Seed the Hash Map** with `{0 : -1}`. This signifies that a remainder of 0 exists right before the array starts (at dummy index -1), which handles cases where a valid multiple of $k$ begins cleanly at index 0.
- **Loop** through each number using index `j`:

1. Add the number to `running_sum`.
2. Compute the remainder: `remainder = running_sum % k`.
3. _Account for negative numbers if present (C++ handles negative modulo differently, so ensure remainder is positive: `(remainder + k) % k`)._
4. **Check the Map:** If this `remainder` already exists in `remainder_indices`:

- Fetch the `earliest_index`.
- Check if `j - earliest_index >= 2`. If it is, return `true`.

5. **If it doesn't exist:** Insert the remainder mapped to the current index `j`. _(Crucial: Do not update the index if it already exists, because we want to preserve the earliest possible index to maximize our subarray length check!)_

- If the loop finishes without a match, return `false`.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases:

- **Subarray starts at index 0 (e.g., `nums = [6, 6]`, `k = 6`):** At index 1, `running_sum = 12`, `12 % 6 = 0`. The map contains `0` at index `-1`. `1 - (-1) = 2`, which passes the length check.
- **Remainder cycles without satisfying length (e.g., `nums = [0, 0]`, `k = 1`):** Two consecutive zeros will maintain a remainder of 0, expanding the length past index `-1` to trigger a valid return.

### Target Complexities:

- **Time Complexity:** $O(N)$ — A single linear sweep with average $O(1)$ Hash Map lookup and insertion operations.
- **Space Complexity:** $O(\min(N, k))$ — The hash map can hold at most $N$ elements, or at most $k$ unique remainders (since any number modulo $k$ only yields values from $0$ to $k-1$).

---

## The C++ Implementation

Here is the elegant C++ code implementing this specialized lookup:

```cpp
#include <vector>
#include <unordered_map>

class Solution {
public:
    bool checkSubarraySum(std::vector<int>& nums, int k) {
        // Hash map to store: [Remainder -> Earliest Index Seen]
        std::unordered_map<int, int> remainder_indices;

        // Base case: A remainder of 0 occurs at dummy index -1
        remainder_indices[0] = -1;

        int running_sum = 0;

        for (int j = 0; j < nums.size(); ++j) {
            running_sum += nums[j];

            int remainder = running_sum % k;
            // Handle potentially negative numbers safely to keep remainder positive
            if (remainder < 0) {
                remainder += k;
            }

            // If the remainder has been seen before
            if (remainder_indices.find(remainder) != remainder_indices.end()) {
                int earliest_index = remainder_indices[remainder];
                // Validate if the contiguous length is at least 2
                if (j - earliest_index >= 2) {
                    return true;
                }
            } else {
                // Only store the first occurrence of a remainder to preserve maximum span
                remainder_indices[remainder] = j;
            }
        }

        return false;
    }
};

```

---

## **LeetCode 974: Subarray Sums Divisible by K**.

https://youtu.be/7Xeorb721LQ?si=yy5D4DgWfZl3AAby

## 1. Core Pattern Recognition

### The Clues in the Problem Description

- **"Contiguous Subarray":** The classic indicator that you need to use **Prefix Sums** to handle array slices efficiently.
- **"Divisible by K":** Like LC 523, we are looking for a sum that is a multiple of $k$. This means we will be tracking **remainders** (`sum % k == 0`) rather than exact target values.
- **Counting the Total Number:** Unlike LC 523, which just asked for a `true`/`false` existence check, this problem asks for the _total count_ of such subarrays. This means we are shifting back to a **Frequency Map** (just like LC 560 and 930), but our keys will be the remainders.

---

## 2. The "Naive" Starting Point

- **The Approach:** Run a nested loop configuration where you generate every possible contiguous subarray. For each subarray, calculate its sum, and check if `sum % k == 0`. If it is, increment your global counter.
- **Why it's inefficient:** There are $O(N^2)$ subarrays. Calculating and checking each one results in a time complexity of $O(N^2)$, which will result in a Time Limit Exceeded (TLE) error for an array of size $3 \times 10^4$.
- **The Redundant Work:** Shifting the left boundary over and over again forces you to discard previous cumulative calculations and re-sum interior elements repeatedly.

---

## 3. The Intuition Shift

We rely on the exact same modulo arithmetic principle we uncovered in LC 523:

$$\text{Subarray Sum} = \text{Prefix Sum up to } j - \text{Prefix Sum up to } (i-1)$$

For the subarray sum to be divisible by $k$, its remainder must be 0:

$$(\text{Prefix Sum up to } j - \text{Prefix Sum up to } (i-1)) \pmod k = 0$$

Which simplifies to:

$$\text{Prefix Sum up to } j \pmod k = \text{Prefix Sum up to } (i-1) \pmod k$$

### The "Aha!" Moment

If your current prefix sum at index `j` leaves a remainder of `r`, any previous prefix sum in your history that _also_ left a remainder of `r` creates a valid divisible subarray between that old position and your current position.

Since we want to count _all_ possible valid subarrays, we don't just care _if_ a remainder has appeared before; we need to know **how many times** it has appeared. If a remainder of `2` has appeared 3 times in the past, it means there are exactly 3 historical split-points that can pair with our current index to form a divisible subarray.

---

## 4. Conceptual Algorithm (No Code)

We will use a single pass through the array, maintaining:

1. `running_sum`: Accumulates the elements as we go.
2. `remainder_frequencies`: A Hash Map (`std::unordered_map<int, int>`) tracking `[remainder -> frequency count]`.
3. `total_subarrays`: An integer tracking our global count of valid subarrays.

### Step-by-Step Walkthrough:

- **Seed the Hash Map** with `{0 : 1}`. This represents a remainder of 0 occurring once before we start, catching any valid prefix sum that is divisible by $k$ right from index 0.
- **Loop** through each number in the array:

1. Add the current number to `running_sum`.
2. Compute the remainder: `remainder = running_sum % k`.
3. **Crucial Adjustment for C++:** C++ allows negative remainders (e.g., `-2 % 5 = -2`). In modulo arithmetic, a remainder of `-2` is equivalent to a remainder of `3` when $k=5$. We must normalize negative remainders: `if (remainder < 0) remainder += k;`.
4. **Check the Map:** If this normalized `remainder` exists in our map, add its frequency count directly to `total_subarrays`.
5. **Update the Map:** Increment the frequency count of this `remainder` by 1 so future elements can look back at it.

- Return `total_subarrays`.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases:

- **Negative Numbers (e.g., `nums = [-1, 2, 9]`, `k = 2`):** Because we normalize the remainder to always be positive (`remainder = (remainder % k + k) % k`), negative numbers will match their positive mathematical equivalents perfectly.
- **Perfect Multiples:** If an element itself is a multiple of $k$, the remainder won't change from the previous step, meaning it will immediately form a valid subarray with the index right before it.

### Target Complexities:

- **Time Complexity:** $O(N)$ — A single pass through the array with $O(1)$ average hash map lookup and insertion operations.
- **Space Complexity:** $O(\min(N, k))$ — The hash map will never store more than $k$ unique elements, because any integer modulo $k$ can only produce a remainder between $0$ and $k-1$.

---

## The C++ Implementation

Here is the clean, easy-to-follow C++ code that beautifully glues your frequency tracking and modulo patterns together:

```cpp
#include <vector>
#include <unordered_map>

class Solution {
public:
    int subarraysDivByK(std::vector<int>& nums, int k) {
        // Hash map to store: [Remainder -> Frequency of occurrence]
        std::unordered_map<int, int> remainder_frequencies;

        // Base case: A remainder of 0 has occurred 1 time initially
        remainder_frequencies[0] = 1;

        int running_sum = 0;
        int total_subarrays = 0;

        for (int num : nums) {
            running_sum += num;

            int remainder = running_sum % k;
            // Normalize negative remainders to be strictly positive
            if (remainder < 0) {
                remainder += k;
            }

            // If this remainder occurred in the past, add its frequency to our count
            if (remainder_frequencies.find(remainder) != remainder_frequencies.end()) {
                total_subarrays += remainder_frequencies[remainder];
            }

            // Record the current remainder into our precomputed history map
            remainder_frequencies[remainder]++;
        }

        return total_subarrays;
    }
};

```
