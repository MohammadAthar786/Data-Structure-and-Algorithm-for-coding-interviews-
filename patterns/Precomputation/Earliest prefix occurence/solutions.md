## **LeetCode 325: Maximum Size Subarray Sum Equals k**

---

## 1. Core Pattern Recognition

### The Clues in the Problem Description

- **"Contiguous Subarray":** The definitive signal that you need a **Prefix Sum** to compute subarray segments efficiently without re-adding elements.
- **"Sum Equals k":** You are looking for a precise target value, meaning we look for an exact numerical complement rather than using modulo/remainders.
- **"Maximum Size":** This is the ultimate clue for the **Earliest Prefix Occurrence** sub-pattern. If you want a subarray ending at index `j` to be as large as possible, the starting point `i` needs to be as far left as possible. To make `i` as small as possible, you must record the _very first time_ you ever see a particular prefix sum value and refuse to overwrite it later.

---

## 2. The "Naive" Starting Point

- **The Approach:** You check every single possible contiguous subarray by picking a start index `i` and an end index `j`. You compute the sum of that range. If the sum equals `k`, you calculate the length `j - i + 1` and update your `max_length`.
- **Why it's inefficient:** Checking all possible pairs requires nested loops, leading to an $O(N^2)$ time complexity. For large arrays, this results in a _Time Limit Exceeded_ (TLE) error.
- **The Redundant Work:** As your end pointer `j` expands, you scan backward across all historical starting positions over and over again. You completely discard the cumulative sums you have already computed, calculating them from scratch for different left boundaries.

---

## 3. The Intuition Shift

We leverage our trusty algebraic formula one more time:

$$\text{Subarray Sum} = \text{Prefix Sum up to } j - \text{Prefix Sum up to } (i-1)$$

We want this $\text{Subarray Sum}$ to be exactly $k$:

$$k = \text{Prefix Sum up to } j - \text{Prefix Sum up to } (i-1)$$

Rearranging to look at it from our current position $j$:

$$\text{Prefix Sum up to } (i-1) = \text{Prefix Sum up to } j - k$$

### The "Aha!" Moment

When you are standing at index `j`, you calculate a `target = current_prefix_sum - k`. If that `target` prefix sum exists in your history, the elements between that old position and your current position sum to $k$.

To make this specific subarray as **long** as possible, you want the `target` prefix sum to have happened **as early as possible** in the array.

Therefore, instead of a frequency map, we use a Hash Map that stores **`[Prefix Sum -> The Earliest Index Where It Occurred]`**. If we encounter the same prefix sum again later, we **ignore** it and keep the older index to preserve the maximum possible distance.

### Concrete Example: `nums = [1, -1, 5, -2, 3]`, `k = 3`

Let's track the running prefix sum and map:

- **Before starting:** Prefix sum 0 occurred at dummy index `-1`. Map: `{0: -1}`.
- **Index 0 (`num = 1`):** `sum = 1`. Target lookup: `1 - 3 = -2`. Not found. Save `1` at index `0`. Map: `{0: -1, 1: 0}`.
- **Index 1 (`num = -1`):** `sum = 0`. Target lookup: `0 - 3 = -3`. Not found. `0` already exists in the map at index `-1`. **Do not overwrite it!** Map stays: `{0: -1, 1: 0}`.
- **Index 2 (`num = 5`):** `sum = 5`. Target lookup: `5 - 3 = 2`. Not found. Save `5` at index `2`. Map: `{0: -1, 1: 0, 5: 2}`.
- **Index 3 (`num = -2`):** `sum = 3`. Target lookup: `3 - 3 = 0`. Found!
- `0` was earliest seen at index `-1`.
- Length = `3 - (-1) = 4`. (`max_len = 4`, corresponding to `[1, -1, 5, -2]`).
- Save `3` at index `3`. Map: `{0: -1, 1: 0, 5: 2, 3: 3}`.

- **Index 4 (`num = 3`):** `sum = 6`. Target lookup: `6 - 3 = 3`. Found!
- `3` was earliest seen at index `3`.
- Length = `4 - 3 = 1`. `max_len` remains `4`.

---

## 4. Conceptual Algorithm (No Code)

We will use a single pass through the array, tracking:

1. `current_prefix_sum`: The running total from index 0.
2. `prefix_earliest_index`: A Hash Map (`std::unordered_map<int, int>`) tracking `[Prefix Sum -> Earliest Index]`.
3. `max_length`: An integer tracking the maximum subarray length found (initialize to 0).

### Step-by-Step Walkthrough:

- **Seed the Hash Map** with `{0 : -1}` so that subarrays starting right from index 0 can calculate their length correctly via `j - (-1)`.
- **Loop** through each number in the array using index `j`:

1. Add the number to `current_prefix_sum`.
2. Calculate your historical complement: `target = current_prefix_sum - k`.
3. **Check the Map:** If `target` exists in `prefix_earliest_index`, fetch its index (`earliest_i`). Calculate the potential length: `j - earliest_i`. Update `max_length` if this length is larger.
4. **Update the Map Safely:** Check if `current_prefix_sum` is already in the map. If it is **not**, insert it with the current index `j`. If it is already there, do nothing.

- Return `max_length`.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases:

- **The whole array sums to k:** Seeding `{0 : -1}` ensures that if `current_prefix_sum - k == 0` at the very last index `N-1`, the calculation `(N-1) - (-1) = N` evaluates perfectly.
- **No valid subarray exists:** `max_length` will remain `0`, which is the correct output.
- **Zeros and Negative numbers:** They cause the prefix sum to fluctuate or stay flat. By preventing our map from overwriting existing keys, we securely anchor our left boundary to its furthest possible position.

### Target Complexities:

- **Time Complexity:** $O(N)$ — A single linear pass over the array with average $O(1)$ Hash Map lookups.
- **Space Complexity:** $O(N)$ — In the worst-case scenario, the map stores up to $N$ unique prefix sum pairs.

---

## The C++ Implementation

Here is the clean, readable C++ implementation for this pattern:

```cpp
#include <vector>
#include <unordered_map>
#include <algorithm>

class Solution {
public:
    int maxSubArrayLen(std::vector<int>& nums, int k) {
        // Hash map to store: [Prefix Sum -> Earliest Index Seen]
        std::unordered_map<int, int> prefix_earliest_index;

        // Base case: A prefix sum of 0 occurs at dummy index -1
        prefix_earliest_index[0] = -1;

        long long current_prefix_sum = 0; // Using long long to prevent integer overflow
        int max_length = 0;

        for (int j = 0; j < nums.size(); ++j) {
            current_prefix_sum += nums[j];

            // Calculate what historical prefix sum we need to find
            long long target = current_prefix_sum - k;

            // If that target exists in our history, calculate the distance
            if (prefix_earliest_index.find(target) != prefix_earliest_index.end()) {
                int earliest_i = prefix_earliest_index[target];
                max_length = std::max(max_length, j - earliest_i);
            }

            // CRUCIAL: Only record the prefix sum if it hasn't been seen before.
            // This guarantees we preserve the furthest left boundary.
            if (prefix_earliest_index.find(current_prefix_sum) == prefix_earliest_index.end()) {
                prefix_earliest_index[current_prefix_sum] = j;
            }
        }

        return max_length;
    }
};

```

---

## **LeetCode 525: Contiguous Array**.

https://youtu.be/AcSWGXBu-Xo?si=FoLGdv4wtB4U4twc

## 1. Core Pattern Recognition

### The Clues in the Problem Description

- **"Contiguous Subarray":** The keyword telling you that a **Prefix Sum** mechanism will be needed to handle array intervals efficiently.
- **"Equal number of 0 and 1":** You are balancing two types of elements. Whenever you need to find balanced subsets (e.g., equal numbers of X and Y, equal matching brackets, etc.), you can treat one element as a positive weight ($+1$) and the other as a negative weight ($-1$).
- **"Maximum Length":** This tells you that you are maximizing a distance. To maximize the distance ending at index `j`, you need to look back and find the _earliest possible index_ where the exact same net balance occurred.

---

## 2. The "Naive" Starting Point

- **The Approach:** You check every possible contiguous subarray using nested loops (start index `i` and end index `j`). For each subarray, you count the number of `0`s and `1`s. If they are equal, you compute the length `j - i + 1` and update your max length variable.
- **Why it's inefficient:** There are $O(N^2)$ subarrays, and counting elements explicitly takes up to $O(N)$ time per subarray, yielding an $O(N^3)$ or slightly optimized $O(N^2)$ time complexity. For an array of size $10^5$, this will severely time out.
- **The Redundant Work:** Shifting your boundaries repeatedly forces you to recount matching sets of elements over and over again, discarding the perfect history of the array's balance up to that point.

---

## 3. The Intuition Shift

Let's apply the **$+1$ / $-1$ transformation**:

- Change every `1` into a `+1`.
- Change every `0` into a `-1`.

If a subarray has an _equal number of 0s and 1s_, then the sum of its transformed elements will be exactly **0**!

Suddenly, the problem transforms from _"find a subarray with equal 0s and 1s"_ into **"find the maximum length of a subarray whose sum equals 0"**. This is literally LeetCode 325 where $k = 0$.

Let's look at the prefix sum equation again:

$$\text{Subarray Sum} = \text{Prefix Sum up to } j - \text{Prefix Sum up to } (i-1)$$

We want this $\text{Subarray Sum} = 0$:

$$0 = \text{Prefix Sum up to } j - \text{Prefix Sum up to } (i-1)$$

$$\text{Prefix Sum up to } (i-1) = \text{Prefix Sum up to } j$$

### The "Aha!" Moment

If your running prefix sum at index `j` is `S`, and you saw that exact same prefix sum `S` earlier at index `i-1`, it means the net change between those two points was exactly 0. Therefore, the elements between them must contain an equal number of 0s and 1s. To make this subarray as long as possible, you want to match your current index `j` with the **earliest possible index** where `S` was first seen.

### Concrete Example: `nums = [0, 1, 0]`

Transform `nums` mentally to: `[-1, 1, -1]`
Let's track the running prefix sum and map:

- **Before starting:** Sum 0 occurred at dummy index `-1`. Map: `{0: -1}`.
- **Index 0 (`num = 0 -> -1`):** `sum = -1`. Have we seen `-1`? No. Record it: `{0: -1, -1: 0}`.
- **Index 1 (`num = 1 -> +1`):** `sum = -1 + 1 = 0`. Have we seen `0`? Yes, at dummy index `-1`!
- Calculate length: $\text{Current Index } 1 - \text{Old Index } (-1) = 2$.
- `max_length = 2` (Subarray is `[0, 1]`).
- `0` is already in the map, so **do not overwrite it**.

- **Index 2 (`num = 0 -> -1`):** `sum = 0 + (-1) = -1`. Have we seen `-1`? Yes, it was first seen at index `0`!
- Calculate length: $\text{Current Index } 2 - \text{Old Index } 0 = 2$.
- `max_length` remains 2.

---

## 4. Conceptual Algorithm (No Code)

We will use a single pass through the array, tracking:

1. `current_balance`: An integer tracking our cumulative sum (adding 1 for a `1`, subtracting 1 for a `0`).
2. `balance_earliest_index`: A Hash Map (`std::unordered_map<int, int>`) tracking `[Balance Value -> Earliest Index Seen]`.
3. `max_length`: An integer tracking the maximum valid length found (initialize to 0).

### Step-by-Step Walkthrough:

- **Seed the Hash Map** with `{0 : -1}` to gracefully catch balanced subarrays that begin right from index 0.
- **Loop** through the array using index `j`:

1. If `nums[j] == 1`, add 1 to `current_balance`. Otherwise, subtract 1 from `current_balance`.
2. **Check the Map:** If `current_balance` already exists in `balance_earliest_index`:

- Retrieve the `earliest_i`.
- Compute the length: `j - earliest_i`.
- Update `max_length` if this length is larger.

3. **Update the Map Safely:** If `current_balance` is **not** in the map, insert it mapped to the current index `j`. If it is already present, do not touch it (preserving the furthest left anchor).

- Return `max_length`.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases:

- **Array starts with a perfect balance (e.g., `[0, 1]`):** Seeding `{0: -1}` ensures that at index 1, where the balance returns to 0, the distance calculates perfectly to `1 - (-1) = 2`.
- **No balanced subarrays exist (e.g., `[1, 1, 1]`):** The balance keeps climbing monotonically, never matching an old state. `max_length` returns 0.

### Target Complexities:

- **Time Complexity:** $O(N)$ — One clean pass over the array with average $O(1)$ Hash Map lookups.
- **Space Complexity:** $O(N)$ — In the worst case (e.g., all 1s), the hash map will grow linearly up to $N$ entries.

---

## The C++ Implementation

Here is the clean, easy-to-follow C++ code implementing this beautiful transformation:

```cpp
#include <vector>
#include <unordered_map>
#include <algorithm>

class Solution {
public:
    int findMaxLength(std::vector<int>& nums) {
        // Hash map to store: [Running Balance -> Earliest Index Seen]
        std::unordered_map<int, int> balance_earliest_index;

        // Base case: A balance of 0 occurs at dummy index -1
        balance_earliest_index[0] = -1;

        int current_balance = 0;
        int max_length = 0;

        for (int j = 0; j < nums.size(); ++j) {
            // 1. Transform on the fly: +1 for '1', -1 for '0'
            if (nums[j] == 1) {
                current_balance += 1;
            } else {
                current_balance -= 1;
            }

            // 2. If this net balance has been seen before, we found a zero-sum segment
            if (balance_earliest_index.find(current_balance) != balance_earliest_index.end()) {
                int earliest_i = balance_earliest_index[current_balance];
                max_length = std::max(max_length, j - earliest_i);
            }
            // 3. Otherwise, log it. Only store the earliest occurrence to maximize span.
            else {
                balance_earliest_index[current_balance] = j;
            }
        }

        return max_length;
    }
};

```

---

## **LeetCode 1124: Longest Well-Performing Interval**.

## 1. Core Pattern Recognition

### The Clues in the Problem Description

- **"Contiguous Interval of Days":** Once again, a clear sign that we are hunting for subarrays, making **Prefix Sums** our foundational tool.
- **"Well-Performing Interval":** Defined as an interval where tiring days (hours > 8) are _strictly greater_ than non-tiring days. We are balancing two opposing conditions. This screams for a **$+1$ / $-1$ transformation**.
- **"Longest":** This dictates that we are maximizing a distance, which means we must anchor our lookup to the **earliest possible historical occurrence**.

---

## 2. The "Naive" Starting Point

- **The Approach:** You run a nested loop setup (`i` for start day, `j` for end day). For each window, you count how many days have hours > 8 and how many don't. If $\text{tiring} > \text{non-tiring}$, you calculate the length `j - i + 1` and update your max tracker.
- **Why it's inefficient:** Checking all possible pairs requires an $O(N^2)$ time complexity. With an array size of $10^4$, this can easily border on a Time Limit Exceeded (TLE) state depending on strict execution limits.
- **The Redundant Work:** When moving the window, you re-evaluate sub-intervals from scratch, entirely ignoring the relative cumulative balance you built up leading to that point.

---

## 3. The Intuition Shift

Let’s apply the $+1$ / $-1$ rule on the fly:

- If `hours[i] > 8`, treat it as a **`+1`** (Tiring day).
- If `hours[i] <= 8`, treat it as a **`-1`** (Non-tiring day).

Our goal changes from a wordy description to a clear mathematical one: **Find the longest subarray whose sum is strictly greater than 0 ($\text{Sum} > 0$).**

Let's look at our prefix sum inequality:

$$\text{Subarray Sum} = \text{Prefix Sum up to } j - \text{Prefix Sum up to } (i-1) > 0$$

$$\text{Prefix Sum up to } j > \text{Prefix Sum up to } (i-1)$$

When standing at index `j` with a `current_prefix_sum`, two scenarios can unfold:

1. **`current_prefix_sum > 0`**: This means from day 0 all the way to day `j`, there are strictly more tiring days than non-tiring days. The entire interval from the very beginning is valid! The maximum length ending at `j` is simply `j + 1`.
2. **`current_prefix_sum <= 0`**: The interval from day 0 isn't valid. We need to look back into our history map for an older prefix sum that is strictly _smaller_ than our current one.

### The "Aha!" Moment

Since our prefix sum changes by exactly $+1$ or $-1$ at each step, the prefix sum decreases or increases gradually. If we are at a non-positive sum (say, `-2`), the earliest prefix sum that is smaller than `-2` will always be exactly **`-3`** (`current_prefix_sum - 1`).

Why? Because to reach any lower value (like `-4` or `-5`) from our initial starting point of `0`, the running total _must_ have crossed `-3` first. Thus, the earliest occurrence of `current_prefix_sum - 1` will always be further left than any lower value, perfectly maximizing our subarray length.

---

## 4. Conceptual Algorithm (No Code)

We will sweep the array once, keeping track of:

1. `current_score`: The running total ($+1$ for tiring day, $-1$ for normal day).
2. `earliest_index`: A Hash Map (`std::unordered_map<int, int>`) storing `[Prefix Sum Value -> Earliest Index Seen]`.
3. `max_length`: An integer tracking our maximum interval length.

### Step-by-Step Walkthrough:

- **Loop** through the array using index `j`:

1. Update `current_score` based on `hours[j]`.
2. **Check Case 1:** If `current_score > 0`, the whole prefix is valid. Set `max_length = j + 1`.
3. **Check Case 2:** If `current_score <= 0`, look for `target = current_score - 1` in our map.

- If `target` exists, calculate the distance: `j - earliest_index[target]` and maximize `max_length`.

4. **Update History Map:** If `current_score` is **not** already in the map, log it: `earliest_index[current_score] = j`. (If it is already there, don't overwrite it, as we want to preserve the furthest left boundary).

---

## 5. Edge Cases & Complexity

### Critical Edge Cases:

- **No tiring days at all (e.g., `[5, 5, 5]`):** `current_score` continuously drops. `max_length` remains `0`.
- **Alternating values starting poorly (e.g., `[6, 9]`):** At index 0, score is `-1` (logged). At index 1, score becomes `0`. It looks for `0 - 1 = -1`. It finds `-1` at index 0. Length is `1 - 0 = 1`. Correct.

### Target Complexities:

- **Time Complexity:** $O(N)$ — One pass through the array with $O(1)$ hash map lookups.
- **Space Complexity:** $O(N)$ — Storing unique running scores in our map.

---

## The C++ Implementation

Here is the clean, highly optimized C++ solution:

```cpp
#include <vector>
#include <unordered_map>
#include <algorithm>

class Solution {
public:
    int longestWPI(std::vector<int>& hours) {
        // Hash map to store: [Prefix Score -> Earliest Index Seen]
        std::unordered_map<int, int> earliest_index;

        int current_score = 0;
        int max_length = 0;

        for (int j = 0; j < hours.size(); ++j) {
            // 1. Transform on the fly: +1 for tiring day (>8), -1 otherwise
            current_score += (hours[j] > 8) ? 1 : -1;

            // 2. Case 1: If the entire prefix from index 0 is valid
            if (current_score > 0) {
                max_length = j + 1;
            }
            // 3. Case 2: Look for the complement that gives a net positive sum
            else {
                int target = current_score - 1;
                if (earliest_index.find(target) != earliest_index.end()) {
                    max_length = std::max(max_length, j - earliest_index[target]);
                }

                // Only log the score if it hasn't been seen before to preserve max distance
                if (earliest_index.find(current_score) == earliest_index.end()) {
                    earliest_index[current_score] = j;
                }
            }
        }

        return max_length;
    }
};

```
