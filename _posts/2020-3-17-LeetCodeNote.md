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
Last update: 2020/3/23  
<!--more-->

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


## 4. Median of Two Sorted Arrays  
2020/3/19


```python
class Solution:
    # def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
    def findMedianSortedArrays(self, nums1, nums2):
        
        len_t = len(nums1) + len(nums2)
        even = True if len_t % 2 == 0 else False
        idx_tar = len_t // 2
        idx_nums = -1
        if nums2:
            j = nums2[0]
        
        for i in nums1:
            if nums2:
                while j <= i:
                    idx_nums += 1
                    if idx_nums == idx_tar:
                        val_2 = j
                        break
                    elif even & (idx_nums == idx_tar - 1):
                        val_1 = j
                    nums2.pop(0)
                    if len(nums2) == 0:
                        break
                    j = nums2[0]
            idx_nums += 1
            if idx_nums == idx_tar:
                val_2 = i
            elif even & (idx_nums == idx_tar - 1):
                val_1 = i
            if idx_nums >= idx_tar:
                break
        
        if nums2:
            for j in nums2:
                idx_nums += 1
                if idx_nums == idx_tar:
                    val_2 = j
                elif even & (idx_nums == idx_tar - 1):
                    val_1 = j
                if idx_nums >= idx_tar:
                    break

        if even:
            return((val_1 + val_2)/2)
        else:
            return(val_2)
        
        
mysol = Solution()
mysol.findMedianSortedArrays([1,2], [3,4]) # 2.5
mysol.findMedianSortedArrays([1,3], [2]) # 2
mysol.findMedianSortedArrays([3,4], [1,2]) # 2.5
```

    2.5

    2

    2.5


## 5. Longest Palindromic Substring  
2020/3/19  
执行用时 :2268 ms, 在所有 Python3 提交中击败了54.75%的用户  
内存消耗 :13.5 MB, 在所有 Python3 提交中击败了31.36%的用户  
比较暴力的解法，有空再深入研究

```python
class Solution:
    # def longestPalindrome(self, s: str) -> str:
    def longestPalindrome(self, s):
        def localString(idx, s, type): # 找s[idx]处的局部最优解
            len_s = len(s)
            idx_left = idx-1
            idx_right = idx if type=="even" else idx+1
            if idx_left>=0 and idx_right<=len_s-1:
                cur_str = "" if type=="even" else s[idx]
                while s[idx_left] == s[idx_right]:
                    cur_str = s[idx_left]+cur_str+s[idx_right]
                    if idx_left == 0 or idx_right == len_s-1:
                        break
                    idx_left -= 1
                    idx_right += 1
            else:
                cur_str = ""
            return(cur_str)

        len_s = len(s)
        if len_s == 0:
            return(s)
        # 初始化
        max_len = 1
        max_string = s[0]

        for idx, char in enumerate(s):
            # 以s[idx]为中心，长度为奇数的回文数
            cur_str = localString(idx, s, "odd")
            cur_len = len(cur_str)
            if cur_len > max_len:
                max_len = cur_len
                max_string = cur_str

            # 以s[idx-1], s[idx]为中心，长度为偶数的回文数
            cur_str = localString(idx, s, "even")
            cur_len = len(cur_str)
            if cur_len > max_len:
                max_len = cur_len
                max_string = cur_str

        return(max_string)


mysol = Solution()
mysol.longestPalindrome("a") # "a"
mysol.longestPalindrome("bb") # bb
mysol.longestPalindrome("babad") # "bab"
mysol.longestPalindrome("cbbd") # "bb"
```

    'a'

    'bb'

    'bab'

    'bb'


## 6. ZigZag Conversion  
2020/3/20


```python
class Solution:
    # def convert(self, s: str, numRows: int) -> str:
    def convert(self, s, numRows):
        if numRows==1:
            return(s)
            
        res = []
        len_s = len(s)
        step = 2*numRows-2

        # i_row = 1
        idx = 0 # i_row-1
        while idx<len_s:
            res.append(s[idx])
            idx += step

        # 1<i_row<numRows
        for i_row in range(2, numRows):
            idx = i_row-1
            idx2 = 2*numRows-i_row-1
            while idx2<len_s:
                res.append(s[idx])
                res.append(s[idx2])
                idx += step
                idx2 += step
            if idx<len_s:
                res.append(s[idx])

        # i_row = numRows
        idx = numRows-1 # i_row-1
        while idx<len_s:
            res.append(s[idx])
            idx += step

        return("".join(res))
    

mysol = Solution()
mysol.convert("PAYPALISHIRING", 1) # "PAYPALISHIRING"
mysol.convert("PAYPALISHIRING", 3) # "PAHNAPLSIIGYIR"
mysol.convert("PAYPALISHIRING", 4) # "PINALSIGYAHRPI"
```

    'PAYPALISHIRING'

    'PAHNAPLSIIGYIR'

    'PINALSIGYAHRPI'


## 7. Reverse Integer  
2020/3/20


```python
class Solution:
    # def reverse(self, x: int) -> int:
    def reverse(self, x):
        if (x>2**31-1) or (x<-2**31):
            return(0)

        sign = (x<0)
        x = abs(x)
        res = 0

        while x:
            res = res*10 + x%10
            x = x//10

        res = -res if sign else res
        if (res>2**31-1) or (res<-2**31):
            return(0)
        else:
            return(res)
        

mysol=Solution()
mysol.reverse(123) # 321
mysol.reverse(-123) # -321
mysol.reverse(120) # 21
mysol.reverse(1534236469) # 0
```

    321

    -321

    21

    0


## 8. String to Integer (atoi)  
2020/3/20


```python
class Solution:
    # def myAtoi(self, str: str) -> int:
    def myAtoi(self, str):
        if str == "":
            return(0)
        INT_MAX = 2**31 - 1
        INT_MIN = -2**31
        
        idx = 0
        len_str = len(str)
        while str[idx] == " ":
            idx += 1
            if idx > len_str - 1:
                return(0)

        sign = "-" if str[idx] == "-" else "+"
        if str[idx] in ["+", "-"]:
            idx += 1

        res = 0
        for char in str[idx:]:
            ord_char = ord(char)
            if ord_char not in range(48, 58): # 包括+-号
                break
            else: # [0-9]:
                res = res*10 + ord_char - 48

        if sign == "-":
            res = -res
            res = max(res, INT_MIN)
        else:
            res = min(res, INT_MAX)
        
        return(res)

            
mysol = Solution()
mysol.myAtoi("") # 0
mysol.myAtoi("   ") # 0 
mysol.myAtoi("words and 987") # 0
mysol.myAtoi("-91283472332") # -2147483648
mysol.myAtoi("   -42") # -42
```

    0

    0

    0

    -2147483648

    -42


## 9. Palindrome Number  
2020/3/21


```python
class Solution:
    # def isPalindrome(self, x: int) -> bool:
    def isPalindrome_a(self, x):
        if x<0:
            return(False)
        if x == int(str(x)[::-1]):
            return(True)
        else:
            return(False)
        
        
    def isPalindrome_b(self, x):
        if x<0:
            return(False)
        x0 = x
        res = 0
        while x:
            res = res * 10 + x % 10
            x = x // 10
        return(res == x0)


mysol = Solution()
mysol.isPalindrome_a(0)# ; mysol.isPalindrome_b(0) # True
mysol.isPalindrome_a(121)# ; mysol.isPalindrome_b(121) # True
mysol.isPalindrome_a(-121)# ; mysol.isPalindrome_b(-121) # False
mysol.isPalindrome_a(10)# ; mysol.isPalindrome_b(10) # False
```

    True

    True

    False

    False



## 10. Regular Expression Matching  
2020/3/21


```python
class Solution:
    # def isMatch(self, s: str, p: str) -> bool:
    def isMatch(self, s, p):
        if p == "":
            return(s=="")

        if len(p) == 1:
            if len(s) != 1:
                return(False)
            else:
                if p[0] == ".":
                    return(True)
                else:
                    return(s[0] == p[0])

        if p[1] != "*":
            if len(s)==0:
                return(False)
            if p[0] != "." and s[0] != p[0]:
                return(False)
            else:
                return(self.isMatch(s[1:], p[1:]))
        else:
            idx = 0
            len_s = len(s)
            if len_s == 0:
                return(self.isMatch(s, p[2:]))
            while idx<=len_s-1:
                if p[0] != "." and s[idx] != p[0]:
                    return(self.isMatch(s[idx:], p[2:]))
                if self.isMatch(s[idx:], p[2:]):
                    return(True)
                idx += 1
            return(self.isMatch("", p[2:]))
            """
            idx = 0时，isMatch(s[idx:], p[2:])是将p[0:1]即p[0]*对应了s中的""
            idx = 1时，将p[0]*对应了s[0](前提是s[0]与p[0]匹配)
            idx = 2时，将p[0]*对应了s[0:1](前提是s[0]、s[1]均与p[0]匹配)
            ......
            """

mysol = Solution()
mysol.isMatch("aa", "a") # False
mysol.isMatch("aa", "a*") # True
mysol.isMatch("ab", ".*") # True
mysol.isMatch("aab", "c*a*b") # True
mysol.isMatch("mississippi", "mis*is*p*.") # False
```


    False

    True

    True

    True

    False

## 11. Container With Most Water  
2020/3/22

```python
class Solution:
    # def maxArea(self, height: List[int]) -> int:
    def maxArea(self, height):
        num_container = len(height)
        max_idx1 = height.index(max(height))
        max_idx2 = num_container-1-height[::-1].index(max(height))
        max_area = 0
        max_height_i = 0
        for i in range(0, max_idx1+1):
            if height[i] <= max_height_i:
                continue
            max_height_i = height[i]
            max_height_j = 0
            for j in range(num_container-1, max_idx2-1, -1):
                if height[j]<=max_height_j:
                    continue
                max_height_j = height[j]
                area_ij = (j-i)*min(height[i], height[j])
                max_area = max(max_area, area_ij)
        return(max_area)
    
mysol = Solution()
mysol.maxArea([1,8,6,2,5,4,8,3,7]) # 49
```

    49


## 12. Integer to Roman  
2020/3/22


```python
class Solution:
    num_list = [1000, 500, 100, 50, 10, 5, 1]
    rom_list = list("MDCLXVI")
    def intToRoman(self, num, idx=0, ini=""):
        if num==0:
            return(ini)
        while idx <= 6:
            inum = self.num_list[idx]
            if num>=inum:
                if idx%2 == 0: # 1000, 100, 10, 1
                    n = num//inum
                    if n<4:
                        return(self.intToRoman(num%inum, idx+1, ini+self.rom_list[idx]*n))
                    else: # 4000, 400, 40, 4
                        return(self.intToRoman(num%inum, idx+1, ini+self.rom_list[idx]+self.rom_list[idx-1]))
                else: # 500, 50, 5
                    if num < 9*self.num_list[idx+1]:
                        return(self.intToRoman(num%inum, idx+1, ini+self.rom_list[idx]))
                    else: # 900, 90, 9
                        return(self.intToRoman(num%self.num_list[idx+1], idx+2, ini+self.rom_list[idx+1]+self.rom_list[idx-1]))
            idx += 1
            
mysol = Solution()
mysol.intToRoman(3) # "III"
mysol.intToRoman(4) # "IV"
mysol.intToRoman(9) # "IX"
mysol.intToRoman(58) # "LVIII"
mysol.intToRoman(1994) # "MCMXCIV"
mysol.intToRoman(3999) # "MMMCMXCIX"
```

    'III'

    'IV'

    'IX'

    'LVIII'

    'MCMXCIV'

    'MMMCMXCIX'



## 13. Roman to Integer  
2020/3/23


```python
class Solution:
    rom_int = {"I": 1, "V": 5, "X": 10, "L": 50, "C": 100, "D": 500, "M": 1000}
    # def romanToInt(self, s: str) -> int:
    def romanToInt(self, s):
        res = 0
        last = 1000
        idx = 0
        while idx < len(s):
            if self.rom_int[s[idx]] <= last:
                last = self.rom_int[s[idx]]
                res += last
            else:
                res += (self.rom_int[s[idx]]-last-last) # 前面多加了一次last也要减掉
                last = self.rom_int[s[idx]]
            idx += 1
        return(res)
    
mysol = Solution()
mysol.romanToInt("III") # 3
mysol.romanToInt("IV") # 4
mysol.romanToInt("IX") # 9
mysol.romanToInt("LVIII") # 58
mysol.romanToInt("MCMXCIV") # 1994
mysol.romanToInt("MMMCMXCIX") # 3999
```

    3

    4

    9

    58

    1994

    3999



## 14. Longest Common Prefix  
2020/3/23


```python
class Solution:
    # def longestCommonPrefix(self, strs: List[str]) -> str:
    def longestCommonPrefix(self, strs):
        if len(strs) == 0:
            return("")
        
        prefix = strs[0]
        for str_i in strs[1:]:
            len_i = min(len(prefix), len(str_i))
            prefix = prefix[:len_i]
            if prefix == "":
                break
            for idx in range(0, len_i):
                if str_i[idx] != prefix[idx]:
                    prefix = prefix[:idx]
                    break

        return(prefix)
    
mysol = Solution()
mysol.longestCommonPrefix(["aa", "a"]) # "a"
mysol.longestCommonPrefix(["flower","flow","flight"]) # "fl"
```

    'a'

    'fl'


## 15. 3Sum  
2020/3/23


```python
class Solution:
    def toRight(self, nums, idx, rightBoundry):
        while idx < rightBoundry: # 跳过nums[idx]右边所有值与nums[idx]相同的元素，若无等值元素则idx不变
            if nums[idx] == nums[idx+1]:
                idx += 1
            else:
                break
        return(idx+1)

    def toLeft(self, nums, idx, leftBoundry):
        while idx > leftBoundry: # 跳过nums[idx]左边所有值与nums[idx]相同的元素，若无等值元素则idx不变
            if nums[idx] == nums[idx-1]:
                idx -= 1
            else:
                break
        return(idx-1)

    # def threeSum(self, nums: List[int]) -> List[List[int]]:
    def threeSum(self, nums):
        len_nums = len(nums)
        if len_nums<3:
            return([])
        nums.sort()
        ans = []
        idx1 = 0
        while idx1 < len_nums-2:
            left = nums[idx1]
            if left > 0:
                break
            idx2, idx3 = idx1+1, len_nums-1
            while idx2 < idx3:
                mid, right = nums[idx2], nums[idx3]
                if left+mid+right==0:
                    ans.append([left, mid, right])
                    idx2 = self.toRight(nums, idx2, idx3)
                    idx3 = self.toLeft(nums, idx3, idx2)
                elif left+mid+right<0:
                    idx2 = self.toRight(nums, idx2, idx3)
                else:
                    idx3 = self.toLeft(nums, idx3, idx2)
            idx1 = self.toRight(nums, idx1, len_nums-1)

        return(ans)
    
mysol = Solution()
mysol.threeSum([-1,0,1,2,-1,-4]) # [[-1,-1,2],[-1,0,1]]
```

    [[-1, -1, 2], [-1, 0, 1]]

## to be continued
