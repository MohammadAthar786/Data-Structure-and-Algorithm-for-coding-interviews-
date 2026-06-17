## LC 242 — Valid Anagram

**Pattern:** HashMap / Frequency Count

### 1. Pattern Recognition

Clue:

```text
Need to check whether two strings contain the same characters
Order does not matter
Frequency matters
```

So this is a **frequency counting** problem.

Example:

```text
s = "anagram"
t = "nagaram"

Both have:
a -> 3
n -> 1
g -> 1
r -> 1
m -> 1
```

So they are anagrams.

---

### 2. Naive Idea

Sort both strings and compare.

```text
s = "anagram" -> "aaagmnr"
t = "nagaram" -> "aaagmnr"
```

If sorted strings are equal, answer is true.

```cpp
bool isAnagram(string s, string t) {
    sort(s.begin(), s.end());
    sort(t.begin(), t.end());

    return s == t;
}
```

Time:

```text
O(N log N)
```

This works, but HashMap can do better.

---

### 3. Intuition Shift

Instead of rearranging the strings, just count characters.

Think like this:

```text
For s, add frequency
For t, remove frequency
```

Example:

```text
s = "rat"
t = "car"

After s:
r:+1, a:+1, t:+1

After t:
c:-1, a:0, r:0, t:+1
```

Counts are not all zero, so not anagram.

---

### 4. Conceptual Algorithm

```text
If lengths are different:
    return false

Create frequency array/hashmap

For each character in s:
    increase count

For each character in t:
    decrease count

If every count is zero:
    return true
Else:
    return false
```

---

### 5. Edge Cases & Complexity

Edge cases:

```text
s = "", t = ""              true
s = "a", t = "a"            true
s = "a", t = "b"            false
s = "ab", t = "a"           false
s = "aacc", t = "ccac"      false
```

Complexity:

```text
Time: O(N)
Space: O(1)
```

Space is O(1) because lowercase English letters are only 26.

---

## Beginner-Friendly C++ Code

```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.length() != t.length()) {
            return false;
        }

        vector<int> freq(26, 0);

        for (char ch : s) {
            freq[ch - 'a']++;
        }

        for (char ch : t) {
            freq[ch - 'a']--;
        }

        for (int count : freq) {
            if (count != 0) {
                return false;
            }
        }

        return true;
    }
};
```

### Recall Line

```text
Anagram = same character frequency, order does not matter.
```

## LC 383 — Ransom Note (Practice on your own )

**Pattern:** HashMap / Frequency Count

### 1. Pattern Recognition

Clue:

```text
Can we construct ransomNote using letters from magazine?
Each magazine letter can be used only once.
```

This means:

```text
Need to check availability of characters
Frequency matters
Order does not matter
```

So this is a **HashMap / frequency counting** problem.

---

### 2. Naive Starting Point

For every character in `ransomNote`, search it inside `magazine`.

Example:

```text
ransomNote = "aa"
magazine   = "ab"
```

We find first `a`, but second `a` is not available.

Naive C++ snippet:

```cpp
bool canConstruct(string ransomNote, string magazine) {
    for (char ch : ransomNote) {
        bool found = false;

        for (int i = 0; i < magazine.size(); i++) {
            if (magazine[i] == ch) {
                magazine[i] = '#'; // mark used
                found = true;
                break;
            }
        }

        if (!found) return false;
    }

    return true;
}
```

Why inefficient?

```text
For each character of ransomNote, we scan magazine again.
Time = O(N * M)
```

---

### 3. Intuition Shift

The “aha” moment:

> Instead of searching every time, first count what magazine gives us.

Example:

```text
magazine = "aab"
```

Frequency:

```text
a -> 2
b -> 1
```

Now use letters for:

```text
ransomNote = "aa"
```

Process:

```text
Need a -> available 2, use one -> remaining 1
Need a -> available 1, use one -> remaining 0
```

Possible.

But:

```text
ransomNote = "aaa"
magazine = "aab"
```

Process:

```text
Need a -> 2 to 1
Need a -> 1 to 0
Need a -> 0, not possible
```

---

### 4. Conceptual Algorithm

```text
Create frequency array/hashmap for magazine

For each character in magazine:
    increase count

For each character in ransomNote:
    if count of character is 0:
        return false
    decrease count

return true
```

Core invariant:

```text
Magazine is our limited inventory.
RansomNote is our demand.
```

---

### 5. Edge Cases & Complexity

Edge cases:

```text
ransomNote = "", magazine = "abc"      true
ransomNote = "a", magazine = ""        false
ransomNote = "aa", magazine = "ab"     false
ransomNote = "aa", magazine = "aab"    true
```

Complexity:

```text
Time: O(N + M)
Space: O(1)
```

Space is O(1) because lowercase English letters are only 26.

---

## Beginner-Friendly C++ Code

```cpp
class Solution {
public:
    bool canConstruct(string ransomNote, string magazine) {
        vector<int> freq(26, 0);

        for (char ch : magazine) {
            freq[ch - 'a']++;
        }

        for (char ch : ransomNote) {
            if (freq[ch - 'a'] == 0) {
                return false;
            }

            freq[ch - 'a']--;
        }

        return true;
    }
};
```

### Recall Line

```text
Magazine = inventory, ransomNote = demand.
Count first, then consume.
```

## LC 347 — Top K Frequent Elements

**Pattern:** HashMap + Heap / Bucket Sort

### 1. Pattern Recognition

Clue:

```text
Find k most frequent elements
Frequency matters
Order of original array does not matter
```

So first thought:

```text
Use HashMap to count frequency
Then select top k by frequency
```

This problem is not just HashMap. It is usually:

```text
HashMap + Min Heap
or
HashMap + Bucket Sort
```

---

### 2. Naive Starting Point

Count frequency, then repeatedly search for the max frequency element.

Example:

```text
nums = [1,1,1,2,2,3], k = 2

frequency:
1 -> 3
2 -> 2
3 -> 1
```

Naive idea:

```text
Find max frequency element -> 1
Remove it
Find next max frequency element -> 2
```

### Naive C++ snippet

```cpp
vector<int> topKFrequent(vector<int>& nums, int k) {
    unordered_map<int, int> freq;

    for (int num : nums) {
        freq[num]++;
    }

    vector<int> answer;

    while (k--) {
        int bestNum = 0;
        int bestFreq = -1;

        for (auto it : freq) {
            if (it.second > bestFreq) {
                bestFreq = it.second;
                bestNum = it.first;
            }
        }

        answer.push_back(bestNum);
        freq.erase(bestNum);
    }

    return answer;
}
```

Why inefficient?

```text
If there are M unique numbers:
Each time we scan M elements
We do this K times

Time = O(N + K*M)
Worst case can become O(N^2)
```

---

### 3. Intuition Shift

The “aha” moment:

> Frequency is the ranking key.
> We do not care about original positions.

After counting:

```text
nums = [1,1,1,2,2,3]

1 appears 3 times
2 appears 2 times
3 appears 1 time
```

Now the problem becomes:

```text
Pick k largest frequencies
```

There are two common optimized ways:

```text
Min Heap of size K:
Keep only top K frequent elements

Bucket Sort:
Frequency can only be from 1 to N
Put numbers into buckets by frequency
```

---

## Approach 1: HashMap + Min Heap

### 4. Conceptual Algorithm

```text
Count frequency using unordered_map

Create min heap based on frequency

For each number-frequency pair:
    Push into heap

    If heap size becomes greater than k:
        Remove the smallest frequency element

At the end, heap contains top k frequent elements
```

Why min heap?

```text
Because we want to quickly remove the least frequent among current candidates.
```

Example:

```text
freq:
1 -> 3
2 -> 2
3 -> 1
k = 2
```

Process:

```text
push (3,1) -> heap: 1
push (2,2) -> heap: 1,2
push (1,3) -> heap size 3, remove frequency 1

Remaining: 1 and 2
```

### C++ Code — Min Heap

```cpp
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> freq;

        for (int num : nums) {
            freq[num]++;
        }

        priority_queue<
            pair<int, int>,
            vector<pair<int, int>>,
            greater<pair<int, int>>
        > minHeap;

        for (auto it : freq) {
            int num = it.first;
            int count = it.second;

            minHeap.push({count, num});

            if (minHeap.size() > k) {
                minHeap.pop();
            }
        }

        vector<int> answer;

        while (!minHeap.empty()) {
            answer.push_back(minHeap.top().second);
            minHeap.pop();
        }

        return answer;
    }
};
```

Complexity:

```text
Time: O(N log K)
Space: O(N)
```

---

## Approach 2: HashMap + Bucket Sort

### Intuition

Frequency cannot be bigger than `nums.size()`.

So create buckets:

```text
bucket[frequency] = list of numbers with that frequency
```

Example:

```text
nums = [1,1,1,2,2,3]

1 -> freq 3
2 -> freq 2
3 -> freq 1
```

Buckets:

```text
bucket[1] = [3]
bucket[2] = [2]
bucket[3] = [1]
```

Now scan buckets from high frequency to low frequency:

```text
bucket[3] -> pick 1
bucket[2] -> pick 2
```

Answer:

```text
[1, 2]
```

### C++ Code — Bucket Sort

```cpp
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> freq;

        for (int num : nums) {
            freq[num]++;
        }

        vector<vector<int>> bucket(nums.size() + 1);

        for (auto it : freq) {
            int num = it.first;
            int count = it.second;

            bucket[count].push_back(num);
        }

        vector<int> answer;

        for (int i = nums.size(); i >= 1; i--) {
            for (int num : bucket[i]) {
                answer.push_back(num);

                if (answer.size() == k) {
                    return answer;
                }
            }
        }

        return answer;
    }
};
```

Complexity:

```text
Time: O(N)
Space: O(N)
```

---

### 5. Edge Cases

```text
nums = [1], k = 1
answer = [1]

nums = [1,1,2,2,3], k = 2
answer can be [1,2]

nums = [4,4,4,6,6,7], k = 1
answer = [4]

nums = [-1,-1,-2,-2,-2,3], k = 1
answer = [-2]
```

### Recall Line

```text
Count frequency first, then choose top K by frequency.
HashMap gives counts, Heap/Bucket gives ranking.
```

## LC 692 — Top K Frequent Words

**Pattern:** HashMap + Heap / Sorting

Very similar to **LC 347**, but with one extra rule:

```text
If two words have same frequency,
word with smaller lexicographical order comes first.
```

Example:

```text
words = ["i","love","leetcode","i","love","coding"]
k = 2

frequency:
i        -> 2
love     -> 2
leetcode -> 1
coding   -> 1

answer = ["i", "love"]
```

Because both `i` and `love` have frequency `2`, and `"i"` comes before `"love"`.

---

## 1. Pattern Recognition

Clues:

```text
Top K
Frequent
Words
Tie-breaking by lexicographical order
```

So the pattern is:

```text
HashMap for frequency count
Heap or sorting for ranking
```

Ranking rule:

```text
Higher frequency first
If same frequency, lexicographically smaller word first
```

---

## 2. Naive Starting Point

Count frequencies, then repeatedly find the best word.

```cpp
vector<string> topKFrequent(vector<string>& words, int k) {
    unordered_map<string, int> freq;

    for (string word : words) {
        freq[word]++;
    }

    vector<string> answer;

    while (k--) {
        string bestWord = "";
        int bestFreq = -1;

        for (auto it : freq) {
            string word = it.first;
            int count = it.second;

            if (count > bestFreq ||
                (count == bestFreq && word < bestWord)) {
                bestFreq = count;
                bestWord = word;
            }
        }

        answer.push_back(bestWord);
        freq.erase(bestWord);
    }

    return answer;
}
```

Problem:

```text
Each time we scan all unique words.
If there are M unique words:

Time = O(K * M)
```

Too much repeated work.

---

## 3. Intuition Shift

The “aha” moment:

> After counting, this is not a string problem anymore.
> It becomes a ranking problem.

For each word, we have:

```text
(word, frequency)
```

Now we sort/rank using:

```text
frequency descending
word ascending
```

Example:

```text
words = ["the","day","is","sunny","the","the","the","sunny","is","is"]
k = 4
```

Frequency:

```text
the   -> 4
is    -> 3
sunny -> 2
day   -> 1
```

Ranking:

```text
the, is, sunny, day
```

Answer:

```text
["the","is","sunny","day"]
```

---

## 4. Conceptual Algorithm

### Sorting version

```text
Count frequency of every word using hashmap

Put all unique words into a vector

Sort words by:
    higher frequency first
    if same frequency, lexicographically smaller first

Take first k words
```

This is easiest for interviews.

---

## Beginner-Friendly C++ Code — Sorting

```cpp
class Solution {
public:
    vector<string> topKFrequent(vector<string>& words, int k) {
        unordered_map<string, int> freq;

        for (string word : words) {
            freq[word]++;
        }

        vector<string> uniqueWords;

        for (auto it : freq) {
            uniqueWords.push_back(it.first);
        }

        sort(uniqueWords.begin(), uniqueWords.end(), [&](string a, string b) {
            if (freq[a] == freq[b]) {
                return a < b;
            }

            return freq[a] > freq[b];
        });

        vector<string> answer;

        for (int i = 0; i < k; i++) {
            answer.push_back(uniqueWords[i]);
        }

        return answer;
    }
};
```

---

## Heap Version Intuition

Heap version is a bit tricky because of tie-breaking.

For final answer, we want:

```text
High frequency first
Lexicographically small first
```

But in a **min heap of size k**, we remove the “worst” candidate.

Worst candidate means:

```text
Lower frequency is worse
If same frequency, lexicographically larger word is worse
```

### Heap C++ Code

```cpp
class Solution {
public:
    struct Compare {
        bool operator()(pair<string, int>& a, pair<string, int>& b) {
            if (a.second == b.second) {
                return a.first < b.first;
            }

            return a.second > b.second;
        }
    };

    vector<string> topKFrequent(vector<string>& words, int k) {
        unordered_map<string, int> freq;

        for (string word : words) {
            freq[word]++;
        }

        priority_queue<
            pair<string, int>,
            vector<pair<string, int>>,
            Compare
        > minHeap;

        for (auto it : freq) {
            minHeap.push({it.first, it.second});

            if (minHeap.size() > k) {
                minHeap.pop();
            }
        }

        vector<string> answer;

        while (!minHeap.empty()) {
            answer.push_back(minHeap.top().first);
            minHeap.pop();
        }

        reverse(answer.begin(), answer.end());

        return answer;
    }
};
```

---

## 5. Edge Cases & Complexity

Edge cases:

```text
words = ["a"], k = 1
answer = ["a"]

words = ["a","b"], k = 2
answer = ["a","b"]

words = ["b","a"], k = 2
answer = ["a","b"]

words = ["aaa","aa","a"], k = 2
all frequency 1
answer = ["a","aa"]
```

Complexity:

Sorting:

```text
Time: O(N + M log M)
Space: O(M)
```

Heap:

```text
Time: O(N + M log K)
Space: O(M + K)
```

Where:

```text
N = total words
M = unique words
```

### Recall Line

```text
Count frequency first, then rank by:
frequency high first, word small first.
```

## LC 767 — Reorganize String

**Pattern:** HashMap + Max Heap / Greedy

Goal:

```text
Rearrange string so no two adjacent characters are same.
If impossible, return "".
```

Example:

```text
s = "aab"
answer = "aba"
```

---

## 1. Pattern Recognition

Clues:

```text
Need rearrangement
Same characters should not be adjacent
Frequency matters
Always choose best available character
```

So pattern is:

```text
HashMap for frequency
Max Heap for greedy selection
```

Why max heap?

```text
We should place the most frequent character first,
but avoid placing the same character twice continuously.
```

---

## 2. Naive Starting Point

Try all possible permutations and check which one is valid.

Example:

```text
s = "aab"

permutations:
aab  invalid
aba  valid
baa  invalid
```

Problem:

```text
Total permutations can be factorial level.
Time ≈ O(N!)
```

Too slow.

---

## 3. Intuition Shift

The “aha” moment:

> The character with highest frequency is the biggest danger.

Example:

```text
s = "aaabbc"

freq:
a -> 3
b -> 2
c -> 1
```

We should not delay `a` too much, because it may become impossible later.

Greedy idea:

```text
Pick the most frequent available character,
but not the same as previous character.
```

Visual:

```text
result = ""

pick a -> result = "a"
cannot pick a again
pick b -> result = "ab"
pick a -> result = "aba"
pick b -> result = "abab"
pick a -> result = "ababa"
pick c -> result = "ababac"
```

Valid.

---

## 4. Conceptual Algorithm

```text
Count frequency of every character

Put all characters into max heap by frequency

While heap has at least 2 characters:
    Pop most frequent char
    Pop second most frequent char

    Add both to answer

    Decrease both frequencies

    If still remaining, push back into heap

If one character remains:
    If its frequency is more than 1:
        return ""
    Else:
        add it to answer

Return answer
```

Why pop two characters?

```text
Because placing two different characters together prevents adjacency conflict.
```

---

## Beginner-Friendly C++ Code

```cpp
class Solution {
public:
    string reorganizeString(string s) {
        unordered_map<char, int> freq;

        for (char ch : s) {
            freq[ch]++;
        }

        priority_queue<pair<int, char>> maxHeap;

        for (auto it : freq) {
            maxHeap.push({it.second, it.first});
        }

        string answer = "";

        while (maxHeap.size() >= 2) {
            auto first = maxHeap.top();
            maxHeap.pop();

            auto second = maxHeap.top();
            maxHeap.pop();

            answer += first.second;
            answer += second.second;

            first.first--;
            second.first--;

            if (first.first > 0) {
                maxHeap.push(first);
            }

            if (second.first > 0) {
                maxHeap.push(second);
            }
        }

        if (!maxHeap.empty()) {
            auto last = maxHeap.top();

            if (last.first > 1) {
                return "";
            }

            answer += last.second;
        }

        return answer;
    }
};
```

---

## 5. Edge Cases & Complexity

Edge cases:

```text
s = "a"       answer = "a"
s = "aa"      answer = ""
s = "aab"     answer = "aba"
s = "aaab"    answer = ""
s = "vvvlo"   possible answer = "vlvov"
```

Important impossible condition:

```text
If max frequency > (n + 1) / 2, answer is impossible.
```

Complexity:

```text
Time: O(N log 26) ≈ O(N)
Space: O(26) ≈ O(1)
```

For general characters:

```text
Time: O(N log K)
Space: O(K)
```

### Recall Line

```text
Most frequent character is the danger.
Use max heap and always place two different characters.
```
