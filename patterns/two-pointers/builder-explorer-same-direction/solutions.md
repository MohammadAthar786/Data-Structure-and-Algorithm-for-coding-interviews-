# LC 26. Remove Duplicates from Sorted Array

https://youtu.be/06ALbFrgIoQ?si=xKdhC-XOOY7Rqikf

**Pattern:** Two Pointers → Builder & Explorer (Same Direction)

---

# 1. Core Pattern Recognition

Problem:

```text
Given a sorted array,
remove duplicates in-place.

Return number of unique elements.
```

Example:

```text
nums = [1,1,2]

Output:
k = 2

nums = [1,2,_]
```

---

### Why?

Because:

```text
We scan array once (Explorer)

We build answer inside same array (Builder)
```

---

# 2. Naive Starting Point

One naive idea:

```text
Create another array.

Traverse original array.

Insert only unique values.
```

Example:

```text
[1,1,2,2,3]
```

Build:

```text
[1,2,3]
```

---

Problem:

```text
Extra space O(N)
```

Question specifically asks:

```text
In-place
```

So we need:

```text
O(1) space
```

---

# 3. Intuition Shift (Aha Moment)

Example:

```text
[1,1,2,2,3]
```

Imagine array divided into two parts:

```text
[ Built Unique Part ][ Unprocessed Part ]
```

Initially:

```text
[1 | 1,2,2,3]
```

---

### Builder Pointer

Points to end of unique section.

```text
builder = 0
```

---

### Explorer Pointer

Scans array.

```text
explorer = 1
```

---

Explorer sees:

```text
nums[1] = 1
nums[builder] = 1
```

Duplicate.

Ignore.

---

Move explorer.

```text
explorer = 2
```

Now:

```text
nums[2] = 2
nums[builder] = 1
```

New unique value.

Expand built area.

```text
builder++
nums[builder] = nums[explorer]
```

Array becomes:

```text
[1,2 | 2,2,3]
```

---

Continue.

Final:

```text
[1,2,3 | ...]
```

---

## Aha Moment

Instead of creating a new array:

```text
Use first part of original array
as the output array itself.
```

---

# Visual Representation

Initial:

```text
B
↓
[1,1,2,2,3]
 E
```

---

Duplicate:

```text
[1,1,2,2,3]
   E
```

Builder stays.

---

New value found:

```text
B
↓
[1,2,2,2,3]
     E
```

Builder grows.

---

Final:

```text
[1,2,3,_,_]

     B
```

Unique section completed.

---

# 4. Conceptual Algorithm

---

## Step 1

Handle small array.

```text
If size <= 1
return size
```

---

## Step 2

Initialize:

```text
builder = 0
```

Unique part currently contains:

```text
nums[0]
```

---

## Step 3

Explorer starts from:

```text
explorer = 1
```

---

## Step 4

While exploring:

If:

```text
nums[explorer] != nums[builder]
```

Found new unique value.

Expand answer.

```text
builder++

nums[builder] = nums[explorer]
```

---

Otherwise:

```text
duplicate
ignore
```

---

## Step 5

Return:

```text
builder + 1
```

because builder stores last unique index.

---

# Dry Run

Example:

```text
[0,0,1,1,1,2,2,3,3,4]
```

---

Start:

```text
builder = 0

[0]
```

---

Explorer finds:

```text
0
```

Duplicate.

Skip.

---

Explorer finds:

```text
1
```

Unique.

```text
builder = 1

[0,1]
```

---

Explorer finds:

```text
1
1
```

Duplicates.

Skip.

---

Explorer finds:

```text
2
```

Unique.

```text
[0,1,2]
```

---

Continue.

Final:

```text
[0,1,2,3,4]
```

Return:

```text
5
```

---

# 5. Edge Cases & Complexity

### Empty array

```text
[]
```

Return:

```text
0
```

---

### Single element

```text
[5]
```

Return:

```text
1
```

---

### All duplicates

```text
[1,1,1,1]
```

Return:

```text
1
```

---

### No duplicates

```text
[1,2,3,4]
```

Return:

```text
4
```

---

# Complexity

Time:

```text
O(N)
```

Single scan.

---

Space:

```text
O(1)
```

No extra array.

---

# C++ Code (Student Style)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int removeDuplicates(vector<int>& nums) {

        if (nums.empty()) {
            return 0;
        }

        int builder = 0;

        for (int explorer = 1; explorer < nums.size(); explorer++) {

            if (nums[explorer] != nums[builder]) {

                builder++;

                nums[builder] = nums[explorer];
            }
        }

        return builder + 1;
    }
};
```

---

# LC 283. Move Zeroes

https://youtu.be/k5lIW5XxC7g?si=1M_PXLbIi-ZHW-c_

**Pattern:** Two Pointers → Builder & Explorer (Same Direction)

This is one of the cleanest Builder-Explorer problems.

---

# 1. Core Pattern Recognition

Problem:

```text
Move all 0's to the end
while maintaining relative order
of non-zero elements.
```

Example:

```text
[0,1,0,3,12]

Output:

[1,3,12,0,0]
```

---

## Clues

Whenever you see:

```text
move elements
maintain relative order
in-place
stable arrangement
compact valid elements
```

Think:

```text
Builder + Explorer
```

---

### Why?

We want:

```text
All non-zero elements together
```

and

```text
All zeros pushed behind
```

This is exactly like building a new array inside the old array.

---

# 2. Naive Starting Point

Create another array.

Example:

```text
[0,1,0,3,12]
```

First collect:

```text
[1,3,12]
```

Then append zeros:

```text
[1,3,12,0,0]
```

---

Problem:

```text
Extra Space = O(N)
```

Question asks:

```text
Do it in-place
```

---

# 3. Intuition Shift (Aha Moment)

Imagine array divided into two regions:

```text
[ Built Non-Zero Part ][ Unprocessed Part ]
```

---

Example:

```text
[0,1,0,3,12]
```

Builder:

```text
B = 0
```

Explorer:

```text
E scans entire array
```

---

Explorer sees:

```text
0
```

Ignore.

---

Explorer sees:

```text
1
```

Valid element.

Put it at builder.

```text
nums[B] = 1
B++
```

Array:

```text
[1,1,0,3,12]
```

---

Explorer sees:

```text
0
```

Ignore.

---

Explorer sees:

```text
3
```

Put at builder.

```text
[1,3,0,3,12]
```

Builder grows.

---

Explorer sees:

```text
12
```

Put at builder.

```text
[1,3,12,3,12]
```

---

After scan:

Builder tells us:

```text
First 3 positions contain
all non-zero values.
```

Now fill remaining positions:

```text
0
0
```

Final:

```text
[1,3,12,0,0]
```

---

## Aha Moment

Instead of moving zeros:

```text
Focus on collecting non-zero elements.
```

Then:

```text
Fill remaining positions with zeros.
```

Much easier.

---

# Visual Representation

Initial:

```text
B
↓
[0,1,0,3,12]
 E
```

---

Explorer finds:

```text
1
```

Store at builder.

```text
   B
   ↓
[1,1,0,3,12]
   E
```

---

Explorer finds:

```text
3
```

Store.

```text
      B
      ↓
[1,3,0,3,12]
        E
```

---

Explorer finds:

```text
12
```

Store.

```text
         B
         ↓
[1,3,12,3,12]
            E
```

---

Fill remaining with zeros:

```text
[1,3,12,0,0]
```

---

# 4. Conceptual Algorithm

---

## Step 1

Initialize:

```text
builder = 0
```

---

## Step 2

Explorer scans whole array.

For each element:

```text
if nums[explorer] != 0
```

Copy it.

```text
nums[builder] = nums[explorer]

builder++
```

---

## Step 3

After scan:

```text
builder
```

points to first empty position.

---

## Step 4

Fill rest with:

```text
0
```

until end of array.

---

# Dry Run

Example:

```text
[0,1,0,3,12]
```

---

Start:

```text
builder = 0
```

---

Explorer = 0

```text
0
```

Skip.

---

Explorer = 1

```text
1
```

Copy.

```text
[1,1,0,3,12]

builder = 1
```

---

Explorer = 2

```text
0
```

Skip.

---

Explorer = 3

```text
3
```

Copy.

```text
[1,3,0,3,12]

builder = 2
```

---

Explorer = 4

```text
12
```

Copy.

```text
[1,3,12,3,12]

builder = 3
```

---

Fill remaining:

```text
[1,3,12,0,0]
```

---

# Alternative (Interview Favorite)

Instead of copying and filling later:

Use swapping.

---

Builder:

```text
Points to next position where
non-zero should go.
```

Explorer:

```text
Finds non-zero element.
```

Whenever explorer finds non-zero:

```text
swap(nums[builder], nums[explorer])

builder++
```

---

Example:

```text
[0,1,0,3,12]
```

---

Find:

```text
1
```

Swap with index 0.

```text
[1,0,0,3,12]
```

---

Find:

```text
3
```

Swap.

```text
[1,3,0,0,12]
```

---

Find:

```text
12
```

Swap.

```text
[1,3,12,0,0]
```

Done.

This version is usually preferred.

---

# 5. Edge Cases & Complexity

### All zeros

```text
[0,0,0]
```

Output:

```text
[0,0,0]
```

---

### No zeros

```text
[1,2,3]
```

Output:

```text
[1,2,3]
```

---

### Single element

```text
[0]
```

or

```text
[5]
```

Works.

---

# Complexity

Time:

```text
O(N)
```

Single scan.

---

Space:

```text
O(1)
```

In-place.

---

# C++ Code (Student Style - Swap Version)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    void moveZeroes(vector<int>& nums) {

        int builder = 0;

        for (int explorer = 0; explorer < nums.size(); explorer++) {

            if (nums[explorer] != 0) {

                swap(nums[builder], nums[explorer]);

                builder++;
            }
        }
    }
};
```

---

````

# LC 27. Remove Element

https://youtu.be/aoYxNjRscNA?si=DagnesARRJoei5n9


## C++ Code — Student Style

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int builder = 0;

        for (int explorer = 0; explorer < nums.size(); explorer++) {
            if (nums[explorer] != val) {
                nums[builder] = nums[explorer];
                builder++;
            }
        }

        return builder;
    }
};
````

Interview one-line:

```text
I am not actually deleting elements; I am overwriting the front part with valid elements.
```

# LC 443. String Compression

https://youtu.be/I7drewKcN1Y?si=Ubo33rzZ4S-sx0zU

## C++ Code — Student Style

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int compress(vector<char>& chars) {
        int n = chars.size();

        int builder = 0;
        int explorer = 0;

        while (explorer < n) {
            char currentChar = chars[explorer];
            int count = 0;

            while (explorer < n && chars[explorer] == currentChar) {
                explorer++;
                count++;
            }

            chars[builder] = currentChar;
            builder++;

            if (count > 1) {
                string countString = to_string(count);

                for (char digit : countString) {
                    chars[builder] = digit;
                    builder++;
                }
            }
        }

        return builder;
    }
};
```

Interview one-line:

```text
Explorer reads repeated groups, builder writes compressed groups at the front.
```

# LC 80. Remove Duplicates from Sorted Array II

https://youtu.be/ycAq8iqh0TI?si=Uv-z8XrkCZZKeCzS

**Pattern:** Two Pointers → Builder & Explorer

This is the advanced version of LC 26.

---

## Difference Between LC 26 and LC 80

### LC 26

Allow:

```text
1 copy
```

Example:

```text
[1,1,1,2,2,3]

↓

[1,2,3]
```

---

### LC 80

Allow:

```text
At most 2 copies
```

Example:

```text
[1,1,1,2,2,3]

↓

[1,1,2,2,3]
```

Notice:

```text
Third 1 removed
```

but

```text
First two 1's kept
```

---

# Intuition Shift (Aha Moment)

For LC 26 we asked:

```text
Is current value different from previous unique value?
```

For LC 80 we ask:

```text
Have I already placed this value twice?
```

---

Example:

```text
[1,1,1,2,2,3]
```

Suppose builder has already built:

```text
[1,1]
```

Builder index:

```text
2
```

Explorer sees:

```text
1
```

Can we place it?

Check:

```text
nums[builder-2]
```

which is:

```text
1
```

Current value is also:

```text
1
```

Meaning:

```text
Two copies already exist.
```

Reject it.

---

### Key Insight

Once builder has written at least 2 elements:

Compare current value with:

```text
nums[builder - 2]
```

If equal:

```text
already have 2 copies
```

Reject.

If different:

```text
safe to place
```

Accept.

---

# 4. Visual Understanding

Example:

```text
[1,1,1,2,2,3]
```

---

Initially:

```text
Builder has:

[1,1]
```

because first two elements are always safe.

---

Explorer:

```text
sees third 1
```

Check:

```text
nums[builder-2]
=
nums[0]
=
1
```

Current:

```text
1
```

Equal.

Skip.

---

Explorer:

```text
sees 2
```

Check:

```text
nums[builder-2]
=
1
```

Different.

Place it.

---

Built array:

```text
[1,1,2]
```

---

Continue.

Final:

```text
[1,1,2,2,3]
```

---

# The Magic Rule

For "allow at most 2 duplicates":

```text
Accept current element only if

nums[explorer] != nums[builder-2]
```

---

# 5. Conceptual Algorithm

---

### Step 1

Arrays of size:

```text
0,1,2
```

are already valid.

Return size.

---

### Step 2

Start:

```text
builder = 2
```

Why?

Because:

```text
First two elements always allowed.
```

---

### Step 3

Explorer starts:

```text
explorer = 2
```

---

### Step 4

Check:

```text
if nums[explorer] != nums[builder-2]
```

Accept it.

```text
nums[builder] = nums[explorer]
builder++
```

Otherwise:

```text
skip
```

---

### Step 5

Return:

```text
builder
```

---

# Dry Run

Example:

```text
[1,1,1,2,2,3]
```

---

Initial:

```text
builder = 2
```

Built:

```text
[1,1]
```

---

Explorer:

```text
nums[2] = 1
```

Check:

```text
nums[builder-2]
=
nums[0]
=
1
```

Equal.

Skip.

---

Explorer:

```text
2
```

Check:

```text
1
```

Different.

Place.

```text
[1,1,2]
```

builder = 3

---

Explorer:

```text
2
```

Check:

```text
nums[1]
=
1
```

Different.

Place.

```text
[1,1,2,2]
```

builder = 4

---

Explorer:

```text
3
```

Check:

```text
nums[2]
=
2
```

Different.

Place.

```text
[1,1,2,2,3]
```

---

Return:

```text
5
```

---

# Generalization

### Allow at most K duplicates

Change:

```text
builder = K
```

and check:

```text
nums[explorer] != nums[builder-K]
```

This works for:

```text
K = 1  -> LC 26
K = 2  -> LC 80
K = 3  -> custom interview question
```

---

# Complexity

Time:

```text
O(N)
```

Single scan.

Space:

```text
O(1)
```

---

# C++ Code (Student Style)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int removeDuplicates(vector<int>& nums) {

        int n = nums.size();

        if (n <= 2) {
            return n;
        }

        int builder = 2;

        for (int explorer = 2; explorer < n; explorer++) {

            if (nums[explorer] != nums[builder - 2]) {

                nums[builder] = nums[explorer];
                builder++;
            }
        }

        return builder;
    }
};
```

---

General Form

```cpp
Keep at most K copies

builder = K

Check:
nums[explorer] != nums[builder-K]
```
