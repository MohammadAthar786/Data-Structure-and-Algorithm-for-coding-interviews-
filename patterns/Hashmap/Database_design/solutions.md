## LC 380 — Insert Delete GetRandom O(1)

https://youtu.be/3yTnLrNdJGo?si=z2hbV7MSK7f88Id0

**Pattern:** HashMap + Dynamic Array

Goal:

```text
insert(val)     -> O(1)
remove(val)     -> O(1)
getRandom()     -> O(1)
```

---

## 1. Pattern Recognition

Clues:

```text
Need insert in O(1)
Need delete in O(1)
Need random element in O(1)
```

Normal structures fail:

```text
vector: random O(1), insert end O(1), but delete middle O(N)
set/hashset: insert/delete O(1), but getRandom not O(1)
```

So we combine both:

```text
vector + unordered_map
```

---

## 2. Core Intuition

Use:

```text
vector stores values
map stores value -> index in vector
```

Example:

```text
values = [10, 20, 30]
indexMap:
10 -> 0
20 -> 1
30 -> 2
```

Now:

```text
getRandom() = values[random index]
```

---

## 3. Hard Part: Remove in O(1)

Deleting from middle of vector is costly.

Example remove `20`:

```text
values = [10, 20, 30]
```

Instead of shifting, swap with last:

```text
swap 20 with 30
values = [10, 30, 20]
```

Now remove last:

```text
values = [10, 30]
```

Update map:

```text
30 -> 1
delete 20
```

This makes delete O(1).

---

## 4. Conceptual Algorithm

### insert(val)

```text
If val already exists:
    return false

Push val into vector
Store val -> last index in map
return true
```

### remove(val)

```text
If val does not exist:
    return false

Find index of val
Get last element

Move last element to val's index
Update last element's index in map

Pop last element from vector
Delete val from map
return true
```

### getRandom()

```text
Pick random index from 0 to vector.size() - 1
Return vector[index]
```

---

## 5. Beginner-Friendly C++ Code

```cpp
class RandomizedSet {
private:
    vector<int> nums;
    unordered_map<int, int> indexMap;

public:
    RandomizedSet() {

    }

    bool insert(int val) {
        if (indexMap.find(val) != indexMap.end()) {
            return false;
        }

        nums.push_back(val);
        indexMap[val] = nums.size() - 1;

        return true;
    }

    bool remove(int val) {
        if (indexMap.find(val) == indexMap.end()) {
            return false;
        }

        int indexToRemove = indexMap[val];
        int lastValue = nums.back();

        nums[indexToRemove] = lastValue;
        indexMap[lastValue] = indexToRemove;

        nums.pop_back();
        indexMap.erase(val);

        return true;
    }

    int getRandom() {
        int randomIndex = rand() % nums.size();
        return nums[randomIndex];
    }
};
```

---

## 6. Edge Cases

```text
insert(1)  -> true
insert(1)  -> false

remove(2)  -> false

remove last element works too:
nums = [10, 20]
remove(20)
```

Even if removing last element, the same logic works.

Complexity:

```text
insert: O(1)
remove: O(1)
getRandom: O(1)

Space: O(N)
```

### Recall Line

```text
RandomizedSet = vector for random access + hashmap for index lookup.
Delete by swap-with-last.
```

## LC 146 — LRU Cache

https://youtu.be/81h8O-0U5oo?si=-vI0Dlvs6wlTny3I

**Pattern:** HashMap + Doubly Linked List

Goal:

```text
get(key) -> O(1)
put(key, value) -> O(1)
Remove least recently used item when capacity is full
```

---

## 1. Pattern Recognition

Clues:

```text
Need fast lookup by key
Need fast delete/update
Need track usage order
Need remove least recently used
```

Normal structures fail:

```text
HashMap: fast lookup, but no order
Vector/List alone: order, but lookup is O(N)
```

So combine:

```text
unordered_map + doubly linked list
```

---

## 2. Core Intuition

Use:

```text
HashMap: key -> node address
Doubly Linked List: maintains usage order
```

Order idea:

```text
Most recently used near front
Least recently used near back
```

Example:

```text
capacity = 2

put(1,1) -> [1]
put(2,2) -> [2,1]

get(1)   -> [1,2]
put(3,3) -> remove 2 -> [3,1]
```

Because after `get(1)`, key `1` became recently used. So key `2` becomes least recently used.

---

## 3. Why Doubly Linked List?

When a key is used, we must move it to front.

For example:

```text
[4, 3, 2, 1]
get(2)
```

Now `2` should become most recent:

```text
[2, 4, 3, 1]
```

To remove `2` from middle in O(1), we need:

```text
prev pointer
next pointer
```

That is why we use a **doubly linked list**.

---

## 4. Conceptual Algorithm

### get(key)

```text
If key not present:
    return -1

Find node using hashmap
Move node to front
Return node value
```

### put(key, value)

```text
If key already exists:
    Update value
    Move node to front

Else:
    Create new node
    Add it to front
    Store in hashmap

    If size > capacity:
        Remove node from back
        Delete it from hashmap
```

Core invariant:

```text
Front = most recently used
Back = least recently used
```

---

## 5. Beginner-Friendly C++ Code

```cpp
class LRUCache {
private:
    class Node {
    public:
        int key;
        int value;
        Node* prev;
        Node* next;

        Node(int k, int v) {
            key = k;
            value = v;
            prev = NULL;
            next = NULL;
        }
    };

    int capacity;
    unordered_map<int, Node*> mp;

    Node* head;
    Node* tail;

    void removeNode(Node* node) {
        Node* before = node->prev;
        Node* after = node->next;

        before->next = after;
        after->prev = before;
    }

    void addToFront(Node* node) {
        Node* firstRealNode = head->next;

        head->next = node;
        node->prev = head;

        node->next = firstRealNode;
        firstRealNode->prev = node;
    }

    void moveToFront(Node* node) {
        removeNode(node);
        addToFront(node);
    }

public:
    LRUCache(int capacity) {
        this->capacity = capacity;

        head = new Node(-1, -1);
        tail = new Node(-1, -1);

        head->next = tail;
        tail->prev = head;
    }

    int get(int key) {
        if (mp.find(key) == mp.end()) {
            return -1;
        }

        Node* node = mp[key];
        moveToFront(node);

        return node->value;
    }

    void put(int key, int value) {
        if (mp.find(key) != mp.end()) {
            Node* node = mp[key];
            node->value = value;
            moveToFront(node);
            return;
        }

        Node* newNode = new Node(key, value);
        mp[key] = newNode;
        addToFront(newNode);

        if (mp.size() > capacity) {
            Node* lruNode = tail->prev;

            removeNode(lruNode);
            mp.erase(lruNode->key);

            delete lruNode;
        }
    }
};
```

---

## 6. Edge Cases

```text
capacity = 1

put(1,1) -> [1]
put(2,2) -> remove 1 -> [2]
get(1)   -> -1
get(2)   -> 2
```

```text
Updating existing key:

put(1,1)
put(1,10)

Do not create duplicate key.
Just update value and move key to front.
```

Complexity:

```text
get: O(1)
put: O(1)
Space: O(capacity)
```

### Recall Line

```text
LRU Cache = HashMap for access + Doubly Linked List for usage order.
Move every used key to front, remove from back.
```

## LC 460 — LFU Cache

https://youtu.be/-Vch0tHAsOM?si=AgOa00XTWvArSggU

**Pattern:** HashMap + Frequency Buckets + Doubly Linked List

Goal:

```text
get(key) -> O(1)
put(key, value) -> O(1)

Evict Least Frequently Used key.
If tie, evict Least Recently Used among same frequency.
```

---

## 1. Pattern Recognition

Clues:

```text
Need fast key lookup
Need frequency count of each key
Need remove minimum frequency key
Tie-breaker = LRU
```

So LFU needs **two levels of order**:

```text
Frequency order: lower freq removed first
Recency order inside same freq: older removed first
```

---

## 2. Core Intuition

For every key, store:

```text
key -> value
key -> frequency
```

But that is not enough.

We also need buckets:

```text
freq = 1 -> keys with frequency 1 in LRU order
freq = 2 -> keys with frequency 2 in LRU order
freq = 3 -> keys with frequency 3 in LRU order
```

Example:

```text
freq 1: [5, 7]
freq 2: [2, 9]
freq 3: [1]
```

If capacity full, remove from `minFreq` bucket:

```text
Remove least recent key from freq 1 bucket
```

---

## 3. Aha Moment

In LRU, every access moves key to front.

In LFU:

```text
Every access increases frequency by 1.
So key moves from freq bucket f to freq bucket f + 1.
```

Example:

```text
get(5)

Before:
freq 1: [5, 7]
freq 2: [2]

After:
freq 1: [7]
freq 2: [5, 2]
```

`5` frequency increased from `1` to `2`, and becomes most recent inside freq `2`.

---

## 4. Conceptual Algorithm

### get(key)

```text
If key does not exist:
    return -1

Increase key frequency:
    remove key from old frequency bucket
    add key to new frequency bucket

If old bucket was minFreq and becomes empty:
    minFreq++

Return value
```

### put(key, value)

```text
If capacity == 0:
    return

If key exists:
    update value
    increase key frequency
    return

If cache is full:
    remove least recent key from minFreq bucket

Insert new key:
    value = given value
    frequency = 1
    add key into freq 1 bucket
    minFreq = 1
```

Core invariant:

```text
minFreq always points to the lowest frequency currently present.
Inside each frequency bucket, order is LRU.
```

---

## 5. Beginner-Friendly C++ Code

Using `list<int>` for LRU order inside each frequency bucket.

```cpp
class LFUCache {
private:
    int capacity;
    int minFreq;

    unordered_map<int, int> keyToValue;
    unordered_map<int, int> keyToFreq;

    // freq -> list of keys with that frequency
    unordered_map<int, list<int>> freqToKeys;

    // key -> iterator pointing to its position in freqToKeys[freq]
    unordered_map<int, list<int>::iterator> keyToIterator;

    void increaseFreq(int key) {
        int oldFreq = keyToFreq[key];

        // Remove key from old frequency list
        freqToKeys[oldFreq].erase(keyToIterator[key]);

        // If old frequency bucket becomes empty and it was minFreq
        if (freqToKeys[oldFreq].empty()) {
            freqToKeys.erase(oldFreq);

            if (minFreq == oldFreq) {
                minFreq++;
            }
        }

        // Increase frequency
        int newFreq = oldFreq + 1;
        keyToFreq[key] = newFreq;

        // Add key to front of new frequency list = most recently used
        freqToKeys[newFreq].push_front(key);
        keyToIterator[key] = freqToKeys[newFreq].begin();
    }

public:
    LFUCache(int capacity) {
        this->capacity = capacity;
        this->minFreq = 0;
    }

    int get(int key) {
        if (keyToValue.find(key) == keyToValue.end()) {
            return -1;
        }

        increaseFreq(key);
        return keyToValue[key];
    }

    void put(int key, int value) {
        if (capacity == 0) {
            return;
        }

        if (keyToValue.find(key) != keyToValue.end()) {
            keyToValue[key] = value;
            increaseFreq(key);
            return;
        }

        if (keyToValue.size() == capacity) {
            // Evict least recently used key from minFreq bucket
            int keyToRemove = freqToKeys[minFreq].back();

            freqToKeys[minFreq].pop_back();

            keyToValue.erase(keyToRemove);
            keyToFreq.erase(keyToRemove);
            keyToIterator.erase(keyToRemove);
        }

        // Insert new key with frequency 1
        keyToValue[key] = value;
        keyToFreq[key] = 1;

        freqToKeys[1].push_front(key);
        keyToIterator[key] = freqToKeys[1].begin();

        minFreq = 1;
    }
};
```

---

## 6. Edge Cases

```text
capacity = 0
put(1,1)
get(1) -> -1
```

```text
capacity = 2

put(1,1)  freq: 1-> [1]
put(2,2)  freq: 1-> [2,1]
get(1)    freq: 1-> [2], freq: 2-> [1]
put(3,3)  remove key 2 because freq is lower

get(2) -> -1
get(3) -> 3
get(1) -> 1
```

Tie case:

```text
capacity = 2

put(1,1)
put(2,2)

Both freq = 1
put(3,3)

Remove key 1 because it is least recent among freq 1.
```

Complexity:

```text
get: O(1)
put: O(1)
Space: O(capacity)
```

### Recall Line

```text
LFU Cache = HashMap for key data + frequency buckets.
Evict from minFreq bucket, and inside that bucket evict LRU.
```
