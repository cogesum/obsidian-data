https://shadhelper.notion.site/e03c5086f74b495488e2af71058c51c3

---

https://leetcode.com/problems/valid-anagram/description/
Given two strings `s` and `t`, return `true` if `t` is an anagram of `s`, and `false` otherwise.
```
Example 1:
Input: s = "anagram", t = "nagaram"
Output: true

Example 2:
Input: s = "rat", t = "car"
Output: false

Constraints:
- `1 <= s.length, t.length <= 5 * 104`
- `s` and `t` consist of lowercase English letters.
```

```go
// 1. by map
// 2. by char

func anagramByMap(s, t string) bool {
// base map for s
// iterate by sMap and con by t
// iterate and check if all keys are 0

	baseMap := make(map[char]int, 0)
	for _, c := range s {
		baseMap[string(c)] += 1
	}
	
	for _, c := range t {
		if ok, v := baseMap[c]; ok {
			baseMap[string(c)] -= 1
		}
	}
	
	for _, v := range baseMap {
		if v != 0 {
			return false
		}
	}
	
	return true
}


func anagramByChar(s, t string) bool {
	result := 0
	
	for _, c := range s {
		result += c
	}
	
	for _, c := range t {
		result -= c
	}
	
	retrun result == 0
}

```


**Follow up:** What if the inputs contain Unicode characters? How would you adapt your solution to such a case?

---
https://leetcode.com/problems/contains-duplicate/description/
Given an integer array `nums`, return `true` if any value appears **at least twice** in the array, and return `false` if every element is distinct.

**Example 1:**

**Input:** nums = [1,2,3,1]

**Output:** true

**Explanation:**

The element 1 occurs at the indices 0 and 3.

**Example 2:**

**Input:** nums = [1,2,3,4]

**Output:** false

**Explanation:**

All elements are distinct.

**Example 3:**

**Input:** nums = [1,1,1,3,3,4,3,2,4,2]

**Output:** true

---
https://leetcode.com/problems/longest-consecutive-sequence/description/
Given an unsorted array of integers `nums`, return _the length of the longest consecutive elements sequence._

You must write an algorithm that runs in `O(n)` time.

**Example 1:**

**Input:** nums = [100,4,200,1,3,2]
**Output:** 4
**Explanation:** The longest consecutive elements sequence is `[1, 2, 3, 4]`. Therefore its length is 4.

**Example 2:**

**Input:** nums = [0,3,7,2,5,8,4,6,0,1]
**Output:** 9

**Example 3:**

**Input:** nums = [1,0,1,2]
**Output:** 3