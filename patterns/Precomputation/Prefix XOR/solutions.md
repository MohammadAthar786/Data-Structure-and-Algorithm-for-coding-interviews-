## **Count Subarrays with Given XOR** (often found on InterviewBit / GeeksforGeeks / CodeStudio).

---

## 1. Core Pattern Recognition

### The Clues in the Problem Description

- **"Subarrays":** As always, any question asking for contiguous intervals strongly points toward a **Prefix** tracking mechanism.
- **"Given XOR":** You are looking for a contiguous segment whose elements, when XORed together, equal a specific target value $B$.
- **The Operator properties:** XOR behaves fundamentally like addition/subtraction but without any carry-over. Because it is its own inverse, it is a perfect candidate for prefix-style isolation.

---

## 2. The "Naive" Starting Point

- **The Approach:** You check every single possible contiguous subarray by picking a start index `i` and an end index `j`. You compute the bitwise XOR of all elements from `i` to `j`. If that cumulative XOR equals your target $B$, you increment a counter.
- **Why it's inefficient:** There are $O(N^2)$ subarrays. Even if you optimize it to accumulate the XOR dynamically as `j` moves forward, you still require two nested loops, leading to a **$O(N^2)$ time complexity**.
- **The Redundant Work:** When your left boundary shifts from `i` to `i+1`, you throw away all previous bitwise calculations. You force the machine to re-compute interior combinations over and over again from scratch across different starting positions.

---

## 3. The Intuition Shift

To break out of the $O(N^2)$ trap, we need to review a crucial property of the XOR operator:

1. $X \oplus X = 0$ (Any number XORed with itself cancels out).
2. $X \oplus 0 = X$ (Identity property).

Let's visualize the array split. The prefix XOR from the beginning of the array up to our current index $j$ can be broken into two halves: the unwanted prefix up to index $i-1$, and the target subarray from $i$ to $j$.

$$\text{Prefix\_XOR}[j] = \text{Prefix\_XOR}[i-1] \oplus \text{Subarray\_XOR}$$

We want our $\text{Subarray\_XOR}$ to be exactly our target $B$:

$$\text{Prefix\_XOR}[j] = \text{Prefix\_XOR}[i-1] \oplus B$$

Now, let's use the cancellation property to isolate $\text{Prefix\_XOR}[i-1]$. If we XOR both sides of the equation with $B$:

$$\text{Prefix\_XOR}[j] \oplus B = \text{Prefix\_XOR}[i-1] \oplus B \oplus B$$

$$\text{Prefix\_XOR}[j] \oplus B = \text{Prefix\_XOR}[i-1] \oplus 0$$

$$\text{Prefix\_XOR}[i-1] = \text{Prefix\_XOR}[j] \oplus B$$

### The "Aha!" Moment

When you are standing at index `j`, you know your current `Prefix_XOR[j]`. You do not need to look backward. You simply calculate a target value: `current_prefix_xor ^ B`.

If that exact XOR value has appeared in your history, it means the segment between that historical point and your current index perfectly cancels out to leave exactly $B$. By tracking the **frequencies** of past prefix XOR values in a Hash Map, you can instantly look back and count your valid subarrays in $O(1)$ time.

### Concrete Example: `A = [4, 2, 2, 6, 4]`, `B = 6`

Let's track the running prefix XOR:

- **Before starting:** Prefix XOR 0 occurred 1 time. Map: `{0: 1}`.
- **Index 0 (`val = 4`):** `current_xor = 4`. Target lookup: `4 ^ 6 = 2`. Has `2` appeared? No. Update map: `{0: 1, 4: 1}`.
- **Index 1 (`val = 2`):** `current_xor = 4 ^ 2 = 6`. Target lookup: `6 ^ 6 = 0`. Has `0` appeared? Yes, 1 time! `count = 1`. Update map: `{0: 1, 4: 1, 6: 1}`.
- **Index 2 (`val = 2`):** `current_xor = 6 ^ 2 = 4`. Target lookup: `4 ^ 6 = 2`. Not found. Update map: `{0: 1, 4: 2, 6: 1}` (4 now appeared twice).
- **Index 3 (`val = 6`):** `current_xor = 4 ^ 6 = 2`. Target lookup: `2 ^ 6 = 4`. Has `4` appeared? Yes, 2 times! `count = 1 + 2 = 3`.

---

## 4. Conceptual Algorithm (No Code)

We will process the array in a single linear pass, tracking:

1. `current_prefix_xor`: Keeps track of the running bitwise XOR from index 0.
2. `xor_frequencies`: A Hash Map (`std::unordered_map<int, int>`) storing `[Prefix XOR value -> Frequency Count]`.
3. `total_subarrays`: An integer tracking our global count of valid subarrays.

### Step-by-Step Walkthrough:

- **Seed the Hash Map** with `{0 : 1}` to handle cases where a subarray starting directly from index 0 evaluates perfectly to the target $B$.
- **Loop** through each element in the array:

1. Update `current_prefix_xor` by XORing it with the current element.
2. Compute the historical prefix XOR we need to find: `target = current_prefix_xor ^ B`.
3. **Check the Map:** If `target` exists in `xor_frequencies`, add its frequency count to `total_subarrays`.
4. **Update the Map:** Increment the frequency count of `current_prefix_xor` in the map by 1 so future elements can look back at it.

- Return `total_subarrays`.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases:

- **The target B is 0:** If $B = 0$, our lookup is `current_prefix_xor ^ 0 = current_prefix_xor`. The map will search for how many times the exact same prefix XOR has appeared before, which perfectly catches duplicates (segments that cancel themselves out entirely).
- **Single element matches B:** If the very first element equals $B$, then `current_prefix_xor ^ B = 0`. Seeding the map with `{0: 1}` guarantees this is immediately counted.

### Target Complexities:

- **Time Complexity:** $O(N)$ — A single linear scan through the array with average $O(1)$ Hash Map lookups and insertions.
- **Space Complexity:** $O(N)$ — In the worst-case scenario where all prefix XOR outcomes are distinct, the map can grow up to $N$ entries.

---

## The C++ Implementation

Here is the clean, efficient, and easy-to-follow C++ code that implements this prefix XOR logic:

```cpp
#include <vector>
#include <unordered_map>

class Solution {
public:
    int solve(std::vector<int> &A, int B) {
        // Hash map to store: [Prefix XOR -> Frequency of occurrence]
        std::unordered_map<int, int> xor_frequencies;

        // Base case: A prefix XOR of 0 has occurred 1 time initially
        xor_frequencies[0] = 1;

        int current_prefix_xor = 0;
        int total_subarrays = 0;

        for (int num : A) {
            // 1. Maintain running precomputed XOR state
            current_prefix_xor ^= num;

            // 2. Identify the target historical state using XOR properties
            int target = current_prefix_xor ^ B;

            // 3. Add historical frequencies to total count if they exist
            if (xor_frequencies.find(target) != xor_frequencies.end()) {
                total_subarrays += xor_frequencies[target];
            }

            // 4. Update the history map with the current prefix XOR
            xor_frequencies[current_prefix_xor]++;
        }

        return total_subarrays;
    }
};

```

Look at how neatly this slots into your brain! Standard addition uses subtraction (`Prefix_Sum - K`) to look backward, while bitwise XOR uses itself (`Prefix_XOR ^ B`) to look backward.

---

**LeetCode 1442: Count Triplets That Can Form Two Arrays of Equal XOR**.

## 1. Core Pattern Recognition

### The Clues in the Problem Description

- **"Triplets of indices $(i, j, k)$"**: You are looking for boundaries that define slices of data.
- **"Two contiguous arrays $a$ and $b$"**: Array $a$ spans from $i$ to $j-1$, and array $b$ spans from $j$ to $k$. The word "contiguous" is your massive green light for a **Prefix** pattern.
- **"$a == b$" (Equal XOR)**: You need to evaluate whether the cumulative bitwise XOR of one contiguous section matches the cumulative bitwise XOR of an adjacent contiguous section. Because the bitwise XOR operator is its own inverse, evaluating equality across continuous boundaries screams **Prefix XOR**.

---

## 2. The "Naive" Starting Point

- **The Approach**: The literal translation of the problem description involves setting up three nested loops to choose your indices: an outer loop for $i$, a middle loop for $j$, and an inner loop for $k$. For every single triplet, you would run a loop to calculate the XOR sum of array $a$ and another loop to calculate the XOR sum of array $b$.
- **Why it's inefficient**: This completely naive simulation takes $O(N^4)$ time. Even if you optimize it slightly by accumulating the XOR sums dynamically as the pointers expand, you still need three nested loops to test all combinations of $i$, $j$, and $k$, which hits a wall at **$O(N^3)$ time complexity**.
- **The Redundant Work**: Imagine you find that the range from $i=0$ to $k=5$ can be split at $j=2$. When you check $j=3$ for the exact same $i$ and $k$, you completely recalculate the XOR values for the left and right slices, totally ignoring the fact that the outer boundaries ($i$ and $k$) never changed.

---

## 3. The Intuition Shift

Let's look at the mathematical property that completely collapses this problem from three dimensions down to one.

We are looking for a split point $j$ such that:

$$a = b$$

If we XOR both sides with $b$, we leverage the property that any number XORed with itself is 0 ($b \oplus b = 0$):

$$a \oplus b = 0$$

Because array $a$ (from $i$ to $j-1$) and array $b$ (from $j$ to $k$) are perfectly adjacent, XORing them together ($a \oplus b$) gives you the **total cumulative XOR sum of the entire subarray from index $i$ all the way to index $k$**.

### The "Aha!" Moment

> The internal split point $j$ does not actually matter during our search! If the total XOR sum of a subarray from index $i$ to $k$ is exactly 0, **any** index $j$ you pick between them will automatically split that subarray into two equal XOR halves ($a == b$).

If a subarray from $i$ to $k$ has a total XOR sum of 0, how many valid choices of $j$ do we have? Since $i < j \le k$, $j$ can be anything from $i+1$ up to $k$. The number of valid elements is exactly:

$$\text{Number of choices for } j = k - i$$

### Concrete Example: `arr = [2, 3, 1, 6]`

Let's find subarrays that XOR to 0.

- Look at the slice `[2, 3, 1]` which spans from index $i=0$ to $k=2$.
- $2 \oplus 3 \oplus 1 = 0$.
- Because this entire block XORs to 0, how many triplets can we form? Let's check $k - i = 2 - 0 = 2$ choices for $j$:
- **Choice 1 ($j=1$):** $a = \text{arr}[0] = 2$, $b = \text{arr}[1 \dots 2] = 3 \oplus 1 = 2$. ($2 == 2$, Valid!)
- **Choice 2 ($j=2$):** $a = \text{arr}[0 \dots 1] = 2 \oplus 3 = 1$, $b = \text{arr}[2] = 1$. ($1 == 1$, Valid!)

We went from searching for three independent variables $(i, j, k)$ to a single, much simpler question: **Find a pair of boundaries $(i, k)$ such that the subarray XOR sum is 0.**

---

## 4. Conceptual Algorithm (No Code)

We know from our prefix history that a subarray from $i$ to $k$ has an XOR sum of 0 if:

$$\text{Prefix\_XOR}[k] == \text{Prefix\_XOR}[i-1]$$

As we scan through the array from left to right using a pointer `k`, we will maintain a running `current_prefix_xor`. We need a look-back tool to see where this exact same XOR value has appeared in the past.

1. Keep a running `current_prefix_xor` variable starting at 0.
2. Maintain a history log (Hash Map). But instead of just tracking frequencies, we must know **the indices** where each prefix XOR occurred so we can compute the distance $k - i$.
3. At each element:

- Update the `current_prefix_xor`.
- Check if this exact XOR value exists in our history.
- If it does, it means we have found one or more historical starting indices $i-1$. For each matching past index, calculate the distance $(k - i)$ and add it to our global triplet count.
- Record the current index into the history log for this XOR value.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases:

- **The prefix XOR becomes 0 at index $k$**: This means the subarray starts exactly at index 0. We must seed our history log with an initial prefix XOR of 0 at a dummy index of `-1` so that the distance calculation `k - (-1)` evaluates properly to `k + 1`.
- **Elements are 0**: If the array contains an actual 0, the prefix XOR value won't change between two adjacent steps, creating a subarray of length 1 that XORs to 0. Our distance logic handles this seamlessly.

### Target Complexities:

- **Time Complexity**: $O(N)$ — We want to traverse the array in a single pass. By optimizing our map to store the running counts and index sums mathematically (or utilizing a list of past indices if $N$ is small), we aim for linear time.
- **Space Complexity**: $O(N)$ — To store the history of unique prefix XOR values.

---

## The C++ Implementation

Here is the clean, production-grade C++ solution using a single-pass optimized tracking map:

```cpp
#include <vector>
#include <unordered_map>

class Solution {
public:
    int countTriplets(std::vector<int>& arr) {
        // Map to store: [Prefix XOR -> Frequency of occurrence]
        std::unordered_map<int, int> xor_counts;
        // Map to store: [Prefix XOR -> Sum of indices where it appeared]
        std::unordered_map<int, int> xor_index_sums;

        // Base case: prefix XOR of 0 occurs before the array starts (index -1)
        xor_counts[0] = 1;
        xor_index_sums[0] = -1;

        int current_prefix_xor = 0;
        int total_triplets = 0;

        for (int k = 0; k < arr.size(); ++k) {
            current_prefix_xor ^= arr[k];

            // If this prefix XOR value has been seen before, we found zero-XOR subarrays
            if (xor_counts.find(current_prefix_xor) != xor_counts.end()) {
                int count = xor_counts[current_prefix_xor];
                int index_sum = xor_index_sums[current_prefix_xor];

                // Math breakdown: Total contribution = sum of (k - i_past - 1) for all past indices
                // which simplifies algebraically to: (count * k) - index_sum - count
                total_triplets += (count * k) - index_sum - count;
            }

            // Record the current state into our precomputed history maps
            xor_counts[current_prefix_xor]++;
            xor_index_sums[current_prefix_xor] += k;
        }

        return total_triplets;
    }
};

```
