---
# **Best Time to Buy and Sell Stock (LC 121)**

https://youtu.be/E2-heUEnZKU?si=lCnOUo4pyilgiOUD


## 1. Core Pattern Recognition

In this problem, you want to maximize your profit by choosing a single day to buy a stock and a different day in the future to sell it.

### How it fits "Precomputation"

Precomputation is all about storing information about past states so that when you arrive at a new state, you can make an immediate, $O(1)$ decision instead of recalculating everything from scratch.

### The "Clues" in the Problem:

* **Ordered/Time-Series Data:** You are given an array where the indices represent a strict chronological progression (Time $t_0, t_1, t_2...$). You cannot sell before you buy.
* **Future depends on the Past:** To know your max profit on day `i`, you need information about the minimum price *before* day `i`.
* **"Maximize [Difference]":** Whenever you need to find a max difference between two elements where element $B$ must come after element $A$, you should think: *"As I traverse, can I keep track of the best possible history?"*

---

## 2. The "Naive" Starting Point

The brute-force way to solve this is to try **every single possible pair** of buying and selling days.

- **The Approach:** You run a nested loop. The outer loop picks a day to buy (`i`), and the inner loop looks at every subsequent day to sell (`j`). You calculate `prices[j] - prices[i]` for all combinations and track the maximum profit.
- **Why it's inefficient:** It takes $O(N^2)$ time.
- **The Redundancy:** Imagine the prices are `[3, 6, 1, 8]`.
- When you are at day 1 (price 6) and looking forward, you check what happens if you had bought at 3.
- When you are at day 3 (price 8) and looking forward, the brute force approach scans backward _again_ to find the lowest price. It re-examines 3, 6, and 1.
- **The redundant work is re-scanning the past over and over again to find the minimum price.**

---

## 3. The "Intuition Shift"

Here is your **"Aha!" moment**:

> If you are standing on any given day and deciding to sell, your profit is entirely dictated by **the absolute lowest price you've seen up to that point**. You don't care _when_ that lowest price happened, just _what_ it was.

Instead of looking backward, we can **precompute (or maintain on-the-fly)** the minimum price seen so far.

### Concrete Example: `prices = [7, 1, 5, 3, 6, 4]`

Let's trace what you actually care about as you move from left to right:

- **Day 0 (Price 7):** Lowest seen so far = 7. Profit if sold today = 0.
- **Day 1 (Price 1):** We found a new lower price! Lowest seen so far = 1. Profit if sold today = 0.
- **Day 2 (Price 5):** Lowest seen so far is still 1. If we sell today, profit is $5 - 1 = 4$.
- **Day 3 (Price 3):** Lowest seen so far is still 1. If we sell today, profit is $3 - 1 = 2$.
- **Day 4 (Price 6):** Lowest seen so far is still 1. If we sell today, profit is $6 - 1 = 5$ (New Max!).

By keeping a running record of the minimum price, we effectively "precompute" the best buying day for any future selling day in $O(1)$ time.

---

## 4. Conceptual Algorithm (No Code)

We will maintain two scalar tracking variables as we scan through the array exactly once:

1. `min_price`: Track the cheapest price seen from day 0 up to the current day. Initialize this to infinity (or the first element).
2. `max_profit`: Track the highest profit we've managed to achieve so far. Initialize this to 0.

### Step-by-step Walkthrough:

- **Loop** through each `current_price` in the array:
- **Update History:** Is the `current_price` lower than our `min_price`? If yes, update `min_price`. (We found a better day to buy).
- **Calculate Potential:** If we didn't buy today, what if we _sold_ today? Calculate `potential_profit = current_price - min_price`.
- **Update Global Max:** If `potential_profit` is greater than our `max_profit`, update `max_profit`.

- Return `max_profit`.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases:

1. **Monotonically Decreasing Prices (e.g., `[7, 5, 4, 2, 1]`):** The price only goes down. You can never make a profit. The algorithm must return `0`, not a negative number.
2. **Flat Prices (e.g., `[5, 5, 5, 5]`):** No profit can be made. Should return `0`.
3. **Array of size 1:** You can't buy and sell on the same day. The loop handles this, but it's good to note.

### Target Complexities:

- **Time Complexity:** $O(N)$ — We traverse the array exactly once.
- **Space Complexity:** $O(1)$ — We only use two variables (`min_price` and `max_profit`), completely eliminating the need for an actual precomputation array.

---

## The C++ Implementation

```cpp
#include <vector>
#include <algorithm>
#include <climits>

class Solution {
public:
    int maxProfit(std::vector<int>& prices) {
        int min_price = INT_MAX;
        int max_profit = 0;

        for (int current_price : prices) {
            // 1. Maintain the precomputed state (the lowest buying price seen so far)
            if (current_price < min_price) {
                min_price = current_price;
            }
            // 2. Otherwise, check if selling today yields a better profit
            else {
                int potential_profit = current_price - min_price;
                max_profit = std::max(max_profit, potential_profit);
            }
        }

        return max_profit;
    }
};

```

## **LC 1413 — "Minimum Value to Get Positive Step by Step Sum**

## 1. Core Pattern Recognition

The problem asks us to find a starting integer startValue (which must be $\ge 1$) such that if we sequentially add elements of an array to it, the running total never drops below 1.
How it fits "Precomputation"The Clue:

The phrase "step-by-step sum" is a dead giveaway for a Prefix Sum / Running Total.

The Core Need: The ultimate constraint on our startValue isn't decided by the final total sum, nor is it decided by individual elements. It is entirely decided by the lowest point the cumulative sum reaches during its journey. If we can precompute or track this lowest dip, we can instantly determine how much "cushion" our startValue needs to provide.

## 2. The Naive Starting Point

A truly brute-force approach (ignoring that you already know prefix sums) might look like: try `startValue = 1`, simulate the entire array, check if the running sum ever goes ≤ 0. If it fails, try `startValue = 2`, simulate the _entire array again_. Keep incrementing and re-simulating until it works.

Where's the redundant work? You're re-walking the whole array for every candidate `startValue`. If the answer turns out to be, say, 100, you've simulated the array 100 times. That's `O(N * answer)` — and "answer" itself isn't even bounded nicely by N, it depends on how negative the dips are.

The deeper redundancy: every single one of those simulations computes **the exact same sequence of running sums** — only shifted by a constant (`startValue - 1`). You're recomputing the same cumulative sums over and over just to check "did adding this constant push it over the line."

## 3. The Intuition Shift

Here's the "Aha!": since every simulation is just `startValue + (running sum so far)`, the _shape_ of the running sum curve never changes — only its vertical position shifts depending on what `startValue` you picked. So instead of asking "what startValue makes every point on this curve positive?" for many guesses, just compute the curve **once**, find its **lowest point**, and solve algebraically for the `startValue` that lifts that lowest point just above zero.

Concrete example: `nums = [-3, 2, -3, 4, 2]`

Compute the running sum starting from 0 (no startValue added yet):

- After -3: **-3**
- After 2: -1
- After -3: **-4** ← lowest so far
- After 4: 0
- After 2: 2

The minimum running sum hit is **-4**. That's the worst point in the whole journey — if your total stays positive _there_, it stays positive everywhere (since -4 is the floor).

Now the question becomes pure algebra: I need `startValue + (-4) ≥ 1`, i.e. `startValue ≥ 5`. So `startValue = 5`.

That's the shift — from "simulate many candidate starting points" to "simulate the array exactly once to find the worst dip, then solve one inequality."

## 4. Conceptual Algorithm (No Code)

```
1. Initialize runningSum = 0
2. Initialize minSum = 0   (tracks the lowest the runningSum has ever been)
3. For each number in nums:
       runningSum = runningSum + number
       minSum = min(minSum, runningSum)
4. We need: startValue + minSum >= 1
   => startValue >= 1 - minSum
5. Answer = max(1, 1 - minSum)
```

Notice `minSum` starts at 0, not at `nums[0]` — because the running sum sequence conceptually starts at 0 _before_ you add anything, and that initial 0 is itself a valid "step" to consider (it covers the case where the array never actually dips below the start).

Walking the pointer: there's really just one pointer moving left to right, carrying two pieces of state forward — the running total, and the minimum the running total has touched. No backtracking, no nested loops.

## 5. Edge Cases & Complexity

**Edge cases to watch:**

- **All positive array** → `minSum` stays at 0 (never dips below the starting 0), so `1 - minSum = 1` → answer is `1`. Sanity check this in your head: if the array is all positive, starting at 1 obviously works.
- **Single element array**, especially a single very negative number — make sure your loop handles N=1 correctly (it should, naturally).
- **minSum is positive or zero but you still take max(1, ...)** — don't forget the `max(1, ...)` wrapper; even if `1 - minSum` computes to something ≤ 0, `startValue` must be **at least 1** per problem constraints (it says "positive," and presumably startValue itself must be a positive integer).
- **Off-by-one on the inequality direction** — it's "greater than or equal to 1" not "greater than 0," so double check whether you need `1 - minSum` or `-minSum`. (It's `1 - minSum` because step sum must stay ≥ 1, i.e., strictly positive as an integer.)

**Complexity we're aiming for:**

- **Time: O(N)** — one pass, computing running sum and tracking its minimum.
- **Space: O(1)** — you never need to store the whole prefix sum array, just two running scalars (`runningSum` and `minSum`).

---

```cpp
class Solution {
public:
    int minStartValue(vector<int>& nums) {
        int runningSum = 0;
        int minSum = 0;  // tracks the lowest the running sum ever touches
                         // (starts at 0, the implicit "before we begin" step)

        for (int num : nums) {
            runningSum += num;
            minSum = min(minSum, runningSum);
        }

        // We need: startValue + minSum >= 1  =>  startValue >= 1 - minSum
        // Also startValue must be at least 1 (it has to be positive).
        return max(1, 1 - minSum);
    }
};
```

**Quick trace on `nums = [-3, 2, -3, 4, 2]`:**

| num   | runningSum | minSum |
| ----- | ---------- | ------ |
| start | 0          | 0      |
| -3    | -3         | -3     |
| 2     | -1         | -3     |
| -3    | -4         | **-4** |
| 4     | 0          | -4     |
| 2     | 2          | -4     |

`return max(1, 1 - (-4)) = max(1, 5) = 5` ✅ matches our hand-derived answer.

One line does the heavy lifting: `minSum = min(minSum, runningSum);` — that's the entire "precompute the worst dip" idea from step 3, collapsed into a single running comparison instead of storing a whole prefix-sum array.
