---
published: true
title: LeetCode刷题笔记 (II)
category: Python
tags: 
  - python
layout: post
excerpt_separator: <!--more-->
---
![jpg](/assets/images/20200317leetcode/leetcode-cover.jpg)

Last update: 2020/3/25  
<!--more-->

>ListNode系列：\#2 \#19 \#21  

```python
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None
        
    def list2node(nums):
        if len(nums) == 0:
            return(None)
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
```


## 21. Merge Two Sorted Lists  
2020/3/25


```python
class Solution:
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        cur1, cur2 = l1, l2
        if cur1 is None:
            return(cur2)
        if cur2 is None:
            return(cur1)
            
        if cur1.val < cur2.val:
            res = cur1
            cur1 = cur1.next
        else:
            res = cur2
            cur2 = cur2.next
        cur_res = res

        while cur1 is not None and cur2 is not None:
            if cur1.val < cur2.val:
                cur_res.next = cur1
                cur1 = cur1.next
            else:
                cur_res.next = cur2
                cur2 = cur2.next
            cur_res = cur_res.next
        cur_res.next = cur1 if cur1 is not None else cur2
        
        return(res)

genNode = ListNode.list2node # class ListNode见本文最上方
genList = ListNode.node2list
mysol = Solution()

ans1 = mysol.mergeTwoLists(genNode([1,2,4]), genNode([1,3,4]))
print(genList(ans1)) # [1,1,2,3,4,4]

ans2 = mysol.mergeTwoLists(genNode([1,2,3]), genNode([]))
print(genList(ans2)) # [1,2,3]

ans3 = mysol.mergeTwoLists(genNode([]), genNode([4,5,6]))
print(genList(ans3)) # [4,5,6]
```

    [1, 1, 2, 3, 4, 4]
    [1, 2, 3]
    [4, 5, 6]


## 34.Find First and Last Position of Element in Sorted Array  
2020/3/17

```python
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
    
## to be continued
