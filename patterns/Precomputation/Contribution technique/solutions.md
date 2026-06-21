## **Sum of All Subarrays Sum**

---

## 1. Core Pattern Recognition

To spot a **Contribution Technique** problem, you are looking for a very specific phrasing constraint:

- **The Setup:** You are given an array of numbers.
- **The Clue:** The problem asks you to compute an aggregate metric (like a total sum) across **every single possible subarray** (or subset) of the array.
- **The Scale:** The size of the array $N$ is usually quite large (e.g., $N \ge 10^5$), meaning anything slower than $O(N)$ or $O(N \log N)$ will completely time out.

> **The Giveaway:** Instead of grouping the problem by **subarrays** (i.e., _“Let’s look at Subarray A, then Subarray B, then Subarray C...”_), you shift your perspective to the **individual elements** (i.e., _“How many total times does this specific element `arr[i]` appear across ALL possible subarrays?”_).

---

## 2. The "Naive" Starting Point

### The Brute-Force Approach

The most literal way to solve this is to generate every single valid subarray. A subarray is defined by its starting index `L` and its ending index `R`.

```cpp
long long total_sum = 0;
for (int L = 0; L < n; ++L) {
    for (int R = L; R < n; ++R) {
        // Calculate the sum from L to R
        for (int i = L; i <= R; ++i) {
            total_sum += arr[i];
        }
    }
}

```

_(Even if you optimize this with a running prefix sum to drop the third loop, you are still bound by a nested `L` and `R` dual-loop framework)._

### Why it's Inefficient

An array of size $N$ has exactly $\frac{N(N+1)}{2}$ subarrays. A nested-loop approach forces you to touch elements over and over again to build these individual ranges, resulting in a time complexity of $O(N^2)$. If $N = 10^5$, $O(N^2)$ equates to $10^{10}$ operations, which triggers a certain Time Limit Exceeded (TLE).

---

## 3. The Intuition Shift

Here is the **"Aha!" moment**: Stop looking at subarrays as whole entities. Instead, look at a single element at index `i` and count its **frequency of contribution**.

If an element `arr[i]` appears in $X$ different subarrays, then its total contribution to the global final sum is simply:

$$\text{Contribution} = \text{arr}[i] \times X$$

### A Concrete Example

Let's look at an array: `[A, B, C]` at indices `0, 1, 2`.
Let's isolate element **`B`** (at index $1$).

For a subarray to contain `B`, where can its starting point `L` be?

- `L` can be at index $0$ (matching element `A`)
- `L` can be at index $1$ (matching element `B` itself)
- Total choice of left boundaries = **2** (`L ∈ {0, 1}`)

Where can its ending point `R` be?

- `R` can be at index $1$ (matching element `B`)
- `R` can be at index $2$ (matching element `C`)
- Total choice of right boundaries = **2** (`R ∈ {1, 2}`)

By the fundamental counting principle of combinatorics, any choice of these left boundaries paired with any choice of these right boundaries forms a valid unique subarray containing `B`.

$$\text{Total Subarrays containing B} = (\text{Left choices}) \times (\text{Right choices}) = 2 \times 2 = 4$$

_(The 4 subarrays are: `[B]`, `[A, B]`, `[B, C]`, and `[A, B, C]`)._

### The Universal Formula for Index `i`

For any index `i` in an array of length `N`:

- Number of valid starting positions to its left (inclusive): `i + 1`
- Number of valid ending positions to its right (inclusive): `N - i`

$$\text{Total Subarrays containing arr}[i] = (i + 1) \times (N - i)$$

---

## 4. Conceptual Algorithm (No Code)

The optimized algorithm eliminates the concept of subarrays completely during iteration:

1. **Initialize:** Create a variable `total_sum = 0`.
2. **Single Pass Scan:** Loop through the array from index `i = 0` to `N - 1`.
3. **Calculate Choice Bounds:**

- Count how many elements are to the left of `i` plus itself: `left_choices = i + 1`.
- Count how many elements are to the right of `i` plus itself: `right_choices = N - i`.

4. **Multiply & Accumulate:**

- Multiply `left_choices * right_choices` to get the total frequency.
- Multiply that frequency by the value `arr[i]`.
- Add this total product straight to `total_sum`.

5. **Return:** Once the loop finishes, `total_sum` holds the exact answer.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases

- **Integer Overflow:** This is the most notorious trap in contribution problems. Even if `arr[i]` fits in a standard 32-bit integer, the product of `(i + 1) * (N - i) * arr[i]` can effortlessly exceed $2 \times 10^9$. You **must** cast the boundary counts to a 64-bit integer (`long long` in C++) before multiplying.
- **Single Element Array ($N=1$):** Handled cleanly. Formula gives $(0+1) \times (1-0) = 1 \times 1 = 1$ contribution.

### Complexity Targets

- **Time Complexity:** $O(N)$ — We visit each element exactly once in a single flat loop.
- **Space Complexity:** $O(1)$ auxiliary space — We only need a couple of local scalar tracking variables.

---

## C++ Implementation

Here is the clean, type-safe C++ implementation utilizing the contribution technique:

```cpp
#include <vector>

class Solution {
public:
    long long subarraySum(std::vector<int>& arr) {
        int n = arr.size();
        long long total_sum = 0;

        for (int i = 0; i < n; ++i) {
            // Count left and right boundary choices
            // Cast to long long early to prevent interim product overflow
            long long left_choices = i + 1;
            long long right_choices = n - i;

            // Total times arr[i] appears across all possible subarrays
            long long frequency = left_choices * right_choices;

            // Accumulate the direct value contribution of this specific element
            total_sum += frequency * arr[i];
        }

        return total_sum;
    }
};

```

## **LeetCode 907: Sum of Subarray Minimums**

https://youtu.be/HRQB7-D2bi0?si=9oXPz2N72wcdPt2K

---

## 1. Core Pattern Recognition

To spot why this is a Contribution Technique problem, let's look at the phrasing:

- **The Clue:** Find the sum of `min(b)` for **every possible subarray** `b`.
- **The Scale:** The array length $N$ goes up to $3 \times 10^4$. A nested loop to find all subarrays will trigger a Time Limit Exceeded (TLE) error.

> **The Giveaway:** Just like the simpler problem, instead of asking _"What is the minimum of this specific subarray?"_, we invert the question and ask: **"For this specific element `arr[i]`, in how many valid subarrays does it act as the absolute minimum?"**

---

## 2. The "Naive" Starting Point

### The Brute-Force Approach

You generate every single subarray using two pointers `L` and `R`. As you expand `R`, you maintain a running minimum of the elements seen so far and add that minimum to your total answer.

### Why it's Inefficient

To check every subarray, you are forced to run nested loops, which takes $O(N^2)$ time.

### Where is the Redundant Work?

Imagine an array where `1` is the absolute smallest element, located right in the middle. The brute-force approach will inspect thousands of different subarrays containing that `1`, manually scanning them or incrementally tracking them, completely blind to the fact that as long as `1` is in the subarray and no smaller element exists, the answer for that entire range is _always_ going to be `1`.

---

## 3. The Intuition Shift

For a given element `arr[i]` to be the minimum of a subarray, how far can that subarray expand to the left and to the right?

- It can expand to the left until it hits an element **strictly smaller** than `arr[i]`.
- It can expand to the right until it hits an element **strictly smaller** than `arr[i]`.

Let's find the boundaries:

- Let `left_boundary` be the distance to the **Previous Smaller Element (PSE)**.
- Let `right_boundary` be the distance to the **Next Smaller Element (NSE)**.

### A Concrete Example

Take the array: `[3, 1, 2, 4]` and isolate element **`2`** (at index 2).

- **Look Left:** The first element smaller than `2` is `1` (at index 1). So `2` can only extend left to include itself. Number of left choices = `1` (just index 2).
- **Look Right:** There are no elements smaller than `2` to its right. It can extend all the way to the end of the array. It can form subarrays ending at index 2 (`4` itself) or index 3 (`4`). Number of right choices = `2` (indices 2 and 3).

Total subarrays where `2` is the minimum = $\text{Left Choices} \times \text{Right Choices} = 1 \times 2 = 2$.
_(The subarrays are `[2]` and `[2, 4]`. In both, `2` is the minimum!)_

### The Universal Formula for LC 907

If an element at index `i` has a distance of `L` to its previous smaller element and `R` to its next smaller element:

$$\text{Total Subarrays where arr[i] is minimum} = L \times R$$

$$\text{Contribution of arr[i]} = arr[i] \times L \times R$$

To find these distances `L` and `R` for _every_ element efficiently in $O(1)$ time, we use a **Monotonic Stack**.

---

## 4. Conceptual Algorithm (No Code)

1. **Find Left Distances (PSE):** Use a monotonically increasing stack to find the index of the closest smaller element to the left for every element. Store the distance `i - pse_index` in a `left` array.
2. **Find Right Distances (NSE):** Use a similar monotonic stack pass from right to left (or left to right tracking boundaries) to find the index of the closest smaller element to the right. Store the distance `nse_index - i` in a `right` array.
3. **Handle Duplicates (Crucial Detail):** If the array has duplicate numbers (e.g., `[2, 1, 2, 1]`), both `1`s might claim the same subarray, leading to double-counting. To fix this, look for _strictly smaller_ on the left, but _smaller or equal_ on the right. This cleanly breaks the tie.
4. **Calculate Global Sum:** Iterate through the array one last time. For each index `i`, calculate `(left[i] * right[i] * arr[i])` and accumulate it into your total, modulo $10^9 + 7$.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases

- **No Smaller Element Exists:** If there is no smaller element to the left, the boundary naturally defaults to the start of the array (`i + 1`). If none to the right, it defaults to the end of the array (`n - i`).
- **Modulo Arithmetic:** The answer can be huge. You must apply `% 1000000007` at each addition step to prevent integer overflow.

### Complexity Targets

- **Time Complexity:** $O(N)$ — Each element is pushed and popped from the monotonic stack at most once.
- **Space Complexity:** $O(N)$ to store the stack and boundary tracking vectors.

---

## C++ Implementation

Here is the clean, highly-optimized single-pass style using a monotonic stack to compute the contributions efficiently:

```cpp
#include <vector>
#include <stack>

class Solution {
public:
    int sumSubarrayMins(std::vector<int>& arr) {
        int n = arr.size();
        const int MOD = 1e9 + 7;

        // Arrays to store distances to Previous Smaller Element (PSE) and Next Smaller Element (NSE)
        std::vector<long long> left(n), right(n);

        // Stack stores pairs of {element_value, count_of_elements_it_dominates}
        std::stack<std::pair<int, int>> s_left, s_right;

        // Step 1: Calculate Left distances (PSE)
        for (int i = 0; i < n; ++i) {
            int count = 1; // Current element itself
            while (!s_left.empty() && s_left.top().first > arr[i]) {
                count += s_left.top().second;
                s_left.pop();
            }
            s_left.push({arr[i], count});
            left[i] = count;
        }

        // Step 2: Calculate Right distances (NSE)
        // Notice the '>=' to handle duplicate values and avoid double counting
        for (int i = n - 1; i >= 0; --i) {
            int count = 1;
            while (!s_right.empty() && s_right.top().first >= arr[i]) {
                count += s_right.top().second;
                s_right.pop();
            }
            s_right.push({arr[i], count});
            right[i] = count;
        }

        // Step 3: Compute total contribution sum
        long long total_sum = 0;
        for (int i = 0; i < n; ++i) {
            long long total_subarrays = (left[i] * right[i]) % MOD;
            long long element_contribution = (total_subarrays * arr[i]) % MOD;
            total_sum = (total_sum + element_contribution) % MOD;
        }

        return total_sum;
    }
};

```

**LeetCode 2104: Sum of Subarray Ranges**

https://youtu.be/gIrMptNPf5M?si=l40A2fu7_LdsBiRL

---

## 1. Core Pattern Recognition

Let's look at the mathematical definition of a subarray range:

$$\text{Range}(b) = \max(b) - \min(b)$$

If we want the sum of all ranges across all subarrays, we can rewrite the total sum using the distributive property of addition:

$$\sum \text{Range}(b) = \sum (\max(b) - \min(b)) = \sum \max(b) - \sum \min(b)$$

> **The Giveaway:** The problem can be split into **two entirely independent sub-problems**:
>
> 1. Find the sum of all subarray **maximums** ($\sum \max(b)$).
> 2. Find the sum of all subarray **minimums** ($\sum \min(b)$).
>
> You already know how to solve the minimums part from LC 907! To solve this problem, you just apply the exact same contribution/monotonic stack technique twice.

---

## 2. The "Naive" Starting Point

### The Brute-Force Approach

Just like before, you can use a nested loop structure to generate every single subarray `[L, R]`. As you expand `R`, you maintain a running `current_max` and `current_min`, compute `current_max - current_min`, and add that difference to your global sum.

### Why it's Inefficient

With $N$ elements, there are $O(N^2)$ subarrays. Running this takes $O(N^2)$ time. While $N \le 1000$ for this specific LeetCode problem (meaning brute force _could_ pass), an interviewer will immediately ask you to optimize it to $O(N)$ to see if you understand the underlying linear pattern.

---

## 3. The Intuition Shift

Instead of looking at subarrays, we isolate each element `arr[i]` and look at it through two distinct lenses:

1. **Lens 1 (As a Minimum):** In how many subarrays does `arr[i]` act as the absolute minimum?

- $\text{Contribution}_{\min} = \text{arr}[i] \times \text{left}_{\min}[i] \times \text{right}_{\min}[i]$

2. **Lens 2 (As a Maximum):** In how many subarrays does `arr[i]` act as the absolute maximum?

- $\text{Contribution}_{\max} = \text{arr}[i] \times \text{left}_{\max}[i] \times \text{right}_{\max}[i]$

### Finding the Maximum Boundaries

For an element to be the absolute _maximum_ of a subarray, it can expand left and right until it hits an element **strictly greater** than itself.

- **Look Left:** Find the distance to the Previous Greater Element (PGE).
- **Look Right:** Find the distance to the Next Greater Element (NGE).

By running a monotonic _decreasing_ stack, we can capture these maximum boundaries just as cleanly as we captured the minimum boundaries.

---

## 4. Conceptual Algorithm (No Code)

1. **Calculate Minimum Contributions:** Run the LC 907 algorithm using a monotonically increasing stack to find the total contribution of each element acting as a minimum.
2. **Calculate Maximum Contributions:** Run a parallel pass using a monotonically decreasing stack. For each element, find the distance to its previous greater element and next greater element. Multiply these distances by `arr[i]` to get its contribution as a maximum.
3. **Handle Duplicates:** Just like in the minimums problem, prevent double-counting of duplicate elements by using strict inequality (`>`) on one side and non-strict inequality (`>=`) on the other side for both your minimum and maximum boundary scans.
4. **Final Math:** Total Sum = (Sum of all Maximum Contributions) $-$ (Sum of all Minimum Contributions).

---

## 5. Edge Cases & Complexity

### Critical Edge Cases

- **No Modulo Required:** Unlike LC 907, LC 2104 does _not_ ask for the result modulo $10^9+7$. Because the values are allowed to grow naturally, your accumulation variables **must** be stored as 64-bit integers (`long long` in C++) to prevent integer overflow.

### Complexity Targets

- **Time Complexity:** $O(N)$ — We perform 4 linear stack passes (or combine them into single structural sweeps), meaning every element is processed in constant amortized time.
- **Space Complexity:** $O(N)$ to store our boundary distance arrays and stack tracking structures.

---

## C++ Implementation

Here is an elegant, easy-to-follow implementation that cleanly separates the Minimum and Maximum calculations so you can visualize the symmetry of the contribution technique:

```cpp
#include <vector>
#include <stack>

class Solution {
public:
    long long subArrayRanges(std::vector<int>& nums) {
        int n = nums.size();

        // Vectors to store boundary distances
        std::vector<long long> left_min(n), right_min(n);
        std::vector<long long> left_max(n), right_max(n);

        // Stacks store pairs of {value, count}
        std::stack<std::pair<int, int>> s_left, s_right;

        // --- STEP 1: CALCULATE MINIMUM CONTRIBUTIONS (Same as LC 907) ---
        for (int i = 0; i < n; ++i) {
            int count = 1;
            while (!s_left.empty() && s_left.top().first > nums[i]) {
                count += s_left.top().second;
                s_left.pop();
            }
            s_left.push({nums[i], count});
            left_min[i] = count;
        }
        while (!s_left.empty()) s_left.pop(); // Clear stack for reuse

        for (int i = n - 1; i >= 0; i--) {
            int count = 1;
            while (!s_right.empty() && s_right.top().first >= nums[i]) {
                count += s_right.top().second;
                s_right.pop();
            }
            s_right.push({nums[i], count});
            right_min[i] = count;
        }
        while (!s_right.empty()) s_right.pop(); // Clear stack for reuse

        // --- STEP 2: CALCULATE MAXIMUM CONTRIBUTIONS (Symmetric Logic) ---
        // Look for Previous Greater Element (PGE)
        for (int i = 0; i < n; ++i) {
            int count = 1;
            while (!s_left.empty() && s_left.top().first < nums[i]) {
                count += s_left.top().second;
                s_left.pop();
            }
            s_left.push({nums[i], count});
            left_max[i] = count;
        }

        // Look for Next Greater Element (NGE)
        for (int i = n - 1; i >= 0; i--) {
            int count = 1;
            while (!s_right.empty() && s_right.top().first <= nums[i]) {
                count += s_right.top().second;
                s_right.pop();
            }
            s_right.push({nums[i], count});
            right_max[i] = count;
        }

        // --- STEP 3: ACCUMULATE THE FINAL DIFFERENCE ---
        long long sum_max = 0;
        long long sum_min = 0;

        for (int i = 0; i < n; ++i) {
            sum_max += (left_max[i] * right_max[i]) * nums[i];
            sum_min += (left_min[i] * right_min[i]) * nums[i];
        }

        return sum_max - sum_min;
    }
};

```
