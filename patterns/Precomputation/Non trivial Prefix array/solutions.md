---

https://youtu.be/CboKBIpo640?si=CmOiQIGsWOGyWB4k


## My Real Intuition for Next Permutation

Think of permutation like a number:

```text
d0 d1 d2 d3 ... dn-1
```

with place values:

```text
10^(n-1), 10^(n-2), 10^(n-3), ... , 10^0
```

So changing a digit on the left creates a bigger jump, and changing a digit on the right creates a smaller jump.

---

Suppose we have two indices:

```text
i < j
```

If we can create a greater permutation by changing either `i` or `j`, then choosing `j` gives less change because `j` has smaller place value.

Example:

```text
13542
```

Changing index `1`:

```text
13542 -> 14532
```

Changing index `3`:

```text
13542 -> 13552
```

The second change is smaller because it happens more to the right.

So for immediate next permutation:

```text
Try to fix/change as far right as possible.
```

This suggests starting from the rightmost side.

---

But a greater permutation only forms when we swap a digit with a bigger digit on its right.

Suppose we want to swap index `k` with some right-side index `j`:

```text
k < j
```

For the number to become greater:

```text
nums[k] < nums[j]
```

If instead:

```text
nums[k] >= nums[j]
```

then swapping will not create a greater permutation at position `k`.

So while traversing from right, we are basically trying to find the first place where:

```text
nums[i-1] < nums[i]
```

because this means:

```text
nums[i-1] can be increased using some digit on its right.
```

---

Now while moving from right to left, if we keep seeing:

```text
nums[i-1] >= nums[i]
```

then looking from right to left, the values are increasing:

```text
2 -> 4 -> 5
```

For example:

```text
1 3 5 4 2
      5 4 2
```

From right to left:

```text
2, 4, 5
```

This is increasing.

That means from left to right, the suffix is decreasing:

```text
5, 4, 2
```

A decreasing suffix is already the maximum possible arrangement.

So we do not need to fix it yet. We keep moving left.

---

When this increasing pattern from right to left breaks for the first time:

```text
2 -> 4 -> 5 -> 3
```

Break happens at:

```text
3
```

Array:

```text
1 3 5 4 2
  ^
pivot
```

This pivot is not magic.

It appears because:

```text
Everything to its right is already maximum,
and this is the first position from the right
that can still be increased.
```

---

Now we have two possible regions:

```text
left section  : [0 ... pivot-1]
right section : [pivot+1 ... n-1]
```

We choose from the right section because we want the immediate next permutation, meaning smallest possible jump.

So we swap pivot with the just greater digit from the right section.

Example:

```text
1 3 5 4 2
```

Pivot:

```text
3
```

Right section:

```text
5 4 2
```

Just greater than `3` is:

```text
4
```

Swap:

```text
1 4 5 3 2
```

---

Now the part I missed earlier:

After swapping, the prefix has already been minimally increased:

```text
1 4
```

Now to get the immediate next permutation, the remaining right section should be as small as possible.

Current suffix:

```text
5 3 2
```

Smallest form:

```text
2 3 5
```

Since this suffix is decreasing, we can simply reverse it.

Final:

```text
1 4 2 3 5
```

---

## Final Recall

```text
Start from right because right-side changes cause smaller jump.

Keep moving while nums[i-1] >= nums[i],
because that means the suffix is already maximum.

First break nums[i-1] < nums[i] gives pivot.

Swap pivot with just greater digit from right section.

Reverse right section to make it minimum.
```

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
        int i = n - 1;

        // Step 1: Find the first decreasing element (pivot) from the right
        while (i > 0 && nums[i-1] >= nums[i]) {
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
