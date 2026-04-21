# Day 1: Hashmaps + Arrays Foundation

Date: April 21, 2026
Label: day1_hashmaps_arrays
Focus: Hashmaps, arrays, fast lookup, frequency maps, complement lookup, and the first three LeetCode patterns.

## Goal For Today

By the end of Day 1, you should be able to look at an array or string problem and ask:

1. Do I need to remember values I have seen before?
2. Do I need to count how often values appear?
3. Do I need to quickly check if something exists?
4. Can a hashmap reduce an O(n^2) brute force solution to O(n)?

If the answer is yes, a hashmap is probably involved.

## Core Vocabulary

### Array

An array is an ordered collection of values.

In Python, LeetCode usually gives you a `list`.

```python
nums = [2, 7, 11, 15]
```

Important array facts:

- Arrays preserve order.
- You can access an index directly.
- Python list indexing is O(1).
- Scanning every element is O(n).
- Nested scans are usually O(n^2).

Example:

```python
nums = [10, 20, 30]

print(nums[0])  # 10
print(nums[1])  # 20
print(nums[2])  # 30
```

### Index

An index is the position of an element.

```python
nums = [10, 20, 30]
# index: 0   1   2
```

LeetCode often asks you to return indexes, not values. Read that carefully.

### Hashmap

A hashmap stores key-value pairs.

In Python, the hashmap data structure is called a dictionary, or `dict`.

```python
ages = {
    "Ada": 36,
    "Grace": 85,
}
```

The key is what you look up.
The value is the information stored at that key.

```python
ages["Ada"]  # 36
```

For DSA interviews, hashmaps are used mostly for:

- Fast lookup
- Counting frequency
- Remembering indexes
- Mapping one thing to another thing
- Avoiding repeated linear scans

### Set

A set stores unique values without associated values.

```python
seen = set()
seen.add(5)
```

Use a set when you only need to know whether a value exists.

Use a dict when you need to associate a value with extra information.

Example:

```python
# Set: only existence matters
seen_numbers = {1, 2, 3}

# Dict: key maps to useful data
index_by_number = {
    1: 0,
    2: 1,
    3: 2,
}
```

## Why Hashmaps Matter

Without a hashmap, you often search by scanning.

```python
nums = [4, 9, 2, 7]

target = 7
found = False

for num in nums:
    if num == target:
        found = True
```

That lookup is O(n), because in the worst case you may inspect every element.

With a set or dict:

```python
nums = [4, 9, 2, 7]
seen = set(nums)

target = 7
found = target in seen
```

The lookup `target in seen` is average O(1).

That is the core superpower: a hashmap lets you ask "Have I seen this before?" quickly.

## Time Complexity Essentials

### O(1)

O(1) means constant time.

The operation does not grow meaningfully as input size grows.

Examples:

```python
nums[0]        # list index lookup: O(1)
my_dict[key]   # dict lookup: average O(1)
value in seen  # set lookup: average O(1)
```

### O(n)

O(n) means linear time.

If there are n items, you may process each item once.

```python
for num in nums:
    print(num)
```

### O(n^2)

O(n^2) means quadratic time.

This usually comes from nested loops over the same input.

```python
for i in range(len(nums)):
    for j in range(i + 1, len(nums)):
        print(nums[i], nums[j])
```

For interviews, going from O(n^2) to O(n) is one of the most common wins.

## Hashmap Operations In Python

### Create a dict

```python
counts = {}
```

### Insert or update a key

```python
counts["a"] = 1
counts["a"] = 2
```

### Look up a key

```python
value = counts["a"]
```

Only do this if you know the key exists.

### Check if a key exists

```python
if "a" in counts:
    print("found it")
```

This is average O(1).

### Safely get a value

```python
count = counts.get("a", 0)
```

If `"a"` exists, this returns `counts["a"]`.
If `"a"` does not exist, this returns `0`.

### Count frequency manually

```python
counts = {}

for char in "banana":
    counts[char] = counts.get(char, 0) + 1

print(counts)
# {'b': 1, 'a': 3, 'n': 2}
```

### Delete or decrease counts

```python
counts = {"a": 3}

counts["a"] -= 1

if counts["a"] == 0:
    del counts["a"]
```

Deleting zero-count keys is sometimes useful when you want two maps to compare cleanly.

## Mental Model: Trading Space For Time

A hashmap often uses extra memory so the algorithm can run faster.

Example:

- Brute force Two Sum: no extra hashmap, O(n^2) time
- Hashmap Two Sum: extra hashmap, O(n) time

This is called a space-time tradeoff.

Interview translation:

"I can use a hashmap to store values I have seen so far, which gives me average O(1) lookups and reduces the total runtime to O(n)."

## Pattern 1: Frequency Map

Use this when the problem asks about:

- Counts
- Duplicates
- Same letters
- Same multiset of values
- "How many times?"
- Comparing whether two collections have the same contents

Template:

```python
freq = {}

for item in items:
    freq[item] = freq.get(item, 0) + 1
```

Example:

```python
nums = [1, 2, 2, 3, 3, 3]

freq = {}

for num in nums:
    freq[num] = freq.get(num, 0) + 1

print(freq)
# {1: 1, 2: 2, 3: 3}
```

Same example with iteration comments:

```python
nums = [1, 2, 2, 3, 3, 3]

freq = {}

# ITERATION 1
num = 1
freq[num] = freq.get(num, 0) + 1
# freq.get(1, 0) returns 0 because 1 is not in freq yet
# freq[1] becomes 0 + 1
# freq is now {1: 1}

# ITERATION 2
num = 2
freq[num] = freq.get(num, 0) + 1
# freq.get(2, 0) returns 0 because 2 is not in freq yet
# freq[2] becomes 0 + 1
# freq is now {1: 1, 2: 1}

# ITERATION 3
num = 2
freq[num] = freq.get(num, 0) + 1
# freq.get(2, 0) returns 1 because 2 already appears once
# freq[2] becomes 1 + 1
# freq is now {1: 1, 2: 2}

# ITERATION 4
num = 3
freq[num] = freq.get(num, 0) + 1
# freq.get(3, 0) returns 0 because 3 is not in freq yet
# freq[3] becomes 0 + 1
# freq is now {1: 1, 2: 2, 3: 1}

# ITERATION 5
num = 3
freq[num] = freq.get(num, 0) + 1
# freq.get(3, 0) returns 1
# freq[3] becomes 1 + 1
# freq is now {1: 1, 2: 2, 3: 2}

# ITERATION 6
num = 3
freq[num] = freq.get(num, 0) + 1
# freq.get(3, 0) returns 2
# freq[3] becomes 2 + 1
# freq is now {1: 1, 2: 2, 3: 3}
```

How to think:

- The key is the item.
- The value is how many times it appears.

## Pattern 2: Seen Set

Use this when the problem asks:

- Have I seen this value before?
- Is there a duplicate?
- Does a value already exist?

Template:

```python
seen = set()

for item in items:
    if item in seen:
        return True
    seen.add(item)

return False
```

Example with loop-focused iteration comments:

```python
items = [1, 2, 3, 1]

seen = set()

# This for loop visits the list from left to right.
# Each pass through the loop is one iteration.
for item in items:
    # ITERATION 1: item is 1
    # seen before checking: set()
    # 1 is not in seen, so do not return yet.
    # Add 1. seen becomes {1}.

    # ITERATION 2: item is 2
    # seen before checking: {1}
    # 2 is not in seen, so do not return yet.
    # Add 2. seen becomes {1, 2}.

    # ITERATION 3: item is 3
    # seen before checking: {1, 2}
    # 3 is not in seen, so do not return yet.
    # Add 3. seen becomes {1, 2, 3}.

    # ITERATION 4: item is 1
    # seen before checking: {1, 2, 3}
    # 1 is already in seen, so return True.

    if item in seen:
        return True

    seen.add(item)

return False
```

Important: the comments describe what happens on each pass, but the real code is only these lines:

```python
seen = set()

for item in items:
    if item in seen:
        return True

    seen.add(item)

return False
```

The loop itself changes `item` automatically:

```text
First pass:  item = 1
Second pass: item = 2
Third pass:  item = 3
Fourth pass: item = 1
```

How to think:

- The set contains everything from the past.
- For each current item, ask whether it already appeared.

## Pattern 3: Complement Lookup

Use this when:

- You need two values that combine to a target.
- You can compute the missing value.
- You want to know whether the missing value appeared earlier.

The key idea:

```text
current + needed = target
needed = target - current
```

Template:

```python
seen = {}

for i, num in enumerate(nums):
    complement = target - num

    if complement in seen:
        return [seen[complement], i]

    seen[num] = i
```

Example with iteration comments:

```python
nums = [2, 7, 11, 15]
target = 9

seen = {}

# ITERATION 1
i = 0
num = 2
complement = target - num
# complement = 9 - 2 = 7
# Is 7 in seen? No.
seen[num] = i
# Store 2 at index 0.
# seen is now {2: 0}

# ITERATION 2
i = 1
num = 7
complement = target - num
# complement = 9 - 7 = 2
# Is 2 in seen? Yes.
# seen[2] is 0.
return [seen[complement], i]
# return [0, 1]
```

How to think:

- At each number, calculate the number that would complete the pair.
- Look backward using the hashmap.
- Store the current number after checking, so you do not accidentally use the same element twice.

## Problem 1: Two Sum

LeetCode: 1. Two Sum
Difficulty: Easy
Pattern: Complement lookup
Core structure: Array + hashmap

### Problem Statement

Given an array of integers `nums` and an integer `target`, return the indexes of the two numbers such that they add up to `target`.

## Status

✅ I COMPLETED TWO SUM SUCCESSFULLY

You may assume:

- Exactly one solution exists.
- You cannot use the same element twice.
- You may return the answer in any order.

Example:

```python
nums = [2, 7, 11, 15]
target = 9

# nums[0] + nums[1] = 2 + 7 = 9
# return [0, 1]
```

### Brute Force Algorithm

Check every pair.

```python
class Solution:
    def twoSum(self, nums: list[int], target: int) -> list[int]:
        for i in range(len(nums)):
            for j in range(i + 1, len(nums)):
                if nums[i] + nums[j] == target:
                    return [i, j]
```

### Brute Force Walkthrough

For:

```python
nums = [2, 7, 11, 15]
target = 9
```

Check:

```text
i = 0, j = 1 -> nums[0] + nums[1] = 2 + 7 = 9
return [0, 1]
```

### Brute Force Complexity

Time: O(n^2)

Why: for every element, you may compare it with many other elements.

Space: O(1)

Why: you only use a few variables.

### Optimized Hashmap Algorithm

Use a hashmap where:

- Key = number seen earlier
- Value = index of that number

At each number:

1. Compute `complement = target - num`.
2. Check if `complement` is already in the hashmap.
3. If yes, return the previous index and current index.
4. If no, store current number and index.

### Optimized Code

```python
class Solution:
    def twoSum(self, nums: list[int], target: int) -> list[int]:
        seen = {}

        for i, num in enumerate(nums):
            complement = target - num

            if complement in seen:
                return [seen[complement], i]

            seen[num] = i
```

### Optimized Code With Iteration Comments

This is not how you need to write it on LeetCode. This version is for learning what each loop pass does.

```python
nums = [2, 7, 11, 15]
target = 9

seen = {}

# ITERATION 1
i = 0
num = nums[i]
# num = 2

complement = target - num
# complement = 9 - 2 = 7

if complement in seen:
    return [seen[complement], i]
# 7 is not in seen, so this if block does not run.

seen[num] = i
# seen[2] = 0
# seen is now {2: 0}

# ITERATION 2
i = 1
num = nums[i]
# num = 7

complement = target - num
# complement = 9 - 7 = 2

if complement in seen:
    return [seen[complement], i]
# 2 is in seen.
# seen[2] is 0.
# return [0, 1]
```

### Optimized Walkthrough

Input:

```python
nums = [2, 7, 11, 15]
target = 9
```

Start:

```python
seen = {}
```

Iteration 1:

```text
i = 0
num = 2
complement = 9 - 2 = 7
7 not in seen
store 2 -> 0
seen = {2: 0}
```

Iteration 2:

```text
i = 1
num = 7
complement = 9 - 7 = 2
2 is in seen
seen[2] = 0
return [0, 1]
```

### Why Store After Checking?

This prevents using the same element twice.

Example:

```python
nums = [3]
target = 6
```

If you stored before checking, you might see `3` as its own complement. That is invalid because one element cannot be used twice.

In the real LeetCode problem there is exactly one answer and at least two numbers, but this habit protects you in related problems.

### Handling Duplicate Numbers

Example:

```python
nums = [3, 3]
target = 6
```

Walkthrough:

```text
i = 0, num = 3, complement = 3
3 not in seen
seen = {3: 0}

i = 1, num = 3, complement = 3
3 in seen
return [0, 1]
```

Duplicates work naturally because the second `3` finds the first `3`.

### Two Sum Complexity

Time: O(n)

Why: one pass through the array, with average O(1) hashmap lookups.

Space: O(n)

Why: in the worst case, the hashmap stores many numbers before finding the answer.

### Two Sum Interview Explanation

"The brute force approach checks every pair, which is O(n^2). I can optimize by using a hashmap to store numbers I have already seen with their indexes. For each number, I compute the complement needed to hit the target. If that complement is already in the hashmap, I return its stored index and the current index. Otherwise, I store the current number. This gives O(n) time and O(n) space."

### Two Sum Manual Coding Template

Write this from memory:

```python
class Solution:
    def twoSum(self, nums: list[int], target: int) -> list[int]:
        seen = {}

        for i, num in enumerate(nums):
            need = target - num

            if need in seen:
                return [seen[need], i]

            seen[num] = i
```

## Problem 2: Contains Duplicate

LeetCode: 217. Contains Duplicate
Difficulty: Easy
Pattern: Seen set or frequency map
Core structure: Array + set/hashmap


## Status

✅ I COMPLETED TWO SUM SUCCESSFULLY

### Problem Statement

Given an integer array `nums`, return `True` if any value appears at least twice. Return `False` if every element is distinct.

Example:

```python
nums = [1, 2, 3, 1]
# return True
```

Example:

```python
nums = [1, 2, 3, 4]
# return False
```

### Brute Force Algorithm

Compare every pair.

```python
class Solution:
    def containsDuplicate(self, nums: list[int]) -> bool:
        for i in range(len(nums)):
            for j in range(i + 1, len(nums)):
                if nums[i] == nums[j]:
                    return True

        return False
```

### Brute Force Complexity

Time: O(n^2)

Why: nested loops compare pairs.

Space: O(1)

Why: no extra data structure grows with input size.

### Optimized Seen Set Algorithm

Use a set to remember values already seen.

At each number:

1. If the number is already in `seen`, return `True`.
2. Otherwise, add it to `seen`.
3. If the loop finishes, return `False`.

### Optimized Code

```python
class Solution:
    def containsDuplicate(self, nums: list[int]) -> bool:
        seen = set()

        for num in nums:
            if num in seen:
                return True

            seen.add(num)

        return False
```

### Optimized Code With Iteration Comments

This version shows the exact state change in the set.

```python
nums = [1, 2, 3, 1]

seen = set()

# ITERATION 1
num = nums[0]
# num = 1

if num in seen:
    return True
# 1 is not in seen, so this if block does not run.

seen.add(num)
# seen is now {1}

# ITERATION 2
num = nums[1]
# num = 2

if num in seen:
    return True
# 2 is not in seen.

seen.add(num)
# seen is now {1, 2}

# ITERATION 3
num = nums[2]
# num = 3

if num in seen:
    return True
# 3 is not in seen.

seen.add(num)
# seen is now {1, 2, 3}

# ITERATION 4
num = nums[3]
# num = 1

if num in seen:
    return True
# 1 is in seen, so return True.
```

### Walkthrough

Input:

```python
nums = [1, 2, 3, 1]
```

Start:

```python
seen = set()
```

Steps:

```text
num = 1
1 not in seen
add 1
seen = {1}

num = 2
2 not in seen
add 2
seen = {1, 2}

num = 3
3 not in seen
add 3
seen = {1, 2, 3}

num = 1
1 is in seen
return True
```

### Alternative One-Liner

```python
class Solution:
    def containsDuplicate(self, nums: list[int]) -> bool:
        return len(nums) != len(set(nums))
```

This works because a set removes duplicates.

Example:

```python
nums = [1, 2, 3, 1]
set(nums) == {1, 2, 3}

len(nums) == 4
len(set(nums)) == 3

4 != 3
return True
```

Same example with comments:

```python
nums = [1, 2, 3, 1]

# Original list length:
len(nums)
# 4

# Convert to a set.
# Sets only keep unique values, so the second 1 disappears.
set(nums)
# {1, 2, 3}

# Unique-value length:
len(set(nums))
# 3

# If the list length and set length differ, duplicates existed.
len(nums) != len(set(nums))
# 4 != 3 -> True
```

### Which Version Should You Use In Interviews?

Prefer the explicit loop version while learning.

Why:

- It shows the pattern clearly.
- It lets you return early.
- It proves you understand the hashmap/set idea.

The one-liner is fine after you can explain the loop version.

### Contains Duplicate Complexity

Loop version:

Time: O(n)

Why: each number is processed once, and set lookup is average O(1).

Space: O(n)

Why: if all values are unique, the set stores all n values.

One-liner:

Time: O(n)

Space: O(n)

### Contains Duplicate Interview Explanation

"I can use a set to track numbers I have seen. As I scan the array, if the current number is already in the set, then it is a duplicate and I return True. Otherwise I add it. If I finish scanning without finding a repeat, I return False. This is O(n) time and O(n) space."

### Contains Duplicate Manual Coding Template

Write this from memory:

```python
class Solution:
    def containsDuplicate(self, nums: list[int]) -> bool:
        seen = set()

        for num in nums:
            if num in seen:
                return True

            seen.add(num)

        return False
```

## Problem 3: Valid Anagram

LeetCode: 242. Valid Anagram
Difficulty: Easy
Pattern: Frequency map
Core structure: String + hashmap



## Status

✅ I COMPLETED TWO SUM SUCCESSFULLY

### Problem Statement

Given two strings `s` and `t`, return `True` if `t` is an anagram of `s`, and `False` otherwise.

An anagram uses the exact same characters with the exact same counts, possibly in a different order.

Example:

```python
s = "anagram"
t = "nagaram"
# return True
```

Example:

```python
s = "rat"
t = "car"
# return False
```

### Key Insight

Order does not matter. Counts matter.

```text
"anagram"
a: 3
n: 1
g: 1
r: 1
m: 1

"nagaram"
a: 3
n: 1
g: 1
r: 1
m: 1
```

Same counts means valid anagram.

Different counts means not a valid anagram.

### Early Length Check

If the strings have different lengths, they cannot be anagrams.

```python
if len(s) != len(t):
    return False
```

This is a fast early exit.

### Sorting Algorithm

Sort both strings and compare them.

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        return sorted(s) == sorted(t)
```

Example:

```python
sorted("rat")  # ['a', 'r', 't']
sorted("car")  # ['a', 'c', 'r']
```

They differ, so return `False`.

### Sorting Complexity

Time: O(n log n)

Why: sorting dominates.

Space: O(n)

Why: sorted strings/lists use extra space in Python.

### Frequency Map Algorithm

Use a dictionary to count characters.

At a high level:

1. If lengths differ, return `False`.
2. Count every character in `s`.
3. Count every character in `t`.
4. Compare the two count maps.

### Frequency Map Code: Two Maps

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        count_s = {}
        count_t = {}

        for char in s:
            count_s[char] = count_s.get(char, 0) + 1

        for char in t:
            count_t[char] = count_t.get(char, 0) + 1

        return count_s == count_t
```

### Frequency Map Code With Iteration Comments

This version shows exactly how each character changes each map.

```python
s = "rat"
t = "car"

count_s = {}
count_t = {}

# COUNT s

# ITERATION 1
char = "r"
count_s[char] = count_s.get(char, 0) + 1
# count_s.get("r", 0) returns 0
# count_s is now {'r': 1}

# ITERATION 2
char = "a"
count_s[char] = count_s.get(char, 0) + 1
# count_s.get("a", 0) returns 0
# count_s is now {'r': 1, 'a': 1}

# ITERATION 3
char = "t"
count_s[char] = count_s.get(char, 0) + 1
# count_s.get("t", 0) returns 0
# count_s is now {'r': 1, 'a': 1, 't': 1}

# COUNT t

# ITERATION 1
char = "c"
count_t[char] = count_t.get(char, 0) + 1
# count_t.get("c", 0) returns 0
# count_t is now {'c': 1}

# ITERATION 2
char = "a"
count_t[char] = count_t.get(char, 0) + 1
# count_t.get("a", 0) returns 0
# count_t is now {'c': 1, 'a': 1}

# ITERATION 3
char = "r"
count_t[char] = count_t.get(char, 0) + 1
# count_t.get("r", 0) returns 0
# count_t is now {'c': 1, 'a': 1, 'r': 1}

count_s == count_t
# {'r': 1, 'a': 1, 't': 1} == {'c': 1, 'a': 1, 'r': 1}
# False
```

### Frequency Map Walkthrough

Input:

```python
s = "rat"
t = "car"
```

Count `s`:

```text
r -> {'r': 1}
a -> {'r': 1, 'a': 1}
t -> {'r': 1, 'a': 1, 't': 1}
```

Count `t`:

```text
c -> {'c': 1}
a -> {'c': 1, 'a': 1}
r -> {'c': 1, 'a': 1, 'r': 1}
```

Compare:

```python
{'r': 1, 'a': 1, 't': 1} == {'c': 1, 'a': 1, 'r': 1}
# False
```

### Frequency Map Complexity

Let n be the length of the strings.

Time: O(n)

Why: each string is scanned once.

Space: O(k)

Why: the maps store counts for distinct characters. `k` is the number of unique characters. In the general case this can be O(n). If the problem guarantees lowercase English letters only, then k is at most 26, which is O(1) space by strict asymptotic analysis.

For interview simplicity, say:

Time: O(n)
Space: O(1) if only lowercase English letters, otherwise O(n)

### Frequency Map Code: One Map

Another common approach:

1. Count characters in `s`.
2. Subtract characters from `t`.
3. If a character is missing or goes below zero, return `False`.
4. If all checks pass, return `True`.

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        counts = {}

        for char in s:
            counts[char] = counts.get(char, 0) + 1

        for char in t:
            if char not in counts:
                return False

            counts[char] -= 1

            if counts[char] < 0:
                return False

        return True
```

Same algorithm with iteration comments:

```python
s = "aacc"
t = "ccac"

counts = {}

# BUILD counts FROM s

# ITERATION 1
char = "a"
counts[char] = counts.get(char, 0) + 1
# counts is now {'a': 1}

# ITERATION 2
char = "a"
counts[char] = counts.get(char, 0) + 1
# counts is now {'a': 2}

# ITERATION 3
char = "c"
counts[char] = counts.get(char, 0) + 1
# counts is now {'a': 2, 'c': 1}

# ITERATION 4
char = "c"
counts[char] = counts.get(char, 0) + 1
# counts is now {'a': 2, 'c': 2}

# SUBTRACT counts USING t

# ITERATION 1
char = "c"
# "c" exists in counts.
counts[char] -= 1
# counts is now {'a': 2, 'c': 1}

# ITERATION 2
char = "c"
# "c" exists in counts.
counts[char] -= 1
# counts is now {'a': 2, 'c': 0}

# ITERATION 3
char = "a"
# "a" exists in counts.
counts[char] -= 1
# counts is now {'a': 1, 'c': 0}

# ITERATION 4
char = "c"
# "c" exists in counts.
counts[char] -= 1
# counts is now {'a': 1, 'c': -1}
# counts["c"] is below 0, so t used too many "c" characters.
return False
```

This works because equal-length strings with no missing character and no overused character must have the same character counts.

### One Map Walkthrough

Input:

```python
s = "aacc"
t = "ccac"
```

Count `s`:

```python
counts = {'a': 2, 'c': 2}
```

Process `t`:

```text
char = c
counts['c'] becomes 1

char = c
counts['c'] becomes 0

char = a
counts['a'] becomes 1

char = c
counts['c'] becomes -1
return False
```

There are too many `c` characters in `t`.

### Array Count Version For Lowercase English Letters

If the problem guarantees only lowercase English letters `a` through `z`, you can use an array of size 26.

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        counts = [0] * 26

        for char in s:
            index = ord(char) - ord("a")
            counts[index] += 1

        for char in t:
            index = ord(char) - ord("a")
            counts[index] -= 1

        for count in counts:
            if count != 0:
                return False

        return True
```

Same array-count idea with comments:

```python
s = "abca"
t = "cbaa"

counts = [0] * 26
# counts[0] tracks "a"
# counts[1] tracks "b"
# counts[2] tracks "c"

# COUNT s

# ITERATION 1
char = "a"
index = ord(char) - ord("a")
# index = 0
counts[index] += 1
# counts[0] is now 1

# ITERATION 2
char = "b"
index = ord(char) - ord("a")
# index = 1
counts[index] += 1
# counts[1] is now 1

# ITERATION 3
char = "c"
index = ord(char) - ord("a")
# index = 2
counts[index] += 1
# counts[2] is now 1

# ITERATION 4
char = "a"
index = ord(char) - ord("a")
# index = 0
counts[index] += 1
# counts[0] is now 2

# SUBTRACT t

# ITERATION 1
char = "c"
index = ord(char) - ord("a")
# index = 2
counts[index] -= 1
# counts[2] is now 0

# ITERATION 2
char = "b"
index = ord(char) - ord("a")
# index = 1
counts[index] -= 1
# counts[1] is now 0

# ITERATION 3
char = "a"
index = ord(char) - ord("a")
# index = 0
counts[index] -= 1
# counts[0] is now 1

# ITERATION 4
char = "a"
index = ord(char) - ord("a")
# index = 0
counts[index] -= 1
# counts[0] is now 0

# All counts are back to 0, so the strings are anagrams.
```

How `ord` works:

```python
ord("a")  # 97
ord("b")  # 98
ord("c")  # 99
```

So:

```python
ord("c") - ord("a")  # 2
```

That maps:

```text
a -> 0
b -> 1
c -> 2
...
z -> 25
```

### Which Valid Anagram Version Should You Use?

For Day 1, use the two-map dictionary version first.

It is the clearest for learning frequency maps:

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        count_s = {}
        count_t = {}

        for char in s:
            count_s[char] = count_s.get(char, 0) + 1

        for char in t:
            count_t[char] = count_t.get(char, 0) + 1

        return count_s == count_t
```

After that feels natural, practice the one-map subtracting version.

### Valid Anagram Interview Explanation

"An anagram must have the same characters with the same frequencies. I first check if the lengths differ, because different lengths cannot be anagrams. Then I build frequency maps for both strings and compare them. Building the maps takes O(n), and comparing them is bounded by the number of unique characters. The solution is O(n) time."

## The Three Day 1 Patterns Side By Side

### Contains Duplicate

Question:

```text
Have I seen this exact value before?
```

Data structure:

```python
seen = set()
```

Core line:

```python
if num in seen:
```

### Two Sum

Question:

```text
Have I seen the value that completes this target?
```

Data structure:

```python
seen = {}
```

Core line:

```python
need = target - num
if need in seen:
```

### Valid Anagram

Question:

```text
Do these two strings have the same character counts?
```

Data structure:

```python
counts = {}
```

Core line:

```python
counts[char] = counts.get(char, 0) + 1
```

## How To Recognize The Pattern

Use a hashmap or set when you hear yourself thinking:

- "I need to know if I already saw this."
- "I need to count occurrences."
- "I need fast lookup."
- "I need the missing/complement value."
- "A nested loop works, but it feels slow."
- "Order does not matter, but frequency does."

## Common Mistakes

### Mistake 1: Returning values instead of indexes

Two Sum asks for indexes.

Wrong:

```python
return [num, complement]
```

Right:

```python
return [seen[complement], i]
```

### Mistake 2: Using the same element twice

Wrong mental move:

```text
target = 6
num = 3
3 + 3 = 6
```

That only works if there are two separate `3` values.

This is why the optimized Two Sum solution checks before storing the current number.

### Mistake 3: Forgetting to add to `seen`

Wrong:

```python
seen = set()

for num in nums:
    if num in seen:
        return True

return False
```

This always returns `False` because `seen` stays empty.

Right:

```python
seen = set()

for num in nums:
    if num in seen:
        return True

    seen.add(num)

return False
```

### Mistake 4: Forgetting the default value while counting

Wrong:

```python
counts[char] += 1
```

This fails the first time `char` appears because the key does not exist yet.

Right:

```python
counts[char] = counts.get(char, 0) + 1
```

### Mistake 5: Sorting when frequency map is the target skill

Sorting is valid for Valid Anagram, but Day 1 is about hashmaps.

Practice the frequency map approach until it feels automatic.

## Python Syntax You Must Be Comfortable With

### `enumerate`

Use `enumerate` when you need both index and value.

```python
nums = [2, 7, 11]

for i, num in enumerate(nums):
    print(i, num)
```

Output:

```text
0 2
1 7
2 11
```

Two Sum needs indexes, so `enumerate` is useful.

### `in`

Checks membership.

```python
if x in seen:
    print("found")
```

For lists, `x in nums` is O(n).
For sets and dicts, `x in seen` is average O(1).

That difference matters.

### `dict.get`

Safely reads a key with a fallback.

```python
counts[char] = counts.get(char, 0) + 1
```

If `char` is missing, `counts.get(char, 0)` gives `0`.

### `set.add`

Adds a value to a set.

```python
seen.add(num)
```

Adding a duplicate to a set does not create another copy.

```python
seen = set()
seen.add(1)
seen.add(1)

print(seen)
# {1}
```

## Manual Practice Routine

For each problem, do this without AI completion:

1. Write the brute force idea in plain English.
2. Write the optimized pattern name.
3. State the data structure.
4. Write the code from memory.
5. Trace the code on one example.
6. Say the time and space complexity out loud.

## Dry Run Template

Use this for any hashmap problem:

```text
Input:

State before loop:

Iteration 1:
current value:
computed value:
hashmap/set before:
decision:
hashmap/set after:

Iteration 2:
current value:
computed value:
hashmap/set before:
decision:
hashmap/set after:

Return:
```

## Practice Prompts

### Two Sum

Trace this:

```python
nums = [4, 2, 9, 7]
target = 11
```

Expected:

```python
[1, 2]
```

Because:

```python
nums[1] + nums[2] == 2 + 9 == 11
```

Trace this:

```python
nums = [3, 2, 4]
target = 6
```

Expected:

```python
[1, 2]
```

### Contains Duplicate

Trace this:

```python
nums = [8, 1, 4, 8, 2]
```

Expected:

```python
True
```

Trace this:

```python
nums = [8, 1, 4, 2]
```

Expected:

```python
False
```

### Valid Anagram

Trace this:

```python
s = "listen"
t = "silent"
```

Expected:

```python
True
```

Trace this:

```python
s = "hello"
t = "bello"
```

Expected:

```python
False
```

## Final Day 1 Code To Memorize

### Two Sum

```python
class Solution:
    def twoSum(self, nums: list[int], target: int) -> list[int]:
        seen = {}

        for i, num in enumerate(nums):
            need = target - num

            if need in seen:
                return [seen[need], i]

            seen[num] = i
```

### Contains Duplicate

```python
class Solution:
    def containsDuplicate(self, nums: list[int]) -> bool:
        seen = set()

        for num in nums:
            if num in seen:
                return True

            seen.add(num)

        return False
```

### Valid Anagram

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        count_s = {}
        count_t = {}

        for char in s:
            count_s[char] = count_s.get(char, 0) + 1

        for char in t:
            count_t[char] = count_t.get(char, 0) + 1

        return count_s == count_t
```

## Day 1 Self-Test

Answer these without looking:

1. What is the difference between a set and a dict?
2. Why is hashmap lookup average O(1)?
3. What is a frequency map?
4. What is a complement in Two Sum?
5. Why do we check the hashmap before inserting the current number in Two Sum?
6. Why does Contains Duplicate use a set instead of a dict?
7. Why does Valid Anagram need counts instead of only checking membership?
8. What is the time complexity of brute force Two Sum?
9. What is the time complexity of hashmap Two Sum?
10. What is the space complexity of Contains Duplicate?

## Day 1 Answers

1. A set stores unique values. A dict stores key-value pairs.
2. A hashmap uses a hash function to jump close to where a key should be stored, so lookup is average constant time.
3. A frequency map is a hashmap where each key is an item and each value is how many times the item appears.
4. The complement is the value needed to reach the target: `target - current`.
5. To avoid using the same element twice.
6. Contains Duplicate only needs to know whether a value was seen before; it does not need to store extra data.
7. Membership alone cannot tell whether counts match. `"aab"` and `"ab"` both contain `a` and `b`, but they are not anagrams.
8. O(n^2).
9. O(n).
10. O(n) in the worst case.

## Day 1 Interview Phrases

Use these phrases until they become natural:

- "I can use a hashmap to avoid scanning the array repeatedly."
- "The hashmap gives average O(1) lookup."
- "This turns the brute force O(n^2) approach into O(n)."
- "I will store values I have already seen."
- "I will count the frequency of each character."
- "Since order does not matter, I care about counts."
- "The tradeoff is extra O(n) space."

## Day 1 Completion Criteria

You are done with Day 1 when you can:

1. Implement all three problems without looking.
2. Explain the brute force and optimized approach for each.
3. Dry run each optimized solution on a small input.
4. State time and space complexity correctly.
5. Recognize whether a problem needs a set, dict, frequency map, or complement lookup.
