## **LeetCode 303: Range Sum Query - Immutable**.

---

## 1. Core Pattern Recognition

You are given an array that will not change (it's _immutable_). Your job is to answer multiple queries asking for the sum of elements between two indices, `left` and `right` inclusive.

### How it fits "Precomputation"

- **The Clues:** You have a fixed data set, and you are asked to perform **repeated queries** over different ranges.
- **The Core Need:** If a process asks you to calculate something over and over again on static data, you should immediately think: _"Can I do heavy lifting once up front so that each future question can be answered instantly?"_ ---

## 2. The "Naive" Starting Point

The brute-force approach handles each query as it arrives.

- **The Approach:** Every time the `sumRange(left, right)` function is called, you start a loop at index `left` and add up every element until you reach `right`.
- **Why it's inefficient:** If the array has length $N$ and we receive $M$ queries, each query takes $O(N)$ time in the worst case. This results in a total time complexity of $O(M \times N)$. If $N$ and $M$ are both 10,000, your code will safely crash with a _Time Limit Exceeded_ (TLE) error.
- **The Redundancy:** Imagine calculating `sumRange(2, 5)` and then immediately being asked for `sumRange(2, 6)`. The brute-force approach calculates the sum from indices 2 to 5 all over again for the second query. You are repeatedly summing the exact same sub-segments.

---

## 3. The Intuition Shift

Instead of summing elements on demand, what if we precalculate the cumulative total from the very beginning of the array up to every possible index? This structure is called a **Prefix Sum Array**.

### Concrete Example: `nums = [3, -2, 3, 8, -1]`

Let's build a `prefix_sums` array where `prefix_sums[i]` stores the sum of all elements from index `0` up to `i-1`. (Adding an extra `0` at the beginning makes boundary math incredibly elegant).

- `prefix_sums[0] = 0` (Sum of zero elements)
- `prefix_sums[1] = 3`
- `prefix_sums[2] = 3 + (-2) = 1`
- `prefix_sums[3] = 1 + 3 = 4`
- `prefix_sums[4] = 4 + 8 = 12`
- `prefix_sums[5] = 12 + (-1) = 11`

Now, suppose we want to find the sum from index `2` to `4` (`[-2, 3, 8]`, which equals `9`).
Instead of looping, we can look at our precomputed totals:

- The sum of everything from index 0 to 4 is stored at `prefix_sums[5]` (`12`).
- But we don't want the elements _before_ index 2 (indices 0 and 1). The sum of those elements is stored at `prefix_sums[2]` (`3`).

If we subtract the unwanted prefix from the total prefix:

$$\text{Sum}(2 \text{ to } 4) = \text{prefix\_sums}[5] - \text{prefix\_sums}[2] = 12 - 3 = 9$$

By spending time up front to compute this array, **every single future range query can be solved using a single subtraction** in $O(1)$ time.

---

## 4. Conceptual Algorithm (No Code)

### Step 1: Constructor (Precomputation Phase)

- Create an array `prefix_sums` of size `N + 1`, initialized with zeros.
- Loop through the original array from left to right.
- Fill each slot: `prefix_sums[i + 1] = prefix_sums[i] + nums[i]`.

### Step 2: Query Phase (`sumRange(left, right)`)

- To get the sum from `left` to `right` inclusive, simply evaluate and return:
  `prefix_sums[right + 1] - prefix_sums[left]`

---

## 5. Edge Cases & Complexity

### Critical Edge Cases:

- **Querying from index 0 (e.g., `left = 0`):** By making the `prefix_sums` array size `N + 1` and starting with a dummy `0`, calculating `prefix_sums[right + 1] - prefix_sums[0]` works perfectly without throwing an out-of-bounds error.

### Target Complexities:

- **Time Complexity:** \* **Initialization:** $O(N)$ — to compute the prefix sum array once.
- **Query (`sumRange`):** $O(1)$ — a single subtraction per query.

- **Space Complexity:** $O(N)$ — to store the precomputed prefix sums.

---

## The C++ Implementation

Here is the clean, production-ready C++ class implementation:

```cpp
#include <vector>

class NumArray {
private:
    std::vector<int> prefix_sums;

public:
    NumArray(std::vector<int>& nums) {
        // Size is N + 1 to easily handle queries that start at index 0
        prefix_sums.assign(nums.size() + 1, 0);

        for (size_t i = 0; i < nums.size(); ++i) {
            prefix_sums[i + 1] = prefix_sums[i] + nums[i];
        }
    }

    int sumRange(int left, int right) {
        // O(1) retrieval using precomputed history
        return prefix_sums[right + 1] - prefix_sums[left];
    }
};

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray* obj = new NumArray(nums);
 * int param_1 = obj->sumRange(left,right);
 */

```

This problem perfectly captures why we precompute: we trade a bit of extra space ($O(N)$) to buy an incredible speedup for all future operations.

Let’s dissect **LeetCode 724: Find Pivot Index**.

This is another brilliant extension of the **Precomputation / Prefix Sum** pattern, but with a clever optimization tweak: you can solve it without allocating an entire extra array for your precomputed states.

---

## **Find Pivot Index (LC 724)**

## 1. Core Pattern Recognition

The problem asks you to find a "pivot index" where the sum of all numbers strictly to the left of that index equals the sum of all numbers strictly to the right of it.

### How it fits "Precomputation"

- **The Clue:** You need to know sub-segment totals (left side sum and right side sum) dynamically for every index you check.
- **The Core Need:** As you iterate through the array, testing each index as a potential pivot, you shouldn't re-sum the left and right elements manually. You need a way to know these sums instantly.

---

## 2. The "Naive" Starting Point

The brute-force way to look for the pivot index is to treat each element as a candidate one by one.

- **The Approach:** For every index `i`, you run a loop to the left (from `0` to `i-1`) to calculate the `left_sum`. Then, you run another loop to the right (from `i+1` to `N-1`) to calculate the `right_sum`. If `left_sum == right_sum`, you return `i`.
- **Why it's inefficient:** For an array of size $N$, you check $N$ indices. For each index, you look at almost all $N$ elements to calculate the sums. This results in an $O(N^2)$ time complexity.
- **The Redundancy:** When you move your candidate index from `i` to `i+1`, the new `left_sum` is just the old `left_sum` plus `nums[i]`. Re-summing from index 0 every time ignores the work you just did.

---

## 3. The Intuition Shift

Instead of computing the left and right sums independently from scratch, notice a mathematical relationship between the **Total Sum**, **Left Sum**, and the **Current Element**:

$$\text{Total Sum} = \text{Left Sum} + \text{nums}[i] + \text{Right Sum}$$

If we want to find a balance point where $\text{Left Sum} == \text{Right Sum}$, we can substitute $\text{Left Sum}$ into $\text{Right Sum}$'s spot:

$$\text{Total Sum} = \text{Left Sum} + \text{nums}[i] + \text{Left Sum}$$

$$\text{Total Sum} = 2 \times \text{Left Sum} + \text{nums}[i]$$

Rearranging this to solve for the condition we can check at any index:

$$\text{Right Sum} = \text{Total Sum} - \text{Left Sum} - \text{nums}[i]$$

### The "Aha!" Moment

If we precompute the **Total Sum** of the entire array once up front, we don't need to track a right sum at all! We can just maintain a running `left_sum` as we move from left to right. At any index `i`, the right sum is implicitly precomputed for us via subtraction.

---

## 4. Conceptual Algorithm (No Code)

1. **Precompute:** Calculate the `total_sum` of all elements in the array.
2. **Track:** Initialize a variable `left_sum = 0`.
3. **Loop:** Iterate through the array using index `i`:

- Calculate the implicit right sum: `right_sum = total_sum - left_sum - nums[i]`.
- **Check:** If `left_sum == right_sum`, you have found your leftmost pivot index. Return `i`.
- **Update:** If they aren't equal, add the current element to `left_sum` (`left_sum += nums[i]`) before moving to the next index.

4. If the loop ends without matching, return `-1`.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases:

- **Pivot at Index 0:** Elements to the left of index 0 don't exist, so `left_sum` is `0`. The formula handles this automatically.
- **Pivot at the Last Index:** Elements to the right don't exist, so `right_sum` should compute to `0`.
- **Negative Numbers:** The logic works exactly the same because sums can fluctuate up and down without breaking the algebraic relationship.

### Target Complexities:

- **Time Complexity:** $O(N)$ — We take one pass to find the total sum, and a second pass to find the pivot index. $O(2N) = O(N)$.
- **Space Complexity:** $O(1)$ — By utilizing the algebraic relationship, we only need two integer variables (`total_sum` and `left_sum`), optimizing our space perfectly.

---

## The C++ Implementation

Here is the clean, high-performance C++ solution:

```cpp
#include <vector>
#include <numeric>

class Solution {
public:
    int pivotIndex(std::vector<int>& nums) {
        // 1. Precompute the total sum of the array
        int total_sum = std::accumulate(nums.begin(), nums.end(), 0);
        int left_sum = 0;

        // 2. Linear scan to check for the pivot property
        for (int i = 0; i < nums.size(); ++i) {
            // Right sum is implicitly known via total_sum and left_sum
            int right_sum = total_sum - left_sum - nums[i];

            if (left_sum == right_sum) {
                return i; // Found the leftmost pivot index
            }

            // Update the precomputed history state for the next index
            left_sum += nums[i];
        }

        return -1;
    }
};

```

## **LeetCode 42: Trapping Rain Water**.

---

## 1. Core Pattern Recognition

You are given an array of heights representing an elevation map. You need to calculate the total units of water it can hold after a rainstorm.

### How it fits "Precomputation"

- **The Clue:** The water trapped over any individual block `i` cannot be determined by looking at block `i` alone. It forms a pool bounded by a "left boundary wall" and a "right boundary wall".
- **The Core Need:** To know the exact depth of the water at index `i`, you need to know the tallest bar anywhere to its left, and the tallest bar anywhere to its right. Instead of checking the whole array for every single index, you can **precompute two cumulative arrays**: a prefix max (tallest wall from the left) and a suffix max (tallest wall from the right).

---

## 2. The "Naive" Starting Point

The brute-force solution tries to compute the water trapped above each column independently.

- **The Approach:** You loop through each element `i` from `1` to `N-2`. For the current index `i`, you launch a loop to the left to find the max height (`left_max`), and another loop to the right to find the max height (`right_max`). The water height at `i` is determined by the shorter of the two bounding walls: `min(left_max, right_max) - height[i]`.
- **Why it's inefficient:** For every single index, you scan almost the entire array. This takes $O(N^2)$ time.
- **The Redundancy:** When you move from index `i` to `i+1`, the tallest wall to its left is highly likely the same or easily determined from the history you just scanned. Erasing that knowledge and re-scanning from index 0 every time is incredibly wasteful.

---

## 3. The Intuition Shift

Let’s change our perspective: **If we can scan the array once to precompute the tallest walls seen from both directions, we can solve every column's water retention in $O(1)$ time.**

### Concrete Example: `heights = [3, 0, 2, 0, 4]`

Let's precompute two helper arrays:

1. `max_left[i]`: Stores the tallest wall from index `0` up to `i`.

- Scanning left-to-right: `[3, 3, 3, 3, 4]`

2. `max_right[i]`: Stores the tallest wall from index `i` up to the end.

- Scanning right-to-left: `[4, 4, 4, 4, 4]`

Now, look at index 2 (where `height = 2`):

- Tallest wall to its left is `max_left[2] = 3`.
- Tallest wall to its right is `max_right[2] = 4`.
- The water container height over index 2 is capped by the shorter wall: `min(3, 4) = 3`.
- Subtract the height of the floor itself: `3 - height[2] = 3 - 2 = 1` unit of water.

By precomputing these bounding states, we completely untangle the problem.

---

## 4. Conceptual Algorithm (No Code)

1. **Precompute Left-to-Right:** Initialize an array `max_left` of size $N$. Walk from left to right, maintaining the highest bar seen so far, and record it at each position.
2. **Precompute Right-to-Left:** Initialize an array `max_right` of size $N$. Walk from right to left, maintaining the highest bar seen so far, and record it.
3. **Calculate Water:** Iterate through the array a third time. At every index `i`:

- Find the effective boundary height: `water_level = min(max_left[i], max_right[i])`.
- If `water_level > height[i]`, add `water_level - height[i]` to our total water counter.

4. Return the counter.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases:

- **Array size less than 3:** You need at least 3 columns to form a valley (left wall, floor, right wall). Arrays of size 0, 1, or 2 trap `0` water.
- **Monotonically Increasing/Decreasing maps (e.g., `[1, 2, 3, 4]`):** No valleys can form. The `min(max_left[i], max_right[i])` will always equal `height[i]`, correctly computing 0 water.

### Target Complexities:

- **Time Complexity:** $O(N)$ — We make three independent passes over the array (two for precomputation, one for evaluation).
- **Space Complexity:** $O(N)$ — We use two arrays to store the precomputed heights.

---

## The C++ Implementation

Here is the clean, readable C++ code implementing this prefix/suffix max precomputation:

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int trap(std::vector<int>& height) {
        int n = height.size();
        if (n < 3) return 0; // Edge case: not enough bars to hold water

        std::vector<int> max_left(n);
        std::vector<int> max_right(n);

        // 1. Precompute the highest wall seen from the left
        max_left[0] = height[0];
        for (int i = 1; i < n; ++i) {
            max_left[i] = std::max(max_left[i - 1], height[i]);
        }

        // 2. Precompute the highest wall seen from the right
        max_right[n - 1] = height[n - 1];
        for (int i = n - 2; i >= 0; --i) {
            max_right[i] = std::max(max_right[i + 1], height[i]);
        }

        // 3. Sum up the trapped water at each index
        int total_water = 0;
        for (int i = 0; i < n; ++i) {
            int water_level = std::min(max_left[i], max_right[i]);
            total_water += water_level - height[i];
        }

        return total_water;
    }
};

```

### Note on Optimization

Once you confidently grasp this $O(N)$ space precomputation approach, it provides the exact logical bridge to the $O(1)$ space **Two-Pointer** optimization, where you calculate `max_left` and `max_right` dynamically from both ends simultaneously!

to see a step-by-step trace of how the boundaries trap water across the index grid.
[Trapping Rain Water Full Visual Solution](https://www.youtube.com/watch?v=09KF1hjWoSU)

## **LeetCode 238: Product of Array Except Self**.

---

## 1. Core Pattern Recognition

You need to return an array `answer` where `answer[i]` is equal to the product of all the elements of `nums` except `nums[i]`.

### How it fits "Precomputation"

- **The Clue:** For any index `i`, the target value is composed of two independent parts: the product of everything to the **left** of `i`, and the product of everything to the **right** of `i`.
- **The Core Need:** Instead of recalculating the left side and right side products for every single position, we can precompute running products from both directions (Prefix Products and Suffix Products).

---

## 2. The "Naive" Starting Point

There are two naive approaches here:

- **Approach A (With Division):** Calculate the product of the entire array once, then loop through and divide the total product by `nums[i]` to get your answer.
- _Why it's disallowed:_ It fails completely if there are zeros in the array (division by zero), and the problem description explicitly bans it.

- **Approach B (Nested Loops):** For every index `i`, run a loop from scratch to multiply all other elements.
- _Why it's inefficient:_ It takes $O(N^2)$ time.
- _The Redundancy:_ To calculate the product except for index 3, you multiply indices 0, 1, and 2. To calculate the product except for index 4, you re-multiply indices 0, 1, 2, and 3. You are repeating the same multiplications over and over.

---

## 3. The Intuition Shift

Let’s visualize what `answer[i]` actually needs.

If `nums = [2, 3, 4, 5]`, let's look at index 2 (value `4`):

- Elements to the left: `[2, 3]` $\rightarrow$ Product = `6` (Prefix)
- Elements to the right: `[5]` $\rightarrow$ Product = `5` (Suffix)
- `answer[2]` is simply $\text{Prefix} \times \text{Suffix} = 6 \times 5 = 30$.

### The "Aha!" Moment

If we construct a prefix product array going left-to-right, and a suffix product array going right-to-left, the answer for any index `i` is just `prefix[i] * suffix[i]`. We can precompute both sweeps in linear time!

---

## 4. Conceptual Algorithm (No Code)

To save memory and achieve $O(1)$ auxiliary space (as requested by the follow-up of this problem), we can use our final `answer` array to hold the prefix products first, and then apply the suffix products on a second pass using a single tracking variable.

1. **Initialize:** Create the `answer` array of size $N$.
2. **Left-to-Right Pass (Prefix):** \* Start with a running product of `1`.

- As you loop from `0` to `N-1`, set `answer[i]` to the current running product (representing everything to the left of `i`).
- Update the running product by multiplying it by `nums[i]`.

3. **Right-to-Left Pass (Suffix):**

- Reset your running product variable to `1` (now representing everything to the right).
- Loop backward from `N-1` down to `0`.
- Multiply the existing value in `answer[i]` by this rightside running product.
- Update the rightside running product by multiplying it by `nums[i]`.

4. Return `answer`.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases:

- **Single Zero (e.g., `[1, 2, 0, 4]`):** The element at the zero index will have a non-zero product (from its left and right neighbors), while every other index will correctly compute to `0`. Our prefix/suffix accumulation handles this seamlessly without throwing division errors.
- **Multiple Zeros (e.g., `[1, 0, 3, 0]`):** Every single index will output `0` because there will always be at least one zero on either its left or right side.

### Target Complexities:

- **Time Complexity:** $O(N)$ — One forward pass and one backward pass. $O(2N) = O(N)$.
- **Space Complexity:** $O(1)$ auxiliary space — If we don't count the output array (as per LeetCode instructions), we only use a single integer variable to track the suffix product dynamically.

---

## The C++ Implementation

Here is the highly optimized, space-efficient C++ solution:

```cpp
#include <vector>

class Solution {
public:
    std::vector<int> productExceptSelf(std::vector<int>& nums) {
        int n = nums.size();
        std::vector<int> answer(n, 1);

        // 1. Pass 1: Precompute prefix products directly into the answer array
        int left_product = 1;
        for (int i = 0; i < n; ++i) {
            answer[i] = left_product;
            left_product *= nums[i];
        }

        // 2. Pass 2: Compute suffix products dynamically and combine them
        int right_product = 1;
        for (int i = n - 1; i >= 0; --i) {
            answer[i] *= right_product;
            right_product *= nums[i];
        }

        return answer;
    }
};

```
