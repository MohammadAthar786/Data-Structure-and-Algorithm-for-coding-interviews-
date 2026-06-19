## **Next Permutation**.

---

> **The Giveaway:** Lexicographical order means dictionary order. If the sequence was `[1, 2, 3]`, the permutations ordered like numbers would be $123 \rightarrow 132 \rightarrow 213 \rightarrow 231 \rightarrow 312 \rightarrow 321$. The pattern asks you to move exactly one step forward along this predictable path.

---

## 2. The "Naive" Starting Point

### The Brute-Force Approach

The literal way to solve this is to find _all possible permutations_ of the given array, sort them in lexicographical order, locate our input array in that sorted list, and then grab the array that sits exactly one index ahead of it.

### Why it's Inefficient

An array of size $N$ has exactly $N!$ (factorial) permutations. Generating and sorting all permutations requires **$O(N! \times N)$** time. If $N = 20$, $20!$ is roughly $2.4 \times 10^{18}$ operations—which would take a modern supercomputer years to execute.

### Where is the Redundant Work?

The brute-force approach ignores the state of the current array entirely. It throws away the valuable structural information already present in our input and reconstructs the entire universe of permutations from scratch just to find a single adjacent neighbor.

---

## 3. The Intuition Shift



### The "Aha!" Moment

Therefore, to find where we can make a change, we must scan from **right to left** looking for the very first spot where the numbers _stop increasing_ as we go backward. This is the first place where a number drops.

### A Concrete Example

Let’s look at the sequence: `[1, 3, 5, 4, 2]`

1. **Scan from right to left:** \* `2` $\rightarrow$ `4` (increasing)

- `4` $\rightarrow$ `5` (increasing)
- `5` $\rightarrow$ `3` (**Break!** `3` is less than `5`).
- We call `3` our **pivot** (index $1$). The suffix `[5, 4, 2]` is entirely decreasing and cannot be made larger.

2. **Find the replacement:** We need to replace our pivot `3` with the next largest number available in that right-hand suffix to make the overall number slightly bigger. Scanning from right to left again, the first number greater than `3` is `4`.

- Swap `3` and `4`.
- The array becomes: `[1, 4, 5, 3, 2]`

3. **Minimize the tail:** The suffix `[5, 3, 2]` is still in descending order. Since we just increased our pivot position from 3 to 4, we want the rest of the number to be as _small_ as possible. The smallest way to write a sequence is in **ascending order**.

- To turn a descending sequence into an ascending one, we don't need to sort it; we just **reverse** it!
- Reversing `[5, 3, 2]` gives `[2, 3, 5]`.

- Final Answer: `[1, 4, 2, 3, 5]`

## ![alt text](<Next permutation-2.png>)

## 4. Conceptual Algorithm (No Code)

Here is how the variables and pointers move over the structure:

1. **Find the Pivot:** Initialize a pointer `i` at the second-to-last element ($N-2$). Move `i` leftward as long as `arr[i] >= arr[i+1]`. Stop when you find the first element that violates this rule. This element at `i` is your **pivot**.
2. **Find the Swap Target:** If a valid pivot is found, create a second pointer `j` starting at the very last element ($N-1$). Move `j` leftward until you hit the first element where `arr[j] > arr[i]`.
3. **Swap:** Swap the values at `arr[i]` and `arr[j]`.
4. **Reverse the Suffix:** Whether a pivot was found or not, the entire subarray to the right of index `i` (from `i+1` to $N-1$) is guaranteed to be strictly decreasing. Reverse this sub-segment in place to reset it to its lowest possible lexicographical form.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases

- **Entire Array is Decreasing (e.g., `[3, 2, 1]`):** Our step 1 pointer `i` will fall off the left edge of the array (becoming `-1`). The algorithm skips the swap steps and goes straight to reversing the entire array, cleanly resulting in `[1, 2, 3]`, which matches the problem's criteria to wrap back around to the smallest sequence.
- **Duplicate Values:** Using strict boundaries (`>=` while searching for the pivot) ensures that duplicate configurations don't lock the pointers into infinite loops.

### Complexity Targets

- **Time Complexity:** $O(N)$ — In the worst case, we do two linear scans backwards and one reverse pass. Each element is looked at a constant number of times.
- **Space Complexity:** $O(1)$ auxiliary space because all mutations and swaps are executed strictly in-place.

---

## C++ Implementation

Here is the straightforward, highly performant C++ implementation mapping out this structural walkthrough:

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    void nextPermutation(std::vector<int>& nums) {
        int n = nums.size();
        int i = n - 2;

        // Step 1: Find the first decreasing element (pivot) from the right
        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i--;
        }

        // Step 2: If the pivot is valid, find its next larger companion swap target
        if (i >= 0) {
            int j = n - 1;
            while (nums[j] <= nums[i]) {
                j--;
            }
            // Step 3: Swap them
            std::swap(nums[i], nums[j]);
        }

        // Step 4: Reverse the suffix to make it as small as possible
        std::reverse(nums.begin() + i + 1, nums.end());
    }
};

```

https://youtu.be/CboKBIpo640?si=HUnAvDTm7Ojn8HF2
