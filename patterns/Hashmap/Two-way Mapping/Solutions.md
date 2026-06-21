## LC 205 — Isomorphic Strings

https://youtu.be/2ISNCDJEgqQ?si=svErjF5KVZRpF7sD

**Pattern:** HashMap / Two-way Mapping

Goal:

```text
Check whether characters in s can be replaced to get t.
```

Example:

```text
s = "egg"
t = "add"

e -> a
g -> d

Valid, because same character always maps to same character.
```

---

## 1. Pattern Recognition

Clues:

```text
Characters must follow same structure
One character maps to another character
Mapping must be consistent
No two characters can map to same character
```

So this is a **HashMap mapping consistency** problem.

Important rule:

```text
s[i] -> t[i] must be one-to-one
```

---

## 2. Naive Starting Point

For every index, check all previous indexes.

```text
s = "paper"
t = "title"

At every position:
Check whether same characters in s match same characters in t
```

Naive C++ snippet:

```cpp
bool isIsomorphic(string s, string t) {
    for (int i = 0; i < s.size(); i++) {
        for (int j = 0; j < i; j++) {
            if (s[i] == s[j] && t[i] != t[j]) {
                return false;
            }

            if (s[i] != s[j] && t[i] == t[j]) {
                return false;
            }
        }
    }

    return true;
}
```

Why inefficient?

```text
For every character, we scan previous characters.
Time = O(N^2)
```

---

## 3. Intuition Shift

The “aha” moment:

> Instead of repeatedly checking history, store the mapping.

Example:

```text
s = "foo"
t = "bar"
```

Process:

```text
f -> b
o -> a
o wants r, but o already maps to a

Invalid
```

Another example:

```text
s = "ab"
t = "aa"
```

Process:

```text
a -> a
b -> a

Invalid, because two different chars map to same char.
```

So we need two checks:

```text
s char should map consistently to t char
t char should not already be used by another s char
```

---

## 4. Conceptual Algorithm

```text
Create map1 for s -> t
Create map2 for t -> s

For every index i:
    c1 = s[i]
    c2 = t[i]

    If c1 already exists in map1:
        It must map to c2
        Otherwise return false

    If c2 already exists in map2:
        It must map to c1
        Otherwise return false

    Store both mappings

Return true
```

Core invariant:

```text
Mapping must be one-to-one and consistent.
```

---

## 5. Edge Cases & Complexity

Edge cases:

```text
s = "egg",   t = "add"     true
s = "foo",   t = "bar"     false
s = "paper", t = "title"   true
s = "ab",    t = "aa"      false
s = "badc",  t = "baba"    false
```

Complexity:

```text
Time: O(N)
Space: O(1)
```

Space is O(1) if character set is ASCII/fixed size.

---

## Beginner-Friendly C++ Code

```cpp
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        unordered_map<char, char> sToT;
        unordered_map<char, char> tToS;

        for (int i = 0; i < s.size(); i++) {
            char c1 = s[i];
            char c2 = t[i];

            if (sToT.find(c1) != sToT.end()) {
                if (sToT[c1] != c2) {
                    return false;
                }
            }

            if (tToS.find(c2) != tToS.end()) {
                if (tToS[c2] != c1) {
                    return false;
                }
            }

            sToT[c1] = c2;
            tToS[c2] = c1;
        }

        return true;
    }
};
```

### Recall Line

```text
Isomorphic = same structure.
Use two maps to enforce one-to-one mapping.
```

## LC 290 — Word Pattern

https://youtu.be/n0cn5tJwkaE?si=0G3JlVOgC5be3OHN

**Pattern:** HashMap / Two-way Mapping

Goal:

```text
pattern = "abba"
s = "dog cat cat dog"

answer = true
```

Because:

```text
a -> dog
b -> cat
b -> cat
a -> dog
```

---

## 1. Pattern Recognition

Clues:

```text
Pattern characters map to words
Same character must map to same word
Different characters cannot map to same word
Order/structure must match
```

This is very similar to **LC 205 Isomorphic Strings**, but here:

```text
character -> word
```

instead of:

```text
character -> character
```

---

## 2. Naive Starting Point

For every pattern character, check all previous positions.

Example:

```text
pattern = "abba"
words   = ["dog", "cat", "cat", "dog"]
```

Naive idea:

```cpp
bool wordPattern(string pattern, string s) {
    vector<string> words;
    stringstream ss(s);
    string word;

    while (ss >> word) {
        words.push_back(word);
    }

    if (pattern.size() != words.size()) {
        return false;
    }

    for (int i = 0; i < pattern.size(); i++) {
        for (int j = 0; j < i; j++) {
            if (pattern[i] == pattern[j] && words[i] != words[j]) {
                return false;
            }

            if (pattern[i] != pattern[j] && words[i] == words[j]) {
                return false;
            }
        }
    }

    return true;
}
```

Why inefficient?

```text
For every index, we scan previous indexes.
Time = O(N^2)
```

---

## 3. Intuition Shift

The “aha” moment:

> Do not repeatedly check old positions. Store the mapping.

Example:

```text
pattern = "abba"
s = "dog cat cat fish"
```

Process:

```text
a -> dog
b -> cat
b -> cat okay
a wants dog, but got fish
```

Invalid.

Another example:

```text
pattern = "abba"
s = "dog dog dog dog"
```

Process:

```text
a -> dog
b also wants dog
```

Invalid, because two pattern characters cannot map to same word.

So we need two checks:

```text
pattern char -> word should be consistent
word -> pattern char should be consistent
```

---

## 4. Conceptual Algorithm

```text
Split s into words

If pattern length != number of words:
    return false

Create map1: char -> string
Create map2: string -> char

For every index i:
    ch = pattern[i]
    word = words[i]

    If ch already exists in map1:
        it must map to current word
        otherwise return false

    If word already exists in map2:
        it must map to current ch
        otherwise return false

    Store both mappings

Return true
```

Core invariant:

```text
One pattern character maps to exactly one word.
One word maps back to exactly one pattern character.
```

---

## 5. Edge Cases & Complexity

Edge cases:

```text
pattern = "abba", s = "dog cat cat dog"     true
pattern = "abba", s = "dog cat cat fish"    false
pattern = "aaaa", s = "dog cat cat dog"     false
pattern = "abba", s = "dog dog dog dog"     false
pattern = "aaa",  s = "dog dog dog dog"     false
```

Complexity:

```text
Time: O(N)
Space: O(N)
```

`N` = number of words / pattern length.

---

## Beginner-Friendly C++ Code

```cpp
class Solution {
public:
    bool wordPattern(string pattern, string s) {
        vector<string> words;
        stringstream ss(s);
        string word;

        while (ss >> word) {
            words.push_back(word);
        }

        if (pattern.size() != words.size()) {
            return false;
        }

        unordered_map<char, string> charToWord;
        unordered_map<string, char> wordToChar;

        for (int i = 0; i < pattern.size(); i++) {
            char ch = pattern[i];
            string currentWord = words[i];

            if (charToWord.find(ch) != charToWord.end()) {
                if (charToWord[ch] != currentWord) {
                    return false;
                }
            }

            if (wordToChar.find(currentWord) != wordToChar.end()) {
                if (wordToChar[currentWord] != ch) {
                    return false;
                }
            }

            charToWord[ch] = currentWord;
            wordToChar[currentWord] = ch;
        }

        return true;
    }
};
```

### Recall Line

```text
Word Pattern = Isomorphic Strings with words.
Use two maps for one-to-one mapping.
```
