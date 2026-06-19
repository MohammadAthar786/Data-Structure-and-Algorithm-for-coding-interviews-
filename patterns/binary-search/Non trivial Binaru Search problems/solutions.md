# LC 4 - Median of Two Sorted Arrays

TODO: Review final placement. Original category is Special Partition Search, not Binary Search on Answer in these notes.

LeetCode Link: https://leetcode.com/problems/median-of-two-sorted-arrays/
Pattern: Binary Search
Category: Binary Search on Partition
Difficulty: Hard
Status:

## 1. Problem Statement

Given two sorted arrays, find the median value of the combined sorted order without fully merging both arrays.

## 2. Pattern Recognition

| Item               | Notes                                                                                                   |
| :----------------- | :------------------------------------------------------------------------------------------------------ |
| Clues              | Two sorted arrays, median, required logarithmic time.                                                   |
| Category           | Binary Search on Partition                                                                              |
| Search Space       | Partition position in the smaller array                                                                 |
| Monotonic Property | If the left partition of array A is too large, move partition left; if too small, move partition right. |
| Invariant          | The correct partition keeps exactly half the elements on the left and satisfies `maxLeft <= minRight`.  |

## 3. Brute Force Approach

- Merge both arrays like merge sort.
- Find the middle element or average of two middle elements.

Why inefficient:

- Full merge takes `O(m + n)` time and extra space.
- Since both arrays are already sorted, we only need the correct partition, not the full merged array.

## 4. Intuition Shift / Aha Moment

### Intuition: Place One Divider Across Two Sorted Shelves

The median is determined only by the values touching the middle divider, not by the full merged order. Place cuts in both arrays so the combined left side contains exactly half the elements.

```text
A = [1, 3, 8]
B = [7, 9, 10, 11]
total = 7, left side must contain 4 values
```

Choosing `cutA` automatically determines `cutB = 4 - cutA`. We binary-search only `cutA`; the total left-side size stays correct by construction.

### Invariant

Every considered partition has exactly the required number of elements on the combined left. We search for the cut where both cross-boundary conditions hold:

```text
leftA <= rightB  and  leftB <= rightA
```

### Example Walkthrough

```text
cutA=1, cutB=3
A: [1 | 3,8]       leftA=1, rightA=3
B: [7,9,10 | 11]   leftB=10, rightB=11
```

`leftB > rightA`. Why move `cutA` right? The left side took too many large values from B and too few from A. Moving the A cut right raises `rightA` and simultaneously moves the B cut left, lowering `leftB`.

```text
cutA=2, cutB=2
A: [1,3 | 8]       leftA=3, rightA=8
B: [7,9 | 10,11]   leftB=9, rightB=10
```

Still `leftB > rightA`, so move A right again.

```text
cutA=3, cutB=1
A: [1,3,8 | ]      leftA=8, rightA=+infinity
B: [7 | 9,10,11]   leftB=7, rightB=9
```

Both cross conditions hold. For odd total length, median is the largest value on the left: `max(8,7)=8`.

### Edge Case / Correction

Binary-search the shorter array so `cutB` always stays representable and complexity is `O(log min(m,n))`. Use negative/positive infinity for empty partition sides rather than special-case comparisons.

### Final Recall

```text
Binary-search a cut in the shorter array.
The other cut supplies the remaining left-half elements.
leftA > rightB: took too much from A, move A cut left.
leftB > rightA: took too little from A, move A cut right.
Valid crossing: median comes from the boundary values.
```

## 5. Optimized Algorithm

Steps:

1. Always binary search the smaller array.
2. Let `partitionA` be the cut in array A.
3. Let `partitionB = halfSize - partitionA`.
4. Read boundary values:
   - `maxLeftA`
   - `minRightA`
   - `maxLeftB`
   - `minRightB`
5. If both left sides are `<=` both right sides, partition is valid.
6. If `maxLeftA > minRightB`, move partitionA left.
7. Else move partitionA right.

Pseudocode:

```text
if nums1 is larger:
    swap arrays

left = 0
right = size of smaller array
half = (m + n + 1) / 2

while left <= right:
    partitionA = midpoint
    partitionB = half - partitionA

    if valid partition:
        return median
    else if maxLeftA > minRightB:
        right = partitionA - 1
    else:
        left = partitionA + 1
```

## 6. Dry Run

Example:

```text
nums1 = [1, 3]
nums2 = [2]
```

Search smaller array:

```text
A = [2]
B = [1, 3]
total = 3
half = 2
```

| Step | left | right | partitionA | partitionB | Boundary Check                                                        | Movement                                |
| :--- | :--- | :---- | :--------- | :--------- | :-------------------------------------------------------------------- | :-------------------------------------- |
| 1    | 0    | 1     | 0          | 2          | `maxLeftB = 3 > minRightA = 2`                                        | A has too few left elements, move right |
| 2    | 1    | 1     | 1          | 1          | `maxLeftA = 2 <= minRightB = 3` and `maxLeftB = 1 <= minRightA = INF` | valid                                   |

Odd total length:

```text
median = max(maxLeftA, maxLeftB) = max(2, 1) = 2
```

## 7. Edge Cases

- One array is empty.
- Total length is odd.
- Total length is even.
- All elements of one array are smaller than the other.
- Duplicate values.
- Very different array sizes.
- Use sentinel values for empty partition sides.

## 8. Complexity

| Type  | Complexity          | Reason                                            |
| :---- | :------------------ | :------------------------------------------------ |
| Time  | `O(log(min(m, n)))` | Binary search only the smaller array's partition. |
| Space | `O(1)`              | No merged array is created.                       |

## 9. C++ Code

```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        if (nums1.size() > nums2.size()) {
            return findMedianSortedArrays(nums2, nums1);
        }

        int m = nums1.size();
        int n = nums2.size();
        int left = 0;
        int right = m;
        int halfSize = (m + n + 1) / 2;

        while (left <= right) {
            int partitionA = left + (right - left) / 2;
            int partitionB = halfSize - partitionA;

            int maxLeftA = (partitionA == 0) ? INT_MIN : nums1[partitionA - 1];
            int minRightA = (partitionA == m) ? INT_MAX : nums1[partitionA];

            int maxLeftB = (partitionB == 0) ? INT_MIN : nums2[partitionB - 1];
            int minRightB = (partitionB == n) ? INT_MAX : nums2[partitionB];

            if (maxLeftA <= minRightB && maxLeftB <= minRightA) {
                if ((m + n) % 2 == 1) {
                    return max(maxLeftA, maxLeftB);
                }

                return (max(maxLeftA, maxLeftB) + min(minRightA, minRightB)) / 2.0;
            }

            if (maxLeftA > minRightB) {
                right = partitionA - 1;
            } else {
                left = partitionA + 1;
            }
        }

        return 0.0;
    }
};
```

## 10. Interview One-Liner

The median is found by binary searching a valid partition where every value on the combined left half is less than or equal to every value on the combined right half.

## 11. Image / Visual Reference

TODO: Original note referenced missing image asset `Images/LC_4_Median_Of_Two_Sorted_Arrays.png`. Keep this placeholder until the source image is available.

```mermaid
flowchart TD
    A["Key observation: median is a valid partition"] --> B["Search space: partition in smaller array"]
    B --> C["Invariant: left side has half the total elements"]
    C --> D["Compute partitionA and partitionB"]
    D --> E{"maxLeftA <= minRightB and maxLeftB <= minRightA?"}
    E -- "yes" --> F["valid partition: compute median"]
    E -- "no" --> G{"maxLeftA > minRightB?"}
    G -- "yes" --> H["partitionA too far right: move left"]
    G -- "no" --> I["partitionA too far left: move right"]
    H --> C
    I --> C
```
