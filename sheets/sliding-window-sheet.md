<!-- SLIDING_WINDOW_V3 -->

# Contiguous Range Processing Patterns


### Philosophy

Core Pattern → State Mechanism → Problem Ladder → Bridge to Next Problem

The goal is not to memorize problems.

The goal is to identify:

* Why a window works
* What state is maintained
* When the window expands
* When the window shrinks
* How validity is updated incrementally

---

# Level 0: Sliding Window Applicability Test

Before asking **what state is maintained**, ask:

## Can Sliding Window Work Here?

A sliding window is usually applicable when:

1. We are processing a contiguous range.
2. Window validity can be updated incrementally.
3. Expanding or shrinking does not require recomputing the entire range.

---

## Typical Signals

* subarray
* substring
* contiguous segment
* longest
* shortest
* at most k
* exactly k
* count valid ranges

---

## Typical Anti-Signals

Sliding window often does **not** work directly when:

* non-contiguous selection allowed
* global optimization required
* arbitrary range queries
* validity cannot be updated incrementally

Consider:

* Prefix Sum
* Binary Search
* Monotonic Stack
* Heap
* DP

instead.

---

# Classification by State Maintenance Mechanism

Sliding Window =

```text
Contiguous Range
+
Incrementally Maintained State
```

---

# 1. Scalar-State Windows

## Core Idea

Window validity depends only on simple scalar values.

No hashmap or deque required.

---

## Maintained State

* running sum
* running count
* window size
* limited violations
* simple constraints

---

# 1A. Fixed-Size Scalar Windows

## Recognition Signals

* fixed window size k
* moving average
* fixed-length subarray

---

### Problem Ladder

| Order | Problem                             | Label   | Maintained State |
| ----- | ----------------------------------- | ------- | ---------------- |
| 1     | Maximum Average Subarray I (LC 643) | MUST DO | Running Sum      |

---

## Invariant

```text
Window size remains constant.
```

---

## Bridge

After fixed-size windows, learn variable-size windows where validity determines when shrinking occurs.

---

# 1B. Variable-Size Scalar Windows

## Recognition Signals

* longest valid range
* shortest valid range
* limited violations
* positive-sum shrinking

---

### Problem Ladder

| Order | Problem                                             | Label    | Maintained State       |
| ----- | --------------------------------------------------- | -------- | ---------------------- |
| 1     | Max Consecutive Ones (LC 485)                       | MUST DO  | Consecutive Count      |
| 2     | Max Consecutive Ones II (LC 487)                    | MUST DO  | Zero Count ≤ 1         |
| 3     | Longest Subarray of 1s After Deleting One (LC 1493) | Variant  | One Deletion Allowance |
| 4     | Minimum Size Subarray Sum (LC 209)                  | MUST DO  | Running Sum ≥ Target   |
| 5     | Maximum Points You Can Obtain from Cards (LC 1423)  | Advanced | Complement Window Sum  |

---

## Invariant

```text
Expand window.

While invalid:
    shrink window.
```

---

## Bridge

Scalar state eventually becomes insufficient.

Next step:

Track frequencies.

---

# 2. Frequency-State Windows

## Core Idea

Window validity depends on frequencies.

Need dynamic tracking of:

* duplicates
* distinct counts
* exact frequencies
* character requirements

---

## Maintained State

* hashmap
* frequency array
* distinct counter
* matched-character counter

---

# 2A. Duplicate and Distinct Tracking

## Recognition Signals

* repeating characters
* at most k distinct
* distinct count constraints

---

### Problem Ladder

| Order | Problem                                               | Label   | Maintained State     |
| ----- | ----------------------------------------------------- | ------- | -------------------- |
| 1     | Longest Substring Without Repeating Characters (LC 3) | MUST DO | Duplicate Prevention |
| 2     | Longest Substring with At Most K Distinct (LC 340)    | MUST DO | Distinct Count ≤ K   |
| 3     | Fruit Into Baskets (LC 904)                           | MUST DO | At Most 2 Distinct   |

---

## Invariant

```text
Maintain frequency map.

Shrink until duplicates or distinct-count violations disappear.
```

---

## Bridge

Instead of preventing duplicates,

now we require exact frequency matching.

---

# 2B. Frequency Matching Windows

## Recognition Signals

* anagrams
* permutation checking
* exact character requirements

---

### Problem Ladder

| Order | Problem                                             | Label    | Maintained State                |
| ----- | --------------------------------------------------- | -------- | ------------------------------- |
| 1     | Find All Anagrams in String (LC 438)                | MUST DO  | Frequency Matching              |
| 2     | Permutation in String (LC 567)                      | MUST DO  | Exact Frequency Satisfaction    |
| 3     | Minimum Window Substring (LC 76)                    | MUST DO  | Required Character Satisfaction |
| 4     | Replace the Substring for Balanced String (LC 1234) | Advanced | Character Balance Validity      |

---

## Invariant

```text
Track required frequencies.

Shrink only while requirements remain satisfied.
```

---

## Bridge

Next step:

Instead of finding the best window,

count all valid windows.

---

# 3. Counting-State Windows

## Core Idea

Count how many valid subarrays/substrings exist.

---

## Maintained State

* contribution counts
* valid-window counts
* exact-count transformations

---

# Most Important Transformation

```text
exactly(k) = atMost(k) - atMost(k-1)
```

---

## Core Insight

For every right pointer:

```text
Count how many valid ranges end at right.
```

---

## Recognition Signals

* count subarrays
* count substrings
* exactly k
* exact sum
* exact distinct count

---

### Problem Ladder

| Order | Problem                                      | Label   | Maintained State        |
| ----- | -------------------------------------------- | ------- | ----------------------- |
| 1     | Binary Subarrays With Sum (LC 930)           | MUST DO | Sum Contribution        |
| 2     | Count Number of Nice Subarrays (LC 1248)     | MUST DO | Odd Count Contribution  |
| 3     | Subarrays with K Different Integers (LC 992) | MUST DO | Exact Distinct Counting |

---

## Invariant

```text
Count valid windows ending at current right.
```

---

## Bridge

Now validity alone is not enough.

Need efficient range optimization.

---

# 4. Monotonic Range Optimization

## Core Idea

Need efficient maintenance of:

* maximum
* minimum
* best candidate

inside a moving range.

---

## Maintained State

* monotonic decreasing deque
* monotonic increasing deque
* candidate ordering

---

## Recognition Signals

* sliding maximum
* sliding minimum
* shortest valid range
* best candidate optimization

---

### Problem Ladder

| Order | Problem                                                          | Label    | Maintained State            |
| ----- | ---------------------------------------------------------------- | -------- | --------------------------- |
| 1     | Sliding Window Maximum (LC 239)                                  | MUST DO  | Monotonic Decreasing Deque  |
| 2     | Longest Continuous Subarray with Absolute Diff ≤ Limit (LC 1438) | MUST DO  | Min + Max Deques            |
| 3     | Shortest Subarray with Sum ≥ K (LC 862)                          | Advanced | Monotonic Prefix Candidates |
| 4     | Jump Game VI (LC 1696)                                           | Advanced | DP Candidate Deque          |

---

## Invariant

```text
Discard candidates that can never become optimal.
```

---

## Bridge

Now multiple synchronized states must be maintained simultaneously.

---

# 5. Multi-Constraint State Windows

## Core Idea

Window validity depends on multiple synchronized states.

---

## Maintained State

* multiple frequency maps
* chunk alignment
* synchronized counts
* multiple invariants

---

## Recognition Signals

* exact word matching
* multiple simultaneous constraints
* difficult shrink logic
* chunk traversal

---

### Problem Ladder

| Order | Problem                                           | Label   | Maintained State                |
| ----- | ------------------------------------------------- | ------- | ------------------------------- |
| 1     | Substring with Concatenation of All Words (LC 30) | MUST DO | Chunk Frequency Synchronization |

---

## Invariant

```text
Multiple conditions must remain valid simultaneously.
```

---

# Final Mental Compression

```text
Contiguous Range Processing
=
Incrementally Maintain State
Over a Moving Window
```

State may be:

* Scalar
* Frequency
* Counting
* Monotonic
* Multi-Constraint

---

# Ultimate Recognition Heuristic

| Signal                            | Likely Mechanism        |
| --------------------------------- | ----------------------- |
| simple running condition          | Scalar Window           |
| duplicates/distinct/frequencies   | Frequency Window        |
| exact count requirements          | Counting Window         |
| max/min optimization              | Monotonic Deque         |
| multiple synchronized constraints | Multi-Constraint Window |

---

# Sliding Window Recognition Flow

```text
Contiguous Range?

    No
        -> Not Sliding Window

    Yes

Can validity be updated incrementally?

    No
        -> Prefix Sum
        -> Binary Search
        -> DP
        -> Other Pattern

    Yes

What state is required?

        Scalar?
            -> Scalar Window

        Frequencies?
            -> Frequency Window

        Exact Count?
            -> Counting Window

        Max/Min Optimization?
            -> Monotonic Deque

        Multiple States?
            -> Multi-Constraint Window
```

---

# Placement Shortlist (High ROI)

Must solve comfortably:

* LC 643
* LC 209
* LC 3
* LC 340
* LC 904
* LC 438
* LC 567
* LC 76
* LC 930
* LC 1248
* LC 992
* LC 239
* LC 1438
* LC 30

These cover the overwhelming majority of sliding-window state transitions seen in interviews.
