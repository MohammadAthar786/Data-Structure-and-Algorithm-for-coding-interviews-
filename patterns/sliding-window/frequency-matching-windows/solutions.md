## LC 438 — Find All Anagrams in a String

https://youtu.be/XxeOHMwBQZU?si=tNLzlCEt3TvIlY-g

This is a **Fixed-Size Frequency Window** problem.

We need to find all substrings of `s` that are anagrams of `p`.

Meaning:

```text
same characters
same frequencies
different order allowed
```

---

## Pattern Recognition

Clues:

```text
find all anagrams
substring
same length as p
character frequency matching
```

Important:

If substring is an anagram of `p`, then its length must be:

```text
p.length()
```

So this becomes:

```text
Check every fixed-size window of length p.size()
and compare its character frequency with p.
```

---

## Naive Approach

Example:

```text
s = "cbaebabacd"
p = "abc"
```

Check every substring of length `3`:

```text
"cba" -> anagram
"bae" -> no
"aeb" -> no
"eba" -> no
"bab" -> no
"aba" -> no
"bac" -> anagram
"acd" -> no
```

Answer:

```text
[0, 6]
```

Naive problem:

For every window, we rebuild the frequency count again.

That repeats work.

---

## Intuition Shift

When window slides by one step:

```text
old window: c b a
new window: b a e
```

Only two changes happen:

```text
remove c
add e
```

So instead of rebuilding frequency every time, update the existing window frequency.

---

## Conceptual Algorithm

```text
1. Create frequency array for p.
2. Create frequency array for current window in s.
3. Maintain window of size p.length().
4. For every right pointer:
      add s[right] to window frequency

      if window size becomes bigger than p.length():
          remove s[left]
          left++

      if window size equals p.length() and frequencies match:
          store left index
```

Since lowercase English letters are used, use:

```text
vector<int> freq(26)
```

---

## Human C++ Code

```cpp
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        vector<int> result;

        int n = s.size();
        int k = p.size();

        if (k > n) {
            return result;
        }

        vector<int> pFreq(26, 0);
        vector<int> windowFreq(26, 0);

        for (char ch : p) {
            pFreq[ch - 'a']++;
        }

        int left = 0;

        for (int right = 0; right < n; right++) {
            windowFreq[s[right] - 'a']++;

            if (right - left + 1 > k) {
                windowFreq[s[left] - 'a']--;
                left++;
            }

            if (right - left + 1 == k && windowFreq == pFreq) {
                result.push_back(left);
            }
        }

        return result;
    }
};
```

---

## Dry Run

```text
s = "cbaebabacd"
p = "abc"
k = 3
```

First valid-size window:

```text
window = "cba"
freq matches "abc"
store index 0
```

Slide:

```text
remove c, add e
window = "bae"
no match
```

Later:

```text
window = "bac"
freq matches "abc"
store index 6
```

Answer:

```text
[0, 6]
```

---

## Edge Cases

```text
s smaller than p
```

Return empty list.

```text
s = "abab", p = "ab"
```

Windows:

```text
"ab" -> index 0
"ba" -> index 1
"ab" -> index 2
```

Answer:

```text
[0, 1, 2]
```

---

## Complexity

```text
Time: O(n * 26)
Space: O(26)
```

Because comparing two arrays costs `26`.

Since `26` is constant:

```text
Time: O(n)
Space: O(1)
```

## LC 567 — Permutation in String

https://youtu.be/iTwwvsyUsi4?si=6TtkSn1PYQQdYmYL

This is almost the same as **LC 438**.

LC 438:

```text
Find all anagram starting indices
```

LC 567:

```text
Return true if any anagram exists
```

So here we only need to find **one valid window**.

---

## Pattern Recognition

Problem says:

```text
Check if s2 contains a permutation of s1
```

Permutation means:

```text
same characters
same frequency
different order allowed
```

So the real question is:

```text
Does s2 contain any substring of length s1.size()
whose frequency matches s1?
```

This is a **fixed-size frequency window** problem.

---

## Naive Approach

Example:

```text
s1 = "ab"
s2 = "eidbaooo"
```

Check every substring of length `2`:

```text
"ei" -> no
"id" -> no
"db" -> no
"ba" -> yes
```

Return:

```text
true
```

Naive issue:

For every substring, we build frequency again.

That repeats work.

---

## Intuition Shift

Keep one sliding window of size `s1.length()`.

When the window moves:

```text
old window: e i
new window: i d
```

Only two things change:

```text
remove e
add d
```

So update frequency instead of rebuilding.

---

## Conceptual Algorithm

```text
1. Let k = s1.length().
2. Build frequency array for s1.
3. Slide a window of size k over s2.
4. Add s2[right] to window frequency.
5. If window size becomes greater than k:
      remove s2[left]
      left++
6. Whenever window size == k:
      compare both frequency arrays.
      if equal, return true.
7. If no window matches, return false.
```

---

## Human C++ Code

```cpp
class Solution {
public:
    bool checkInclusion(string s1, string s2) {
        int k = s1.size();
        int n = s2.size();

        if (k > n) {
            return false;
        }

        vector<int> s1Freq(26, 0);
        vector<int> windowFreq(26, 0);

        for (char ch : s1) {
            s1Freq[ch - 'a']++;
        }

        int left = 0;

        for (int right = 0; right < n; right++) {
            windowFreq[s2[right] - 'a']++;

            if (right - left + 1 > k) {
                windowFreq[s2[left] - 'a']--;
                left++;
            }

            if (right - left + 1 == k && windowFreq == s1Freq) {
                return true;
            }
        }

        return false;
    }
};
```

---

## Dry Run

```text
s1 = "ab"
s2 = "eidbaooo"
```

```text
window = "ei" -> no
window = "id" -> no
window = "db" -> no
window = "ba" -> yes
```

Return:

```text
true
```

---

## Edge Cases

```text
s1 bigger than s2
```

Return:

```text
false
```

```text
s1 = "ab", s2 = "eidboaoo"
```

No `"ab"` or `"ba"` exists.

Return:

```text
false
```

```text
s1 = "a", s2 = "a"
```

Return:

```text
true
```

---

## Complexity

```text
Time: O(n * 26)
Space: O(26)
```

Since 26 is constant:

```text
Time: O(n)
Space: O(1)
```

## LC 76 — Minimum Window Substring

https://youtu.be/3Bp3OVD1EGc?si=t59g0WKT-IXfO8or

This is one of the most important **Variable-Size Frequency Matching Window** problems.

LC 438 / LC 567:

```text
fixed window size
exact frequency match
```

LC 76:

```text
variable window size
minimum window that contains all required characters
```

---

## Pattern Recognition

Problem says:

```text
Find the minimum window substring of s that contains all characters of t.
```

Clues:

```text
substring
minimum length
contains all characters
frequency matters
```

Important:

If `t = "AABC"`, then valid window must contain:

```text
A -> 2 times
B -> 1 time
C -> 1 time
```

So this is not just set matching.

We need frequency matching.

---

## Naive Approach

Try every substring of `s`.

For each substring, check whether it contains all characters of `t`.

Example:

```text
s = "ADOBECODEBANC"
t = "ABC"
```

Check:

```text
"A" -> invalid
"AD" -> invalid
"ADO" -> invalid
...
"ADOBEC" -> valid
...
"BANC" -> valid and smaller
```

Problem:

You repeatedly recount characters for many overlapping substrings.

Time can become:

```text
O(n²) or worse
```

---

## Intuition Shift

We use two actions:

```text
1. Expand right until window becomes valid.
2. Shrink left while window is still valid.
```

That is the key difference.

For minimum window problems:

```text
Expand to satisfy.
Shrink to optimize.
```

Example:

```text
s = "ADOBECODEBANC"
t = "ABC"
```

Expand until first valid window:

```text
[ADOBEC]
```

It contains:

```text
A, B, C
```

Now shrink from left:

```text
remove A -> invalid
```

So `"ADOBEC"` is one candidate.

Continue expanding until valid again:

```text
[CODEBA]
```

Shrink again.

Eventually:

```text
[BANC]
```

This is the minimum valid window.

---

## Important State

We keep:

```text
need = frequency map of t
window = frequency map of current window
required = number of unique chars needed
formed = number of unique chars currently satisfied
```

Example:

```text
t = "ABC"
need = {A:1, B:1, C:1}
required = 3
```

If current window has:

```text
A:1, B:1, C:1
```

Then:

```text
formed = 3
```

Window is valid when:

```text
formed == required
```

For duplicate chars:

```text
t = "AABC"
need = {A:2, B:1, C:1}
```

A is satisfied only when:

```text
window['A'] == 2
```

---

## Conceptual Algorithm

```text
1. Build need map from t.
2. left = 0
3. formed = 0
4. bestLen = infinity
5. bestStart = 0

6. Move right from 0 to s.length - 1:
      add s[right] into window

      if s[right] is needed and its count becomes exactly need count:
          formed++

      while formed == required:
          update best answer with current window

          remove s[left] from window

          if s[left] is needed and its count becomes less than need count:
              formed--

          left++

7. If bestLen is still infinity:
      return ""
   else:
      return substring(bestStart, bestLen)
```

---

## Human C++ Code

```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        if (t.size() > s.size()) {
            return "";
        }

        unordered_map<char, int> need;
        unordered_map<char, int> window;

        for (char ch : t) {
            need[ch]++;
        }

        int required = need.size();
        int formed = 0;

        int left = 0;
        int bestLen = INT_MAX;
        int bestStart = 0;

        for (int right = 0; right < s.size(); right++) {
            char ch = s[right];
            window[ch]++;

            if (need.count(ch) && window[ch] == need[ch]) {
                formed++;
            }

            while (formed == required) {
                int windowSize = right - left + 1;

                if (windowSize < bestLen) {
                    bestLen = windowSize;
                    bestStart = left;
                }

                char leftChar = s[left];
                window[leftChar]--;

                if (need.count(leftChar) && window[leftChar] < need[leftChar]) {
                    formed--;
                }

                left++;
            }
        }

        if (bestLen == INT_MAX) {
            return "";
        }

        return s.substr(bestStart, bestLen);
    }
};
```

---

## Dry Run

```text
s = "ADOBECODEBANC"
t = "ABC"
```

First valid window:

```text
"ADOBEC"
```

Then keep searching and shrinking.

Later valid window:

```text
"EBANC"
```

Shrink:

```text
"EBANC" remove E -> "BANC" still valid
"BANC" remove B -> invalid
```

Best answer:

```text
"BANC"
```

---

## Edge Cases

```text
t.size() > s.size()
```

Return:

```text
""
```

```text
s = "a", t = "a"
```

Return:

```text
"a"
```

```text
s = "a", t = "aa"
```

Return:

```text
""
```

```text
s = "aa", t = "aa"
```

Return:

```text
"aa"
```

---

## Complexity

```text
Time: O(n)
Space: O(k)
```

Where `k` is number of unique characters involved.

Each pointer moves forward only once.

## LC 1234 — Replace the Substring for Balanced String

This is an advanced **variable-size frequency window** problem.

Characters are only:

```text
Q, W, E, R
```

A string is balanced if each character appears exactly:

```text
n / 4
```

times.

---

## Core Pattern Recognition

Problem says:

```text
Replace one substring to make the whole string balanced.
Find minimum length of substring.
```

Important clues:

```text
substring
minimum length
frequency balance
replace
```

The tricky part:

We are not directly checking the inside substring.

We check the characters **outside** the substring.

Because if we replace some substring, the outside part remains fixed.

So we need to find the smallest window such that after removing/replacing it:

```text
outside counts of Q, W, E, R are all <= n / 4
```

Why `<= n/4`?

Because the replacement substring can provide the missing characters.

---

## Naive Approach

Try every substring and ask:

```text
If I replace this substring, can the remaining outside part become balanced?
```

Example:

```text
s = "WQWRQQQW"
```

For each substring:

```text
remove it temporarily
check remaining counts
```

Problem:

Too many substrings.

Time:

```text
O(n²)
```

---

## Intuition Shift

Instead of thinking:

```text
What should I put inside the replacement substring?
```

Think:

```text
What must I remove from the original string so the outside is already not over-limit?
```

Example:

```text
s = "QQWE"
n = 4
required = 1
```

Counts:

```text
Q = 2
W = 1
E = 1
R = 0
```

Only `Q` is extra.

If we replace one `Q`, outside becomes:

```text
Q = 1
W = 1
E = 1
R = 0
```

Now outside is okay because replacement can add missing `R`.

Answer:

```text
1
```

---

## Key Validity Condition

Let `count` store characters **outside the current window**.

When window expands, we remove that character from outside count:

```text
count[s[right]]--
```

Current window is replaceable if:

```text
count['Q'] <= required
count['W'] <= required
count['E'] <= required
count['R'] <= required
```

Then we try to shrink from left to make replacement window smaller.

---

## Conceptual Algorithm

```text
1. Count all characters in s.
2. required = n / 4.
3. If all counts already equal required:
      return 0.

4. left = 0
5. ans = n

6. For right from 0 to n - 1:
      count[s[right]]--

      while outside counts are all <= required:
          update ans with current window size

          count[s[left]]++
          left++

7. Return ans
```

Meaning:

```text
window = substring we plan to replace
count = characters outside the window
```

---

## Human C++ Code

```cpp
class Solution {
public:
    int balancedString(string s) {
        int n = s.size();
        int required = n / 4;

        unordered_map<char, int> count;

        for (char ch : s) {
            count[ch]++;
        }

        if (count['Q'] == required &&
            count['W'] == required &&
            count['E'] == required &&
            count['R'] == required) {
            return 0;
        }

        int left = 0;
        int ans = n;

        for (int right = 0; right < n; right++) {
            count[s[right]]--;

            while (count['Q'] <= required &&
                   count['W'] <= required &&
                   count['E'] <= required &&
                   count['R'] <= required) {

                ans = min(ans, right - left + 1);

                count[s[left]]++;
                left++;
            }
        }

        return ans;
    }
};
```

---

## Dry Run

```text
s = "QQWE"
required = 1
```

Initial count:

```text
Q = 2, W = 1, E = 1, R = 0
```

Move right:

```text
right = 0, remove Q from outside
outside count:
Q = 1, W = 1, E = 1, R = 0
```

Now all counts are `<= 1`, so current window:

```text
"Q"
```

can be replaced.

Answer:

```text
1
```

---

## Edge Cases

Already balanced:

```text
s = "QWER"
```

Return:

```text
0
```

All same character:

```text
s = "QQQQ"
```

Required:

```text
1
```

Need replace 3 characters.

Return:

```text
3
```

Example with missing character:

```text
s = "QQWE"
```

`R` is missing, but that is okay.

We only care whether outside has any character over the limit.

---

## Complexity

```text
Time: O(n)
Space: O(1)
```

Space is constant because only four characters are tracked.
