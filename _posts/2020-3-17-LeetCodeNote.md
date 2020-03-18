---
published: true
title: LeetCode刷题笔记
category: Python
tags: 
  - python
layout: post
excerpt_separator: <!--more-->
---
![jpg](/assets/images/20200317leetcode/leetcode-cover.jpg)

## 34.Find First and Last Position of Element in Sorted Array  
2020/3/17
<!--more-->


```python
# For LeetCode
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:

        def find_left(nums: List[int], target:int) -> int:
            start = 0
            end = len(nums) - 1
            res = -1
            while start<=end:
                mid = start+int((end-start)/2)
                if nums[mid] < target:
                    start = mid + 1
                elif nums[mid] > target:
                    end = mid - 1
                else: # nums[mid] == target
                    res = mid
                    end = mid - 1
            return(res)

        def find_right(nums: list[int], target:int) -> int:
            start = 0
            end = len(nums) - 1
            res = -1
            while start<=end:
                mid = start+int((end-start)/2)
                if nums[mid] < target:
                    start = mid + 1
                elif nums[mid] > target:
                    end = mid - 1
                else:
                    res = mid
                    start = mid + 1
            return(res)

        return([find_left(nums, target), find_right(nums, target)])            
```


```python
# For my PC
class Solution:
    # def searchRange(self, nums: List[int], target: int) -> List[int]:
    def searchRange(self, nums, target):

        # def find_left(nums: List[int], target:int) -> int:
        def find_left(nums, target):
            start = 0
            end = len(nums) - 1
            res = -1
            while start<=end:
                mid = start+int((end-start)/2)
                if nums[mid] < target:
                    start = mid + 1
                elif nums[mid] > target:
                    end = mid - 1
                else: # nums[mid] == target
                    res = mid
                    end = mid - 1
            return(res)

        # def find_right(nums: list[int], target:int) -> int:
        def find_right(nums, target):
            start = 0
            end = len(nums) - 1
            res = -1
            while start<=end:
                mid = start+int((end-start)/2)
                if nums[mid] < target:
                    start = mid + 1
                elif nums[mid] > target:
                    end = mid - 1
                else:
                    res = mid
                    start = mid + 1
            return(res)

        return([find_left(nums, target), find_right(nums, target)])            

mysol = Solution()
mysol.searchRange(nums = [5,7,7,8,8,10], target=6)
mysol.searchRange(nums = [5,7,7,8,8,10], target=8)
```

    [-1, -1]

    [3, 4]


## 2. Add Two Numbers  
2020/3/18

```python
# For LeetCode
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        r = []
        while l1:
            if l2:
                r.append(l1.val+l2.val)
                l2 = l2.next
            else:
                r.append(l1.val)
            l1 = l1.next
        while l2:
            r.append(l2.val)
            l2 = l2.next
        
        r_ln = ListNode(0)
        cur = r_ln
        for idx in range(len(r)-1):
            r[idx+1] += r[idx] // 10
            r[idx] = r[idx] % 10
            cur.val = r[idx]
            cur.next = ListNode(0)
            cur = cur.next

        cur.val = r[-1] % 10
        if r[-1] // 10:
            cur.next = ListNode(0)
            cur = cur.next
            cur.val = r[-1] // 10
        
        return(r_ln)
```

```python
# For my PC
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None
        
    def list2node(nums):
        res = ListNode(0)
        cur = res
        for i in range(len(nums)):
            cur.val = nums[i]
            if i < len(nums) - 1:
                cur.next = ListNode(0)
                cur = cur.next
            else:
                cur.next = None
        return(res)
    
    def node2list(node):
        res = []
        cur = node
        while cur is not None:
            res.append(cur.val)
            cur = cur.next
        return(res)
        

class Solution:
    # def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
    def addTwoNumbers(self, l1, l2):
        r = []
        while l1:
            if l2:
                r.append(l1.val+l2.val)
                l2 = l2.next
            else:
                r.append(l1.val)
            l1 = l1.next
        while l2:
            r.append(l2.val)
            l2 = l2.next
        
        r_ln = ListNode(0)
        cur = r_ln
        for idx in range(len(r)-1):
            r[idx+1] += r[idx] // 10
            r[idx] = r[idx] % 10
            cur.val = r[idx]
            cur.next = ListNode(0)
            cur = cur.next

        cur.val = r[-1] % 10
        if r[-1] // 10:
            cur.next = ListNode(0)
            cur = cur.next
            cur.val = r[-1] // 10
        
        return(r_ln)


mysol = Solution()
node = ListNode.list2node
node2list = ListNode.node2list


node_res = mysol.addTwoNumbers(node([2,4,3]), node([5,6,4]))
node2list(node_res)

node_res = mysol.addTwoNumbers(node([5]), node([5]))
node2list(node_res)

node_res = mysol.addTwoNumbers(node([0]), node([0, 5, 4]))
node2list(node_res)
```


    [7, 0, 8]

    [0, 1]

    [0, 5, 4]

## 3. Longest Substring Without Repeating Characters  
2020/3/18


```python
# For LeetCode
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        max_len = 0
        cur_len = 0
        chardict = {}
        for i, char in enumerate(s):
            if char in chardict:
                if i - chardict[char] <= cur_len:
                    cur_len = i - chardict[char] - 1
            cur_len += 1
            chardict[char] = i
            max_len = max(max_len, cur_len)
        return(max_len)
```


```python
# For my PC
class Solution:
    # def lengthOfLongestSubstring(self, s: str) -> int:
    def lengthOfLongestSubstring(self, s):
        max_len = 0
        cur_len = 0
        chardict = {}
        for i, char in enumerate(s):
            if char in chardict:
                if i - chardict[char] <= cur_len:
                    cur_len = i - chardict[char] - 1
            cur_len += 1
            chardict[char] = i
            max_len = max(max_len, cur_len)
        return(max_len)
    
    
mysol = Solution()
mysol.lengthOfLongestSubstring("aab") # 2
mysol.lengthOfLongestSubstring("abcabcbb") # 3
mysol.lengthOfLongestSubstring("bbbbb") # 1
mysol.lengthOfLongestSubstring("pwwkew") # 3
```

    2

    3

    1

    3


## to be continued
