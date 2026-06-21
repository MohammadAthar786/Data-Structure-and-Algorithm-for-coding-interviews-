# Valid Palindrome 1

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isPalindrome(string s) {
        int left = 0;
        int right = s.size() - 1;

        while (left < right) {

            while (left < right && !isalnum(s[left])) {
                left++;
            }

            while (left < right && !isalnum(s[right])) {
                right--;
            }

            char a = tolower(s[left]);
            char b = tolower(s[right]);

            if (a != b) {
                return false;
            }

            left++;
            right--;
        }

        return true;
    }
};
```

# Valid Palindrome 2

# LC 680. Valid Palindrome II

https://youtu.be/wX3-411uJH0?si=6MD4N-eGcvakWl0e

**Pattern:** String-Based Two Pointers

Core idea:

```text
You are allowed to delete at most ONE character.
```

This is one of the most common Google/Amazon palindrome interview questions.

---

# 1. Core Pattern Recognition

Problem:

```text
Given a string s,
return true if it can become a palindrome
after deleting at most one character.
```

Example:

```text
s = "aba"
```

Already palindrome.

```text
true
```

---

Example:

```text
s = "abca"
```

Delete:

```text
'c'
```

Becomes:

```text
"aba"
```

Palindrome.

Answer:

```text
true
```

---

# Intuition Shift (Aha Moment)

Normal palindrome:

```text
left++
right--
```

until mismatch.

---

Example:

```text
abca
```

Pointers:

```text
a    a
↑    ↑
```

Match.

Move.

---

Now:

```text
b    c
↑    ↑
```

Mismatch.

Important observation:

```text
We can delete ONLY one character.
```

Therefore only two possibilities exist:

---

Delete left character:

````text
remove b
```
both pointer pointing c

Delete right character:

```text
remove c
```
both pointer pointing to b
---

Actually:

```text
abca
````

If remove:

```text
c
```

we get:

```text
aba
```

Palindrome.

---

## Aha Moment

At first mismatch:

```text
Either skip left
OR skip right
```

No other possibility matters.

---

# Visual Understanding

Example:

```text
abca
```

---

Initial:

```text
a b c a
L     R
```

Match.

Move inward.

---

Now:

```text
a b c a
  L R
```

Mismatch:

```text
b != c
```

Try:

```text
Skip b
```

Check:

```text
c
```

Palindrome.

---

Try:

```text
Skip c
```

Check:

```text
b
```

Palindrome.

Either works.

Answer:

```text
true
```

---

# 4. Conceptual Algorithm

---

### Step 1

Normal palindrome scan.

```text
left = 0
right = n-1
```

---

### Step 2

If characters match:

```text
left++
right--
```

Continue.

---

### Step 3

If mismatch occurs:

```text
s[left] != s[right]
```

Try BOTH:

```text
skip left
```

OR

```text
skip right
```

---

Check:

```text
isPalindrome(left+1,right)
```

OR

```text
isPalindrome(left,right-1)
```

---

If either succeeds:

```text
return true
```

Otherwise:

```text
return false
```

---

# Dry Run

Example:

```text
abca
```

---

Start:

```text
left=0
right=3

a == a
```

Move.

---

Now:

```text
left=1
right=2

b != c
```

Mismatch.

---

Option 1:

```text
skip b

check:

ca
```

Not palindrome.

---

Option 2:

```text
skip c

check:

ba
```

Wait carefully.

Actually substring becomes:

```text
aba
```

when viewed in original string.

Palindrome.

Return:

```text
true
```

---

# Another Example

```text
abc
```

---

Pointers:

```text
a != c
```

Try:

```text
bc
```

Not palindrome.

Try:

```text
ab
```

Not palindrome.

Return:

```text
false
```

---

# Why Only One Mismatch Matters

Because:

```text
Only one deletion allowed.
```

The first mismatch is where that deletion must happen.

So:

```text
skip left
or
skip right
```

That's it.

---

# 5. Edge Cases

### Already Palindrome

```text
racecar
```

Return:

```text
true
```

---

### One Character

```text
a
```

Return:

```text
true
```

---

### Two Characters

```text
ab
```

Delete either.

Return:

```text
true
```

---

### Impossible

```text
abc
```

Return:

```text
false
```

---

# Complexity

Main scan:

```text
O(N)
```

Palindrome helper:

```text
O(N)
```

Called at most twice.

Total:

```text
O(N)
```

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

    bool checkPalindrome(string &s, int left, int right) {

        while (left < right) {

            if (s[left] != s[right]) {
                return false;
            }

            left++;
            right--;
        }

        return true;
    }

    bool validPalindrome(string s) {

        int left = 0;
        int right = s.size() - 1;

        while (left < right) {

            if (s[left] == s[right]) {

                left++;
                right--;
            }
            else {

                return checkPalindrome(s, left + 1, right) ||
                       checkPalindrome(s, left, right - 1);
            }
        }

        return true;
    }
};
```

---
