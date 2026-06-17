## LC 3 — Longest Substring Without Repeating Characters

This is your first **Frequency-State Window** problem.

Now we are not just tracking sum/count.

We are tracking:

```text
Which characters are currently inside the window?
```

---

## Pattern Recognition

Problem says:

```text
Find longest substring without repeating characters
```

Clues:

```text
substring
longest
without repeating
characters
```

Important:

```text
substring = contiguous part of string
```

So this becomes:

```text
Find the longest valid window where every character appears at most once.
```

Valid condition:

```text
no duplicate character inside current window
```

---

## Naive Approach

Try every substring and check if it has duplicates.

Example:

```text
s = "abcabcbb"
```

Check:

```text
"a"
"ab"
"abc"
"abca" -> invalid because a repeats
"abcab" -> invalid
...
```

Problem:

For every starting index, we again scan forward and repeatedly check duplicate characters.

This causes redundant work.

Time:

```text
O(n²) or O(n³)
```

depending on how duplicate checking is done.

---

## Intuition Shift

Instead of rebuilding every substring, maintain a moving window.

Example:

```text
s = "abcabcbb"
```

Start:

```text
[a] valid
[a b] valid
[a b c] valid
[a b c a] invalid
```

Why invalid?

```text
a repeated
```

So move `left` until the old `a` is removed.

```text
old: [a b c a]
move left:
[b c a]
```

Now valid again.

Main idea:

```text
Expand right.
If duplicate appears, shrink left until window becomes valid.
```

---

## Conceptual Algorithm

```text
left = 0
set = empty
maxLen = 0

for right from 0 to n - 1:
    while s[right] is already inside set:
        remove s[left] from set
        left++

    add s[right] into set
    update maxLen = max(maxLen, right - left + 1)
```

Why `while`?

Because duplicate may not be at the left position immediately.

Example:

```text
s = "abca"
```

When right reaches second `a`:

```text
window = [a b c]
incoming = a
```

Need to remove:

```text
remove a
```

Then add new `a`.

---

## Human C++ Code

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_set<char> seen;

        int left = 0;
        int maxLen = 0;

        for (int right = 0; right < s.size(); right++) {
            while (seen.count(s[right])) {
                seen.erase(s[left]);
                left++;
            }

            seen.insert(s[right]);

            int windowSize = right - left + 1;
            maxLen = max(maxLen, windowSize);
        }

        return maxLen;
    }
};
```

---

## Dry Run

```text
s = "abcabcbb"
```

```text
right = 0 -> a
window = [a], maxLen = 1

right = 1 -> b
window = [a b], maxLen = 2

right = 2 -> c
window = [a b c], maxLen = 3

right = 3 -> a
a already exists

remove s[left] = a
left moves

window = [b c]
add a

window = [b c a], maxLen = 3
```

Later max remains:

```text
3
```

Answer:

```text
3
```

---

## Edge Cases

```text
s = ""
```

Answer:

```text
0
```

```text
s = "bbbb"
```

Answer:

```text
1
```

```text
s = "abcdef"
```

Answer:

```text
6
```

```text
s = "pwwkew"
```

Answer:

```text
3
```

Because answer is:

```text
"wke"
```

not:

```text
"pwke"
```

`"pwke"` is a subsequence, not substring.

---

## Complexity

```text
Time: O(n)
Space: O(min(n, character_set_size))
```

Each character enters the set once and leaves at most once.

## LC 340 — Longest Substring with At Most K Distinct Characters

This is the next level after LC 3.

LC 3 condition:

```text
no character repeats
```

LC 340 condition:

```text
at most k distinct characters
```

So now we need a **frequency map**, not just a set.

---

## Pattern Recognition

Clues:

```text
longest substring
at most k distinct characters
```

Important:

```text
substring = contiguous window
longest = expand as much as possible
at most k distinct = window validity condition
```

Valid window:

```text
number of unique characters <= k
```

Invalid window:

```text
number of unique characters > k
```

---

## Naive Approach

Try every substring and count distinct characters.

Example:

```text
s = "eceba", k = 2
```

Substrings:

```text
"e"
"ec"
"ece"  -> distinct = {e, c}, valid
"eceb" -> distinct = {e, c, b}, invalid
...
```

Problem:

For each starting index, we keep recalculating character counts again.

Time:

```text
O(n²)
```

or worse if counting distinct separately.

---

## Intuition Shift

Maintain one window and a frequency map.

Example:

```text
s = "eceba", k = 2
```

Expand:

```text
[e]       distinct = 1
[e c]     distinct = 2
[e c e]   distinct = 2
[e c e b] distinct = 3 invalid
```

Now we shrink from left until distinct becomes `2` again.

Remove left characters:

```text
remove e -> e count still 1
window = [c e b], distinct still 3

remove c -> c count becomes 0
window = [e b], distinct becomes 2
```

Now valid again.

Main idea:

```text
Expand right.
If distinct count > k, shrink left.
Update answer only when window is valid.
```

---

## Conceptual Algorithm

```text
left = 0
freq = empty map
maxLen = 0

for right from 0 to n - 1:
    add s[right] into freq

    while freq.size() > k:
        decrease freq[s[left]]
        if freq[s[left]] becomes 0:
            remove it from map
        left++

    update maxLen = max(maxLen, right - left + 1)
```

Why frequency map?

Because when we remove a character from the left, we need to know:

```text
Did this character fully leave the window?
```

Example:

```text
window = "ece"
freq = {e: 2, c: 1}
```

If we remove one `e`:

```text
freq = {e: 1, c: 1}
```

`e` still exists, so distinct count does not reduce.

---

## Human C++ Code

```cpp
class Solution {
public:
    int lengthOfLongestSubstringKDistinct(string s, int k) {
        unordered_map<char, int> freq;

        int left = 0;
        int maxLen = 0;

        for (int right = 0; right < s.size(); right++) {
            freq[s[right]]++;

            while (freq.size() > k) {
                freq[s[left]]--;

                if (freq[s[left]] == 0) {
                    freq.erase(s[left]);
                }

                left++;
            }

            maxLen = max(maxLen, right - left + 1);
        }

        return maxLen;
    }
};
```

---

## Dry Run

```text
s = "eceba", k = 2
```

```text
right = 0 -> e
window = [e]
freq = {e:1}
maxLen = 1

right = 1 -> c
window = [e c]
freq = {e:1, c:1}
maxLen = 2

right = 2 -> e
window = [e c e]
freq = {e:2, c:1}
maxLen = 3

right = 3 -> b
window = [e c e b]
freq = {e:2, c:1, b:1}
invalid because distinct = 3

shrink:
remove e -> freq {e:1, c:1, b:1}
still invalid

remove c -> freq {e:1, b:1}
valid

window = [e b]
maxLen remains 3
```

Answer:

```text
3
```

Substring:

```text
"ece"
```

---

## Edge Cases

```text
s = ""
```

Answer:

```text
0
```

```text
k = 0
```

No characters allowed.

Answer:

```text
0
```

```text
k >= number of distinct characters in s
```

Whole string is valid.

Answer:

```text
s.length()
```

---

## Complexity

```text
Time: O(n)
Space: O(k)
```

Technically space can be:

```text
O(character_set_size)
```

But the map usually stores at most `k + 1` characters during processing.

## LC 904 — Fruit Into Baskets

This is basically the same pattern as **LC 340**.

LC 340:

```text
Longest substring with at most k distinct characters
```

LC 904:

```text
Longest subarray with at most 2 distinct fruit types
```

So here:

```text
k = 2
```

---

## Pattern Recognition

Problem says:

```text
You have two baskets.
Each basket can hold only one type of fruit.
Pick fruits from a contiguous row of trees.
Find maximum fruits you can collect.
```

Translate this:

```text
two baskets = at most 2 distinct types
contiguous row = subarray / sliding window
maximum fruits = longest valid window
```

So the real problem is:

```text
Find longest contiguous subarray with at most 2 distinct numbers.
```

---

## Naive Approach

Try every starting tree and keep collecting until more than 2 fruit types appear.

Example:

```text
fruits = [1, 2, 1, 2, 3]
```

From index 0:

```text
[1]
[1,2]
[1,2,1]
[1,2,1,2]
[1,2,1,2,3] -> invalid, 3 types
```

From index 1:

```text
[2]
[2,1]
[2,1,2]
[2,1,2,3] -> invalid
```

This repeats a lot of work.

Time:

```text
O(n²)
```

---

## Intuition Shift

Keep one window and track fruit frequencies.

Example:

```text
fruits = [1, 2, 1, 2, 3]
```

Expand:

```text
[1]          types = {1}
[1, 2]       types = {1, 2}
[1, 2, 1]    types = {1, 2}
[1, 2, 1, 2] types = {1, 2}
```

Now add `3`:

```text
[1, 2, 1, 2, 3] types = {1, 2, 3}
```

Invalid because we only have two baskets.

So shrink from left until only 2 types remain:

```text
remove 1
[2, 1, 2, 3] still {1, 2, 3}

remove 2
[1, 2, 3] still {1, 2, 3}

remove 1
[2, 3] now {2, 3}
```

Valid again.

---

## Conceptual Algorithm

```text
left = 0
freq = empty map
maxFruits = 0

for right from 0 to n - 1:
    add fruits[right] into freq

    while freq.size() > 2:
        decrease freq[fruits[left]]

        if freq[fruits[left]] becomes 0:
            remove that fruit type from map

        left++

    update maxFruits = max(maxFruits, right - left + 1)
```

---

## Human C++ Code

```cpp
class Solution {
public:
    int totalFruit(vector<int>& fruits) {
        unordered_map<int, int> freq;

        int left = 0;
        int maxFruits = 0;

        for (int right = 0; right < fruits.size(); right++) {
            freq[fruits[right]]++;

            while (freq.size() > 2) {
                freq[fruits[left]]--;

                if (freq[fruits[left]] == 0) {
                    freq.erase(fruits[left]);
                }

                left++;
            }

            maxFruits = max(maxFruits, right - left + 1);
        }

        return maxFruits;
    }
};
```

---

## Dry Run

```text
fruits = [1, 2, 3, 2, 2]
```

```text
right = 0 -> 1
window = [1]
types = {1}
max = 1

right = 1 -> 2
window = [1, 2]
types = {1, 2}
max = 2

right = 2 -> 3
window = [1, 2, 3]
types = {1, 2, 3}
invalid

remove 1
window = [2, 3]
types = {2, 3}
max = 2

right = 3 -> 2
window = [2, 3, 2]
types = {2, 3}
max = 3

right = 4 -> 2
window = [2, 3, 2, 2]
types = {2, 3}
max = 4
```

Answer:

```text
4
```

---

## Edge Cases

```text
fruits = [1]
```

Answer:

```text
1
```

```text
fruits = [1, 1, 1]
```

Answer:

```text
3
```

```text
fruits = [1, 2, 1]
```

Answer:

```text
3
```

```text
fruits = [1, 2, 3]
```

Answer:

```text
2
```

---

## Complexity

```text
Time: O(n)
Space: O(1)
```

Space is technically `O(3)` during processing because the map may temporarily hold 3 fruit types before shrinking.
