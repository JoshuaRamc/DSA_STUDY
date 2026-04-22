# Day 2: Strings + Hashmaps

Date: April 22, 2026
Label: day2_strings_hashmaps
Focus: String traversal, character frequency, string manipulation basics, comparing strings with maps, and grouping strings by shared structure.

## Goal For Today

By the end of Day 2, you should be able to look at a string problem and ask:

1. Do I need to scan characters one by one?
2. Do I need to ignore spaces, punctuation, or capitalization?
3. Do I need to count characters?
4. Do I need to compare strings by their character counts?
5. Do I need to group strings that have the same normalized form?

If the answer is yes, the problem probably uses string traversal, two pointers, sorting, or a hashmap.

## Core Vocabulary

### String

A string is an ordered sequence of characters.

```python
word = "leetcode"
```

Important string facts:

- Strings preserve order.
- You can access a character by index.
- Python strings are immutable, which means you cannot change them in place.
- Scanning a string character by character is O(n).
- Creating a modified string usually costs extra space.

Example:

```python
word = "cat"

print(word[0])  # c
print(word[1])  # a
print(word[2])  # t
```

### Character

A character is one item inside a string.

```python
s = "abc"
# characters: "a", "b", "c"
```

When you loop through a string, Python gives you one character at a time.

```python
for char in "abc":
    print(char)
```

Output:

```text
a
b
c
```

### Substring

A substring is a contiguous piece of a string.

```python
s = "leetcode"

s[0:4]  # "leet"
s[4:8]  # "code"
```

### Prefix

A prefix starts at the beginning of a string.

```text
"flower"

prefixes:
"f"
"fl"
"flo"
"flow"
"flowe"
"flower"
```

For Longest Common Prefix, you are looking for the longest beginning part shared by every string.

### Palindrome

A palindrome reads the same forward and backward.

```text
"racecar" -> palindrome
"madam"   -> palindrome
"hello"   -> not a palindrome
```

For Valid Palindrome, you usually normalize the string by ignoring non-alphanumeric characters and capitalization.

## Python String Operations

### Access By Index

```python
s = "hello"

s[0]  # "h"
s[1]  # "e"
s[4]  # "o"
```

### Length

```python
s = "hello"

len(s)  # 5
```

### Last Index

```python
s = "hello"

last_index = len(s) - 1
# 4
```

### Lowercase

```python
"A".lower()  # "a"
"Hello".lower()  # "hello"
```

Use lowercase when the problem says capitalization does not matter.

### Alphanumeric Check

```python
"a".isalnum()  # True
"7".isalnum()  # True
" ".isalnum()  # False
",".isalnum()  # False
```

Use `isalnum()` when the problem says to ignore punctuation, spaces, and symbols.

### Sort Characters

```python
word = "eat"

sorted(word)
# ['a', 'e', 't']

"".join(sorted(word))
# "aet"
```

Sorting a word gives a canonical version. Anagrams have the same sorted version.

### Join Characters

```python
chars = ["a", "b", "c"]

"".join(chars)
# "abc"
```

This is common when you build a cleaned string or a sorted-key string.

## String Traversal

String traversal means visiting characters one by one.

### Traverse By Character

Use this when you only need the character.

```python
s = "abc"

for char in s:
    print(char)
```

### Traverse By Index

Use this when you need positions.

```python
s = "abc"

for i in range(len(s)):
    print(i, s[i])
```

Output:

```text
0 a
1 b
2 c
```

### Traverse With Two Pointers

Use this when you want to compare characters from both ends.

```python
s = "racecar"

left = 0
right = len(s) - 1

while left < right:
    print(s[left], s[right])
    left += 1
    right -= 1
```

Two pointers are perfect for palindrome problems.

## Character Frequency

Character frequency means counting how many times each character appears.

```python
s = "banana"

counts = {}

for char in s:
    counts[char] = counts.get(char, 0) + 1

print(counts)
# {'b': 1, 'a': 3, 'n': 2}
```

How to think:

- Key = character
- Value = number of times that character appears

This is the same frequency map idea from Day 1, now applied to strings.

## Pattern 1: Clean A String

Use this when the problem asks you to ignore:

- Spaces
- Punctuation
- Symbols
- Capitalization

Template:

```python
cleaned = []

for char in s:
    if char.isalnum():
        cleaned.append(char.lower())

cleaned_string = "".join(cleaned)
```

Example:

```python
s = "A man, a plan!"

cleaned = []

for char in s:
    if char.isalnum():
        cleaned.append(char.lower())

cleaned_string = "".join(cleaned)

print(cleaned_string)
# "amanaplan"
```

Loop-focused trace:

```python
s = "A, b!"

cleaned = []

for char in s:
    # ITERATION 1: char = "A"
    # "A" is alphanumeric, so append "a".
    # cleaned becomes ["a"].

    # ITERATION 2: char = ","
    # "," is not alphanumeric, so skip it.
    # cleaned stays ["a"].

    # ITERATION 3: char = " "
    # space is not alphanumeric, so skip it.
    # cleaned stays ["a"].

    # ITERATION 4: char = "b"
    # "b" is alphanumeric, so append "b".
    # cleaned becomes ["a", "b"].

    # ITERATION 5: char = "!"
    # "!" is not alphanumeric, so skip it.

    if char.isalnum():
        cleaned.append(char.lower())

cleaned_string = "".join(cleaned)
# "ab"
```

## Pattern 2: Two Pointers On A String

Use this when:

- You need to compare the left side and right side.
- You need to move inward.
- You want O(1) extra space.

Template:

```python
left = 0
right = len(s) - 1

while left < right:
    if s[left] != s[right]:
        return False

    left += 1
    right -= 1

return True
```

Loop-focused trace:

```python
s = "racecar"

left = 0
right = len(s) - 1

while left < right:
    # ITERATION 1
    # left = 0, right = 6
    # s[left] = "r", s[right] = "r"
    # They match, so move inward.

    # ITERATION 2
    # left = 1, right = 5
    # s[left] = "a", s[right] = "a"
    # They match, so move inward.

    # ITERATION 3
    # left = 2, right = 4
    # s[left] = "c", s[right] = "c"
    # They match, so move inward.

    if s[left] != s[right]:
        return False

    left += 1
    right -= 1

return True
```

## Pattern 3: Compare Strings With Maps

Use this when:

- Order does not matter.
- Character counts matter.
- You need to know whether two strings contain the same characters the same number of times.

Template:

```python
def build_count_map(s):
    counts = {}

    for char in s:
        counts[char] = counts.get(char, 0) + 1

    return counts

count_s = build_count_map(s)
count_t = build_count_map(t)

return count_s == count_t
```

Example:

```python
s = "listen"
t = "silent"

count_s = {"l": 1, "i": 1, "s": 1, "t": 1, "e": 1, "n": 1}
count_t = {"s": 1, "i": 1, "l": 1, "e": 1, "n": 1, "t": 1}

count_s == count_t
# True
```

Dict order does not matter for equality. The keys and values must match.

## Pattern 4: Group By A Key

Use this when:

- Multiple strings belong together.
- You can compute a shared key for each group.
- All strings with the same key should go into the same list.

For anagrams, the key can be the sorted version of the word.

```python
"eat" -> "aet"
"tea" -> "aet"
"ate" -> "aet"
```

Template:

```python
groups = {}

for word in strs:
    key = "".join(sorted(word))

    if key in groups:
        groups[key].append(word)
    else:
        groups[key] = [word]

return list(groups.values())
```

How to think:

- Key = the normalized form shared by a group
- Value = list of original strings that belong to that group

## Problem 1: Valid Palindrome

LeetCode: 125. Valid Palindrome
Difficulty: Easy
Pattern: String traversal, cleaning, two pointers
Core structure: String + two pointers

## Status

[x] I COMPLETED VALID PALINDROME SUCCESSFULLY

### Problem Statement

Given a string `s`, return `True` if it is a palindrome after converting all uppercase letters into lowercase letters and removing all non-alphanumeric characters. Otherwise, return `False`.

Example:

```python
s = "A man, a plan, a canal: Panama"
# return True
```

After cleaning:

```python
"amanaplanacanalpanama"
```

That cleaned string reads the same forward and backward.

Example:

```python
s = "race a car"
# return False
```

After cleaning:

```python
"raceacar"
```

That does not read the same forward and backward.

### Brute Force Cleaning Algorithm

One simple way:

1. Build a cleaned string.
2. Compare the cleaned string with its reverse.

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        cleaned = []

        for char in s:
            if char.isalnum():
                cleaned.append(char.lower())

        cleaned_string = "".join(cleaned)

        return cleaned_string == cleaned_string[::-1]
```

### Cleaning Walkthrough

Input:

```python
s = "A, bA"
```

Trace:

```python
cleaned = []

for char in s:
    # ITERATION 1: char = "A"
    # alphanumeric, append "a"
    # cleaned = ["a"]

    # ITERATION 2: char = ","
    # not alphanumeric, skip
    # cleaned = ["a"]

    # ITERATION 3: char = " "
    # not alphanumeric, skip
    # cleaned = ["a"]

    # ITERATION 4: char = "b"
    # alphanumeric, append "b"
    # cleaned = ["a", "b"]

    # ITERATION 5: char = "A"
    # alphanumeric, append "a"
    # cleaned = ["a", "b", "a"]

    if char.isalnum():
        cleaned.append(char.lower())

cleaned_string = "".join(cleaned)
# "aba"

cleaned_string == cleaned_string[::-1]
# "aba" == "aba"
# True
```

### Brute Force Complexity

Time: O(n)

Why: you scan the string once, then reverse the cleaned string.

Space: O(n)

Why: you build a cleaned string/list.

### Optimized Two Pointer Algorithm

Instead of building a cleaned string, use two pointers directly on the original string.

At each step:

1. Move `left` forward until it points to an alphanumeric character.
2. Move `right` backward until it points to an alphanumeric character.
3. Compare lowercase versions of both characters.
4. If they differ, return `False`.
5. If they match, move both pointers inward.
6. If the loop finishes, return `True`.

### Optimized Code

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        left = 0
        right = len(s) - 1

        while left < right:
            while left < right and not s[left].isalnum():
                left += 1

            while left < right and not s[right].isalnum():
                right -= 1

            if s[left].lower() != s[right].lower():
                return False

            left += 1
            right -= 1

        return True
```

### Optimized Walkthrough

Input:

```python
s = "A man, a plan, a canal: Panama"
```

Start:

```python
left = 0
right = len(s) - 1
```

Trace:

```text
Iteration 1:
s[left] = "A"
s[right] = "a"
Compare "a" and "a"
They match
Move inward

Later:
Spaces and punctuation are skipped by the inner while loops.

Final:
All matching alphanumeric characters match
return True
```

### Two Pointer Code With Comments

```python
s = "A, bA"

left = 0
right = len(s) - 1

while left < right:
    # ITERATION 1
    # left points to "A"
    # right points to "A"
    # Both are alphanumeric.
    # Compare "a" and "a".
    # They match, so move inward.

    while left < right and not s[left].isalnum():
        left += 1

    while left < right and not s[right].isalnum():
        right -= 1

    if s[left].lower() != s[right].lower():
        return False

    left += 1
    right -= 1

    # ITERATION 2
    # left now points to ","
    # right now points to "b"
    # left is not alphanumeric, so move left forward.
    # left skips "," and " ", then lands on "b".
    # right is already on "b".
    # Compare "b" and "b".
    # They match.

return True
```

### Valid Palindrome Interview Explanation

"I can solve this with two pointers. One pointer starts at the beginning and the other starts at the end. I skip any non-alphanumeric characters, compare the lowercase versions of the characters, and move inward. If any pair differs, the string is not a palindrome. If all pairs match, it is valid. This gives O(n) time and O(1) extra space."

### Valid Palindrome Manual Coding Template

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        left = 0
        right = len(s) - 1

        while left < right:
            while left < right and not s[left].isalnum():
                left += 1

            while left < right and not s[right].isalnum():
                right -= 1

            if s[left].lower() != s[right].lower():
                return False

            left += 1
            right -= 1

        return True
```

## Problem 2: Longest Common Prefix

LeetCode: 14. Longest Common Prefix
Difficulty: Easy
Pattern: String comparison, prefix shrinking
Core structure: Array of strings

### Problem Statement

Write a function to find the longest common prefix string among an array of strings.

If there is no common prefix, return an empty string.

Example:

```python
strs = ["flower", "flow", "flight"]
# return "fl"
```

Example:

```python
strs = ["dog", "racecar", "car"]
# return ""
```

### Key Insight

The common prefix must be a prefix of the first string.

So you can:

1. Start with the first string as the candidate prefix.
2. Compare it with every other string.
3. While a string does not start with the prefix, remove one character from the end of the prefix.
4. Return the prefix when all strings accept it.

### Prefix Shrinking Algorithm

```python
class Solution:
    def longestCommonPrefix(self, strs: list[str]) -> str:
        if not strs:
            return ""

        prefix = strs[0]

        for word in strs[1:]:
            while not word.startswith(prefix):
                prefix = prefix[:-1]

                if prefix == "":
                    return ""

        return prefix
```

### Walkthrough

Input:

```python
strs = ["flower", "flow", "flight"]
```

Start:

```python
prefix = "flower"
```

Trace:

```text
word = "flow"

Does "flow" start with "flower"? No.
Shrink prefix to "flowe".

Does "flow" start with "flowe"? No.
Shrink prefix to "flow".

Does "flow" start with "flow"? Yes.
Move to next word.

word = "flight"

Does "flight" start with "flow"? No.
Shrink prefix to "flo".

Does "flight" start with "flo"? No.
Shrink prefix to "fl".

Does "flight" start with "fl"? Yes.

return "fl"
```

### Code With Loop Comments

```python
strs = ["flower", "flow", "flight"]

prefix = strs[0]
# prefix = "flower"

for word in strs[1:]:
    # OUTER ITERATION 1: word = "flow"
    # prefix starts as "flower"
    # "flow" does not start with "flower", so shrink.
    # prefix becomes "flowe"
    # "flow" does not start with "flowe", so shrink.
    # prefix becomes "flow"
    # "flow" starts with "flow", so stop shrinking.

    # OUTER ITERATION 2: word = "flight"
    # prefix starts as "flow"
    # "flight" does not start with "flow", so shrink.
    # prefix becomes "flo"
    # "flight" does not start with "flo", so shrink.
    # prefix becomes "fl"
    # "flight" starts with "fl", so stop shrinking.

    while not word.startswith(prefix):
        prefix = prefix[:-1]

        if prefix == "":
            return ""

return prefix
# "fl"
```

### Alternative Vertical Scanning Algorithm

Compare characters column by column.

```python
class Solution:
    def longestCommonPrefix(self, strs: list[str]) -> str:
        if not strs:
            return ""

        for i in range(len(strs[0])):
            char = strs[0][i]

            for word in strs:
                if i == len(word) or word[i] != char:
                    return strs[0][:i]

        return strs[0]
```

How to think:

- Check index `0` across all strings.
- Then check index `1` across all strings.
- Stop when one string ends or one character differs.

### Vertical Scanning Walkthrough

Input:

```python
strs = ["flower", "flow", "flight"]
```

Trace:

```text
i = 0
char = "f"
Every word has "f" at index 0.

i = 1
char = "l"
Every word has "l" at index 1.

i = 2
char = "o"
"flight" has "i" at index 2.
Mismatch.
return strs[0][:2]
return "fl"
```

### Complexity

Let `n` be the number of strings and `m` be the length of the shortest string.

Time: O(n * m)

Why: in the worst case, you compare characters across all strings up to the shortest length.

Space: O(1)

Why: you only store a few variables.

### Longest Common Prefix Interview Explanation

"The common prefix must be contained in the first string. I can start with the first string as the candidate prefix, then compare it to each other word. While a word does not start with the candidate prefix, I shrink the prefix from the end. Once every word accepts the prefix, I return it. If the prefix becomes empty, there is no common prefix."

### Longest Common Prefix Manual Coding Template

```python
class Solution:
    def longestCommonPrefix(self, strs: list[str]) -> str:
        if not strs:
            return ""

        prefix = strs[0]

        for word in strs[1:]:
            while not word.startswith(prefix):
                prefix = prefix[:-1]

                if prefix == "":
                    return ""

        return prefix
```

## Problem 3: Group Anagrams

LeetCode: 49. Group Anagrams
Difficulty: Medium
Pattern: Hashmap grouping by normalized key
Core structure: Array of strings + hashmap

## Status

[x] I COMPLETED GROUP ANAGRAMS SUCCESSFULLY (MEDIUM)

### Problem Statement

Given an array of strings `strs`, group the anagrams together. You can return the answer in any order.

Example:

```python
strs = ["eat", "tea", "tan", "ate", "nat", "bat"]

# return something like:
# [["eat", "tea", "ate"], ["tan", "nat"], ["bat"]]
```

### Key Insight

Anagrams become identical if you sort their characters.

```text
"eat" -> "aet"
"tea" -> "aet"
"ate" -> "aet"

"tan" -> "ant"
"nat" -> "ant"

"bat" -> "abt"
```

So the sorted word can be the hashmap key.

### Hashmap Grouping Algorithm

Use a dictionary where:

- Key = sorted version of the word
- Value = list of original words with that sorted version

Steps:

1. Create an empty dictionary called `tracker`.
2. For each word, sort its letters.
3. Convert the sorted letters back into a string key.
4. If the key already exists, append the word to that group.
5. Otherwise, create a new group with that word.
6. Return all groups.

### Your Completed Approach

This is the approach you already wrote.

```python
class Solution(object):
    def groupAnagrams(self, strs):
        """
        :type strs: List[str]
        :rtype: List[List[str]]
        """

        # Sort by letters and store that sorted version as the key.
        # If the sorted version exists, append the word to that group.
        # Otherwise, create a new key and start a new group.

        tracker = {}
        solution = []

        for word in strs:
            sorted_word = "".join(sorted(word))

            if sorted_word in tracker:
                tracker[sorted_word].append(word)
            else:
                tracker[sorted_word] = [word]

        for key, value in tracker.items():
            temp_array = tracker[key]
            solution.append(temp_array)

        return solution
```

### Slightly Cleaner Version

This version returns the grouped values directly.

```python
class Solution:
    def groupAnagrams(self, strs: list[str]) -> list[list[str]]:
        groups = {}

        for word in strs:
            key = "".join(sorted(word))

            if key in groups:
                groups[key].append(word)
            else:
                groups[key] = [word]

        return list(groups.values())
```

Both versions use the same core algorithm.

### Walkthrough

Input:

```python
strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
```

Start:

```python
groups = {}
```

Trace:

```text
word = "eat"
key = "aet"
"aet" not in groups
groups = {"aet": ["eat"]}

word = "tea"
key = "aet"
"aet" already in groups
groups = {"aet": ["eat", "tea"]}

word = "tan"
key = "ant"
"ant" not in groups
groups = {"aet": ["eat", "tea"], "ant": ["tan"]}

word = "ate"
key = "aet"
"aet" already in groups
groups = {"aet": ["eat", "tea", "ate"], "ant": ["tan"]}

word = "nat"
key = "ant"
"ant" already in groups
groups = {"aet": ["eat", "tea", "ate"], "ant": ["tan", "nat"]}

word = "bat"
key = "abt"
"abt" not in groups
groups = {"aet": ["eat", "tea", "ate"], "ant": ["tan", "nat"], "abt": ["bat"]}
```

Return:

```python
list(groups.values())
# [["eat", "tea", "ate"], ["tan", "nat"], ["bat"]]
```

### Code With Loop Comments

```python
strs = ["eat", "tea", "tan", "ate", "nat", "bat"]

groups = {}

for word in strs:
    # ITERATION 1: word = "eat"
    # key = "aet"
    # groups does not have "aet", so create:
    # groups["aet"] = ["eat"]

    # ITERATION 2: word = "tea"
    # key = "aet"
    # groups already has "aet", so append:
    # groups["aet"] = ["eat", "tea"]

    # ITERATION 3: word = "tan"
    # key = "ant"
    # groups does not have "ant", so create:
    # groups["ant"] = ["tan"]

    # ITERATION 4: word = "ate"
    # key = "aet"
    # groups already has "aet", so append:
    # groups["aet"] = ["eat", "tea", "ate"]

    # ITERATION 5: word = "nat"
    # key = "ant"
    # groups already has "ant", so append:
    # groups["ant"] = ["tan", "nat"]

    # ITERATION 6: word = "bat"
    # key = "abt"
    # groups does not have "abt", so create:
    # groups["abt"] = ["bat"]

    key = "".join(sorted(word))

    if key in groups:
        groups[key].append(word)
    else:
        groups[key] = [word]

return list(groups.values())
```

### Complexity

Let:

- `n` = number of words
- `k` = maximum length of a word

Time: O(n * k log k)

Why: you sort each word. Sorting one word costs O(k log k), and you do it for `n` words.

Space: O(n * k)

Why: the hashmap stores all words inside groups, plus keys.

### Alternative Count-Tuple Key

If strings contain only lowercase English letters, you can avoid sorting each word by using a count tuple.

```python
class Solution:
    def groupAnagrams(self, strs: list[str]) -> list[list[str]]:
        groups = {}

        for word in strs:
            counts = [0] * 26

            for char in word:
                index = ord(char) - ord("a")
                counts[index] += 1

            key = tuple(counts)

            if key in groups:
                groups[key].append(word)
            else:
                groups[key] = [word]

        return list(groups.values())
```

Why tuple?

```python
counts = [1, 0, 0]
```

A list cannot be used as a dictionary key because lists are mutable.

```python
key = tuple(counts)
# (1, 0, 0)
```

A tuple can be used as a dictionary key.

### Sorting Key vs Count Tuple

Sorting key:

- Easier to understand.
- Easier to write under interview pressure.
- Time is O(n * k log k).

Count tuple:

- More optimized for lowercase English letters.
- Slightly more code.
- Time is O(n * k).

For Day 2, the sorted-key version is the right version to memorize first.

### Group Anagrams Interview Explanation

"Anagrams have the same letters with the same counts. If I sort the letters in each word, all anagrams produce the same sorted key. I use a hashmap where the key is the sorted word and the value is a list of original words with that key. For each word, I compute the key and append the word to that group. Finally, I return the hashmap values."

### Group Anagrams Manual Coding Template

```python
class Solution:
    def groupAnagrams(self, strs: list[str]) -> list[list[str]]:
        groups = {}

        for word in strs:
            key = "".join(sorted(word))

            if key in groups:
                groups[key].append(word)
            else:
                groups[key] = [word]

        return list(groups.values())
```

## The Three Day 2 Problems Side By Side

### Valid Palindrome

Question:

```text
Does this string read the same from both ends after cleaning?
```

Main pattern:

```python
left = 0
right = len(s) - 1
```

Core idea:

```python
if s[left].lower() != s[right].lower():
    return False
```

### Longest Common Prefix

Question:

```text
What beginning substring is shared by all words?
```

Main pattern:

```python
prefix = strs[0]
```

Core idea:

```python
while not word.startswith(prefix):
    prefix = prefix[:-1]
```

### Group Anagrams

Question:

```text
Which words have the same letters after normalization?
```

Main pattern:

```python
groups = {}
```

Core idea:

```python
key = "".join(sorted(word))
groups[key].append(word)
```

## Common Mistakes

### Mistake 1: Forgetting To Lowercase

For Valid Palindrome:

```python
"A" != "a"
```

Without `.lower()`, this would incorrectly fail.

Correct:

```python
s[left].lower() == s[right].lower()
```

### Mistake 2: Forgetting To Skip Non-Alphanumeric Characters

For Valid Palindrome, punctuation and spaces should not count.

Correct:

```python
while left < right and not s[left].isalnum():
    left += 1
```

### Mistake 3: Returning The Grouping Dict Instead Of The Values

For Group Anagrams, LeetCode wants a list of groups, not the dictionary itself.

Wrong:

```python
return groups
```

Right:

```python
return list(groups.values())
```

### Mistake 4: Using A List As A Dict Key

Wrong:

```python
key = sorted(word)
groups[key] = [word]
```

`sorted(word)` returns a list, and lists cannot be dictionary keys.

Right:

```python
key = "".join(sorted(word))
```

### Mistake 5: Longest Common Prefix Off-By-One

When vertical scanning, if a word is shorter than the current index, stop.

Correct:

```python
if i == len(word) or word[i] != char:
    return strs[0][:i]
```

The `i == len(word)` check prevents indexing past the end of a shorter word.

## Python Syntax You Must Be Comfortable With

### `char.isalnum()`

```python
"a".isalnum()  # True
"1".isalnum()  # True
"!".isalnum()  # False
" ".isalnum()  # False
```

### `char.lower()`

```python
"A".lower()  # "a"
```

### String Slicing

```python
s = "flower"

s[:-1]  # "flowe"
s[:2]   # "fl"
s[::-1] # "rewolf"
```

### `startswith`

```python
"flower".startswith("flow")
# True

"flight".startswith("flow")
# False
```

### Sorting A String

```python
word = "tea"

sorted(word)
# ["a", "e", "t"]

"".join(sorted(word))
# "aet"
```

### `dict.values()`

```python
groups = {
    "aet": ["eat", "tea"],
    "ant": ["tan", "nat"],
}

list(groups.values())
# [["eat", "tea"], ["tan", "nat"]]
```

## Manual Practice Routine

For each Day 2 problem:

1. Say the pattern name.
2. Write the brute force idea in plain English.
3. Write the optimized approach.
4. Code the solution manually.
5. Trace the solution on one example.
6. State time and space complexity.

## Dry Run Template

Use this for string traversal:

```text
Input:

State before loop:

Iteration 1:
current character or pointer:
condition checked:
decision:
state after:

Iteration 2:
current character or pointer:
condition checked:
decision:
state after:

Return:
```

Use this for hashmap grouping:

```text
Input:

State before loop:

Iteration 1:
word:
computed key:
key already exists?
hashmap after:

Iteration 2:
word:
computed key:
key already exists?
hashmap after:

Return:
```

## Practice Prompts

### Valid Palindrome

Trace this:

```python
s = "No lemon, no melon"
```

Expected:

```python
True
```

Trace this:

```python
s = "hello, world"
```

Expected:

```python
False
```

### Longest Common Prefix

Trace this:

```python
strs = ["interview", "internet", "internal"]
```

Expected:

```python
"inte"
```

Trace this:

```python
strs = ["apple", "banana", "carrot"]
```

Expected:

```python
""
```

### Group Anagrams

Trace this:

```python
strs = ["abc", "bca", "cab", "dog", "god"]
```

Expected:

```python
[["abc", "bca", "cab"], ["dog", "god"]]
```

Order of groups does not matter.

## Final Day 2 Code To Memorize

### Valid Palindrome

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        left = 0
        right = len(s) - 1

        while left < right:
            while left < right and not s[left].isalnum():
                left += 1

            while left < right and not s[right].isalnum():
                right -= 1

            if s[left].lower() != s[right].lower():
                return False

            left += 1
            right -= 1

        return True
```

### Longest Common Prefix

```python
class Solution:
    def longestCommonPrefix(self, strs: list[str]) -> str:
        if not strs:
            return ""

        prefix = strs[0]

        for word in strs[1:]:
            while not word.startswith(prefix):
                prefix = prefix[:-1]

                if prefix == "":
                    return ""

        return prefix
```

### Group Anagrams

```python
class Solution:
    def groupAnagrams(self, strs: list[str]) -> list[list[str]]:
        groups = {}

        for word in strs:
            key = "".join(sorted(word))

            if key in groups:
                groups[key].append(word)
            else:
                groups[key] = [word]

        return list(groups.values())
```

## Day 2 Self-Test

Answer these without looking:

1. What does string traversal mean?
2. Why do we use `.lower()` in Valid Palindrome?
3. What does `.isalnum()` check?
4. Why are two pointers useful for palindromes?
5. What does `s[::-1]` do?
6. What is a prefix?
7. Why can the first string be used as the starting prefix?
8. Why does sorting help Group Anagrams?
9. Why can `"".join(sorted(word))` be used as a dictionary key?
10. Why can `sorted(word)` itself not be used as a dictionary key?

## Day 2 Answers

1. String traversal means visiting each character in a string.
2. Because palindrome comparison should ignore capitalization.
3. It checks whether a character is a letter or number.
4. They let us compare the left and right side while moving inward.
5. It returns the reversed version of the string.
6. A prefix is a substring that starts at the beginning of a string.
7. Any common prefix of all strings must also be a prefix of the first string.
8. Anagrams produce the same sorted character sequence.
9. It creates a string, and strings are immutable, so they can be dictionary keys.
10. `sorted(word)` returns a list, and lists are mutable, so they cannot be dictionary keys.

## Day 2 Interview Phrases

Use these phrases until they become natural:

- "I can traverse the string character by character."
- "I will normalize the string by lowercasing and ignoring non-alphanumeric characters."
- "A two-pointer approach lets me compare both ends without building a new string."
- "For character frequency, I can use a hashmap where the key is the character and the value is the count."
- "For anagrams, sorting gives each group a shared key."
- "I can group strings in a hashmap from key to list of original words."
- "The tradeoff is usually extra memory for cleaner lookup or grouping."

## Day 2 Completion Criteria

You are done with Day 2 when you can:

1. Explain string traversal.
2. Implement Valid Palindrome without looking.
3. Implement Longest Common Prefix without looking.
4. Implement Group Anagrams without looking.
5. Explain why sorted anagrams share the same key.
6. State time and space complexity for all three problems.
