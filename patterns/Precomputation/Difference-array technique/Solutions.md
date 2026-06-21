## **LeetCode 370: Range Addition**

https://youtu.be/M0AU0WyZKuQ?si=VtafROiRnnWWZLZT

---

## 1. Core Pattern Recognition

To spot a **Difference Array** problem in the wild, you are looking for a very specific combination of actions:

- **The Setup:** You start with an initial array (usually filled with zeros or static values).
- **The Clue:** You are given a large number of queries, where each query asks you to **modify a continuous subarray (a range from index `start` to `end`)** by adding a specific value `inc`.
- **The Goal:** You only need to return the final state of the array _after all queries have been applied_.

> **The Giveaway:** If a problem asks you to do **frequent range updates** but **delayed/single-time reads** (you don't need to know the intermediate state of the array between updates), it is a textbook candidate for a Difference Array.

---

## 2. The "Naive" Starting Point

Imagine you have an array of size $N$ initialized to zeros, and you are given $K$ update operations.

### The Brute-Force Approach

For every single update `[start, end, inc]`, you write a `for` loop that iterates from `start` to `end` and adds `inc` to each element.

### Why it's Inefficient

If you have a query that covers almost the entire array (e.g., from index $0$ to $N-1$), you are forced to touch $N$ elements. If you have $K$ such queries, your time complexity skyrockets to $O(N \times K)$.

### Where is the Redundant Work?

Imagine two overlapping updates: one adds `5` to indices $1$ to $4$, and the next adds `3` to indices $2$ to $5$.
In the naive approach, you visit index $2, 3,$ and $4$ twice, manually adding the numbers. You are repeating the manual traversal over the exact same memory locations over and over again.

---

## 3. The Intuition Shift

Here is the **"Aha!" moment**: Instead of recording the _absolute value_ at every single index during every query, what if we only record the **changes (differences) between adjacent elements**?

Think of it like a staircase. If you want to raise a section of a boardwalk by 2 inches, you don't need to adjust every single plank individually. You just put a 2-inch block at the _start_ of the section to step up, and a 2-inch drop at the _end_ of the section to step back down. Everything in the middle naturally stays at that elevated level!

### A Concrete Example

Let's say our array size $N = 5$ (initialized to `[0, 0, 0, 0, 0]`).
We have one query: **Add `3` to the range `[1, 3]**`.

Instead of changing indices 1, 2, and 3, we create a **Difference Array (`diff`)** of the same size.

1. We mark the start of the increase: `diff[1] += 3`
2. We mark where the increase _ends_ by canceling it out right after the range: `diff[3 + 1] -= 3` (which is `diff[4]`)

Our `diff` array becomes: `[0, 3, 0, 0, -3]`

To get our actual final array, we just compute the **prefix sum** (running total) of this `diff` array from left to right:

- Index 0: `0` $\rightarrow$ **0**
- Index 1: `0 + 3` $\rightarrow$ **3**
- Index 2: `3 + 0` $\rightarrow$ **3**
- Index 3: `3 + 0` $\rightarrow$ **3**
- Index 4: `3 + (-3)` $\rightarrow$ **0**

Final Array: `[0, 3, 3, 3, 0]` — Exactly what we wanted, but we only modified **two** elements in the `diff` array instead of three!

---

## 4. Conceptual Algorithm (No Code)

Here is how the optimized algorithm flows:

1. **Initialize:** Create a `diff` array of size $N$ filled with `0`.
2. **Process Queries:** For each update `[start, end, inc]`:

- Add `inc` to `diff[start]`. This signifies that from this point forward, all elements are shifted up by `inc`.
- If `end + 1` is still within the bounds of the array, subtract `inc` from `diff[end + 1]`. This signifies that the shift up expires after `end`.

3. **Reconstruct:** Walk through the `diff` array from index `1` to `N-1`. Update each element by adding the value of the previous element to it (`diff[i] += diff[i-1]`). This accumulates the running prefix sum.
4. **Result:** The mutated `diff` array is now your final transformed array.

---

## 5. Edge Cases & Complexity

### Critical Edge Cases

- **`end` is at the very last index ($N-1$):** When `end + 1 == N`, it is out of bounds. You do _not_ subtract `inc` anywhere because the modification extends all the way to the end of the array.
- **Overlapping Ranges:** Multiple queries covering the same indices. The `diff` array handles this naturally through accumulation (e.g., `diff[start] += inc`).
- **Negative Increments:** The logic works exactly the same if `inc` is negative.

### Complexity Targets

- **Time Complexity:** \* Processing $K$ updates takes $O(1)$ time per update $\rightarrow$ $O(K)$.
- Reconstructing the final array takes a single pass $\rightarrow$ $O(N)$.
- **Total Time:** $O(N + K)$, which is a massive upgrade from $O(N \times K)$.

- **Space Complexity:** $O(N)$ to store the difference array (or $O(1)$ auxiliary if you reuse the output array).

---

## C++ Implementation

Here is the clean, easy-to-follow C++ implementation based on this intuition:

```cpp
#include <vector>

class Solution {
public:
    std::vector<int> getModifiedArray(int length, std::vector<std::vector<int>>& updates) {
        // Step 1: Initialize the difference array with zeros
        std::vector<int> diff(length, 0);

        // Step 2: Mark the boundaries for each update operation
        for (const auto& update : updates) {
            int start = update[0];
            int end = update[1];
            int inc = update[2];

            diff[start] += inc;

            // If the boundary drop-off falls within the array, apply it
            if (end + 1 < length) {
                diff[end + 1] -= inc;
            }
        }

        // Step 3: Compute the prefix sum to get the final mutated array
        for (int i = 1; i < length; ++i) {
            diff[i] += diff[i - 1];
        }

        return diff;
    }
};

```

the exact sister problem to LC 370!

## **LeetCode 1109: Corporate Flight Bookings**

https://youtu.be/17sXcR5zGSs?si=MEmroGkYXC_EdvdP

--the exact sister problem to LC 370!

is practically identical in its underlying math, but it wraps the concept in a real-world scenario (flight bookings).

---

## 1. Pattern Recognition & Translation

Let's translate the flight terminology into the array terms we just learned:

- **`n` flights** $\rightarrow$ An array of size $N$ (1-indexed).
- **`bookings[i] = [first_i, last_i, seats_i]`** $\rightarrow$ A range update query `[start, end, inc]`.
- **Goal:** Return the total number of seats booked for each flight $\rightarrow$ Return the final state of the array after all updates.

The core giveaway is exactly the same: **Multiple range updates** (booking seats for a range of flights) followed by a **single final read** (reporting the total seats for all flights).

---

## 2. The Visual Shift (1-Based Indexing)

The only minor twist in this problem is that flights are **1-indexed** (from $1$ to $n$), whereas our C++ vectors are **0-indexed** (from $0$ to $n-1$).

We can handle this easily by shifting the indices down by 1 when updating our difference array.

### Visualizing the Updates

If a booking says `[1, 2, 10]` (add 10 seats to flights 1 and 2) for $n = 5$:

- In 0-indexed terms, this applies to indices `0` and `1`.
- The increase starts at index `0` (flight 1).
- The increase drops off after index `1` (flight 2), which means the drop-off happens at index `2`.

---

## 3. C++ Implementation

Notice how the structure of this code is almost identical to LC 370. We just allocate a size of `n` and adjust the incoming 1-based indices to 0-based indices.

```cpp
#include <vector>

class Solution {
public:
    std::vector<int> corpFlightBookings(std::vector<std::vector<int>>& bookings, int n) {
        // Step 1: Initialize the difference array with zeros
        std::vector<int> diff(n, 0);

        // Step 2: Process each booking
        for (const auto& booking : bookings) {
            int first = booking[0] - 1; // Convert to 0-indexed
            int last = booking[1] - 1;  // Convert to 0-indexed
            int seats = booking[2];

            diff[first] += seats;

            // The drop-off happens right after the 'last' flight
            if (last + 1 < n) {
                diff[last + 1] -= seats;
            }
        }

        // Step 3: Compute the prefix sum to get the final seat counts
        for (int i = 1; i < n; ++i) {
            diff[i] += diff[i - 1];
        }

        return diff;
    }
};

```

---

## Complexity

- **Time Complexity:** $O(N + K)$ where $K$ is the number of bookings and $N$ is the number of flights. We iterate through the bookings once, and then make a single pass through the flights array.
- **Space Complexity:** $O(1)$ auxiliary space if we consider the returned array as part of the output requirements.

## **LeetCode 1094: Car Pooling**

https://youtu.be/E0g0QTwYK6g?si=OhdQr024F17VGPvk

---

## 1. Pattern Recognition & Translation

Let's map the problem parameters directly to our framework:

- **`trips[i] = [numPassengers, from, to]`** $\rightarrow$ A range update query `[start, end, inc]`.
- **The Car Track** $\rightarrow$ An array tracking how many passengers are in the car at any kilometer milestone.
- **`capacity`** $\rightarrow$ A constraint checkpoint. If our prefix sum value at any single index exceeds `capacity`, we immediately know the configuration is invalid.

> **The Edge Case Twist:** Unlike the previous problem where the range was inclusive `[start, end]`, in carpooling, passengers **get out of the car** when they arrive at the destination `to`. This means at kilometer `to`, those passengers are no longer taking up seats.

Therefore, a trip `[numPassengers, from, to]` modifies the range **inclusive of `from` but exclusive of `to**`.

---

## 2. The Visual Shift

Because passengers leave the car _at_ kilometer `to`, the drop-off doesn't happen at `to + 1`. It happens **exactly at `to**`.

If `trips[i] = [2, 1, 5]`, it means:

- At kilometer `1`: $2$ passengers get in (`diff[1] += 2`).
- At kilometer `5`: Those $2$ passengers get out (`diff[5] -= 2`).

---

## 3. Dealing with Constraints: Array vs. Map

The constraints specify that locations are between `0` and `1000`. Because the max coordinate is fixed and very small, we can simply allocate a static array of size `1001` to act as our fixed difference array!

_(Note: If the constraints allowed locations up to $10^9$, you would switch out the static array for an ordered map `std::map<int, int>` to only track active event locations, a variation known as the **Line Sweep** technique.)_

---

## 4. C++ Implementation

Here is how cleanly this maps to code:

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    bool carPooling(std::vector<std::vector<int>>& trips, int capacity) {
        // Since max location is 1000, an array of size 1001 covers all milestones [0...1000]
        std::vector<int> diff(1001, 0);

        // Step 1: Record passenger changes at milestones
        for (const auto& trip : trips) {
            int numPassengers = trip[0];
            int from = trip[1];
            int to = trip[2];

            diff[from] += numPassengers; // Passengers board
            diff[to] -= numPassengers;   // Passengers alight exactly at 'to'
        }

        // Step 2: Compute prefix sums and check against capacity simultaneously
        int currentPassengers = 0;
        for (int i = 0; i <= 1000; ++i) {
            currentPassengers += diff[i];

            // If at any kilometer milestone we exceed the car's limit, return false
            if (currentPassengers > capacity) {
                return false;
            }
        }

        return true;
    }
};

```

---

## Complexity

- **Time Complexity:** $O(N + M)$ where $N$ is the number of trips and $M$ is the fixed coordinate track size ($1001$). This simplifies cleanly to a linear runtime.
- **Space Complexity:** $O(M) = O(1)$ auxiliary space because our tracking array size is strictly bounded by the constant size $1001$.

---

For a deeper look into handling this using an ordered map (useful for larger coordinate ranges) or visualizing how events sort when multiple pickups and dropoffs hit the same index, you can watch this thorough video walk-through on
