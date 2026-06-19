## **LeetCode 304: Range Sum Query 2D - Immutable**

---

## 1. Core Pattern Recognition

To identify a **2D Prefix Sum** problem, look for these specific clues:

- **The Setup:** You are given a static, immutable 2D grid matrix of numbers.
- **The Clue:** The problem asks you to calculate the sum of elements inside a bounding box defined by a submatrix: `(row1, col1)` to `(row2, col2)`.
- **The Frequency:** The method `sumRegion` will be called **frequently (thousands of times)** on the same grid.

> **The Giveaway:** The combination of an **immutable grid** and **repeated region queries** screams that you need to spend time preprocessing the grid once so that subsequent queries are extremely fast.

---

## 2. The "Naive" Starting Point

### The Brute-Force Approach

Every time `sumRegion(row1, col1, row2, col2)` is called, you write a nested `for` loop:

```cpp
int sum = 0;
for (int r = row1; r <= row2; ++r) {
    for (int c = col1; c <= col2; ++c) {
        sum += matrix[r][c];
    }
}

```

### Why it's Inefficient

If the matrix has a size of $M \times N$, a single query could potentially ask for the sum of the entire matrix. This takes $O(M \times N)$ time _per query_. If you have $Q$ queries, the overall time complexity becomes $O(Q \times M \times N)$, which will trigger a Time Limit Exceeded (TLE) exception.

### Where is the Redundant Work?

Imagine querying `sumRegion(0, 0, 5, 5)` and right after, querying `sumRegion(0, 0, 5, 6)`. The second query completely recalculates the first $6 \times 6$ block all over again just to add one extra column. You are continuously looping over the exact same sub-grids.

---

## 3. The Intuition Shift

In 1D arrays, the prefix sum at index $i$ stores the sum from index $0$ to $i$.
In 2D arrays, **the prefix sum at position `(r, c)` stores the sum of the entire rectangle spanning from the top-left corner `(0, 0)` down to `(r, c)`.**

Here is the **"Aha!" geometric visualization moment**:
If you want to find the sum of a specific submatrix box in the middle of the grid, you don't build it up; you take a larger rectangle starting from `(0, 0)` and **cut away the sections you don't need**.

Look at the geometry of how to extract a target submatrix:

1. Start with the massive rectangle from `(0, 0)` to `(row2, col2)`.
2. Subtract the big block directly above it: from `(0, 0)` to `(row1 - 1, col2)`.
3. Subtract the big block directly to its left: from `(0, 0)` to `(row2, col1 - 1)`.
4. _Wait!_ By subtracting both the top and left blocks, you have subtracted the top-left overlapping corner twice!
5. Add back the top-left corner: from `(0, 0)` to `(row1 - 1, col1 - 1)`.

This is the mathematical **Inclusion-Exclusion Principle**. It means any bounding box sum can be found by adding/subtracting exactly **4 precalculated values**, no matter how huge the box is!

---

## 4. Conceptual Algorithm (No Code)

### Phase 1: Preprocessing (Building the Prefix Matrix)

We construct a new matrix called `prefix` where `prefix[r][c]` represents the sum of the matrix from `(0, 0)` to `(r, c)`. To build it without brute force, we use a similar geometric rule row-by-row:

- To compute `prefix[r][c]`, look at the current value in the raw matrix.
- Add the prefix sum directly above it.
- Add the prefix sum directly to its left.
- Subtract the diagonal top-left element (because it was counted twice).

_Tip:_ To avoid annoying boundary checks (like `r - 1` going out of bounds at row 0), we pad our `prefix` matrix with an extra row and column of zeros at the top and left.

### Phase 2: Querying

When `sumRegion(row1, col1, row2, col2)` is called, shift the coordinates to match your padded matrix, and return:

$$\text{Result} = \text{BottomRight} - \text{Top} - \text{Left} + \text{TopLeft}$$

---

## 5. Edge Cases & Complexity

### Critical Edge Cases

- **Querying the Top Row (`row1 == 0`) or Left Column (`col1 == 0`):** By padding the prefix matrix with an extra row and column of zeros (making its size $(M+1) \times (N+1)$), index out-of-bounds errors naturally disappear. The subtraction blocks safely evaluate to `0`.

### Complexity Targets

- **Time Complexity:** \* **Initialization:** $O(M \times N)$ to compute the prefix matrix once.
- **Each Query:** $O(1)$ constant time because it's just 4 matrix lookups and basic math.

- **Space Complexity:** $O(M \times N)$ auxiliary space to store the prefix sum matrix.

---

## C++ Implementation

Here is the clean, 1-indexed padded matrix approach which is standard for avoiding annoying edge-case branch conditions:

```cpp
#include <vector>

class NumMatrix {
private:
    std::vector<std::vector<int>> prefix;

public:
    NumMatrix(std::vector<std::vector<int>>& matrix) {
        if (matrix.empty() || matrix[0].empty()) return;

        int rows = matrix.size();
        int cols = matrix[0].size();

        // Step 1: Create a padded matrix of size (rows + 1) x (cols + 1) filled with 0
        prefix.assign(rows + 1, std::vector<int>(cols + 1, 0));

        // Step 2: Build the 2D prefix sums using geometric accumulation
        for (int r = 1; r <= rows; ++r) {
            for (int c = 1; c <= cols; ++c) {
                prefix[r][c] = matrix[r - 1][c - 1]
                             + prefix[r - 1][c]
                             + prefix[r][c - 1]
                             - prefix[r - 1][c - 1];
            }
        }
    }

    int sumRegion(int row1, int col1, int row2, int col2) {
        // Step 3: Use the Inclusion-Exclusion Principle to calculate the block in O(1).
        // We shift inputs by +1 because our prefix matrix is 1-indexed (padded)
        int r1 = row1 + 1, c1 = col1 + 1;
        int r2 = row2 + 1, c2 = col2 + 1;

        return prefix[r2][c2]
             - prefix[r1 - 1][c2]
             - prefix[r2][c1 - 1]
             + prefix[r1 - 1][c1 - 1];
    }
};

```

## **LeetCode 1314: Matrix Block Sum**

---

## 1. Core Pattern Recognition

To spot why this problem is a 2D Prefix Sum application, notice these core clues:

- **The Operation:** For every cell `(i, j)`, you need to calculate the sum of a neighborhood block bounding box extending $K$ units up, down, left, and right.
- **The Constraints:** You have to output an entirely new matrix where _every single element_ is the sum of its respective localized block.
- **The Trigger:** This implies you will be querying the sum of a submatrix rectangle $M \times N$ times (once for each cell).

> **The Giveaway:** Performing $M \times N$ rectangle sum lookups on a fixed grid means calculating independent region sums repeatedly. Precomputing the 2D prefix sum table will instantly drop each cell's block calculation down to $O(1)$.

---

## 2. The "Naive" Starting Point

### The Brute-Force Approach

For every single cell `(i, j)` in your matrix, you calculate its boundaries:

- Rows go from `i - K` to `i + K`
- Columns go from `j - K` to `j + K`

Then, you run a nested `for` loop to scan every cell inside that calculated square bounding box and add them up.

### Why it's Inefficient

If $K$ is large, each bounding box could potentially cover almost the entire grid. If you have an $M \times N$ matrix, calculating the sum for _one_ cell takes $O(M \times N)$ work. Repeating this for _every single cell_ yields a massive time complexity of **$O((M \times N)^2)$** or $O(M^2 \times N^2)$, which will easily result in a Time Limit Exceeded error.

---

## 3. The Intuition Shift

The "Aha!" moment comes from realizing that **a localized bounding box centered around `(i, j)` is just a regular submatrix query.**

Instead of recalculating the overlap manually for each cell, you can reframe every cell's $K$-neighborhood as a strict submatrix problem defined by a **Top-Left coordinate `(r1, c1)**`and a **Bottom-Right coordinate`(r2, c2)\*\*`.

To handle the edges of the grid safely (where a distance of $K$ might push you outside the actual physical boundaries of the matrix), you can use simple clamping via `std::max` and `std::min`:

- **`r1 = max(0, i - K)`** , **`c1 = max(0, j - K)`**
- **`r2 = min(M - 1, i + K)`** , **`c2 = min(N - 1, j + K)`**

Once you have these exact boundary coordinates clamped inside the grid, you don't iterate! You pass `(r1, c1)` and `(r2, c2)` right into your 4-point Inclusion-Exclusion formula from LC 304 to instantly get the block sum.

---

## 4. Conceptual Algorithm (No Code)

1. **Precompute:** Create an $(M+1) \times (N+1)$ padded 2D prefix sum array (`prefix`) based on the input matrix `mat`.
2. **Initialize Output:** Create a resulting `ans` matrix of size $M \times N$.
3. **Iterate & Compute:** Loop through every cell `(i, j)` from top to bottom, left to right:

- Determine the valid bounds of the submatrix by clamping `i - K`, `j - K`, `i + K`, and `j + K` within the grid limits `[0, M-1]` and `[0, N-1]`.
- Translate those coordinates to match your padded prefix matrix index style (typically adding $+1$).
- Run the $O(1)$ lookup: $\text{BottomRight} - \text{Top} - \text{Left} + \text{TopLeft}$.
- Store the result directly into `ans[i][j]`.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases

- **Small Matrix, Large $K$:** If $K$ is greater than the total width or height of the matrix, the clamping behavior naturally turns the boundaries into the full matrix edge points `(0, 0)` to `(M-1, N-1)`, which handles it perfectly without code breaking.
- **Boundary Blocks:** Cells on the outer edges will naturally have truncated boxes, which are natively managed by the `min/max` boundary restrictions.

### Complexity Targets

- **Time Complexity:** \* $O(M \times N)$ to precalculate the prefix array.
- $O(M \times N)$ to fill the answers since each cell query checks exactly 4 coordinates in $O(1)$ time.
- **Total Time:** $O(M \times N)$, which is optimal.

- **Space Complexity:** $O(M \times N)$ auxiliary space to store the prefix sum array.

---

## C++ Implementation

Here is the clean, readable solution applying our conceptual breakdown:

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    std::vector<std::vector<int>> matrixBlockSum(std::vector<std::vector<int>>& mat, int k) {
        int m = mat.size();
        int n = mat[0].size();

        // Step 1: Build the padded 2D prefix sum array (size (m+1) x (n+1))
        std::vector<std::vector<int>> prefix(m + 1, std::vector<int>(n + 1, 0));
        for (int r = 1; r <= m; ++r) {
            for (int c = 1; c <= n; ++c) {
                prefix[r][c] = mat[r - 1][c - 1]
                             + prefix[r - 1][c]
                             + prefix[r][c - 1]
                             - prefix[r - 1][c - 1];
            }
        }

        std::vector<std::vector<int>> ans(m, std::vector<int>(n, 0));

        // Step 2: Query the prefix sum table for each cell's block window
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                // Find clamped boundaries of our submatrix block
                int r1 = std::max(0, i - k);
                int c1 = std::max(0, j - k);
                int r2 = std::min(m - 1, i + k);
                int c2 = std::min(n - 1, j + k);

                // Shift coordinates by +1 to map properly to our 1-indexed padded prefix array
                int rowStart = r1 + 1, colStart = c1 + 1;
                int rowEnd = r2 + 1, colEnd = c2 + 1;

                // O(1) inclusion-exclusion logic
                ans[i][j] = prefix[rowEnd][colEnd]
                          - prefix[rowStart - 1][colEnd]
                          - prefix[rowEnd][colStart - 1]
                          + prefix[rowStart - 1][colStart - 1];
            }
        }

        return ans;
    }
};

```
