---
title: Leetcode[27]Remove Element
date: 2019-12-02 21:00:52
tags: [python, list, pop, leetcode]
category: Leetcode
---
leetcode上一道简单题27.移除元素，给定一个数组 nums 和一个值 val，你需要原地移除所有数值等于 val 的元素，返回移除后数组的新长度。
<!-- more -->
比较常规的想法
```python
class Solution(object):
    def removeElement(self, nums, val):
        """
        :type nums: List[int]
        :type val: int
        :rtype: int
        """
        k = 0
        for i in nums:
            if i != val:
                nums[k] = i
                k += 1
        return k
```
其实python自带的pop方法也很好用
```python
class Solution(object):
    def removeElement(self, nums, val):
        """
        :type nums: List[int]
        :type val: int
        :rtype: int
        """
        # k = 0
        # for i in nums:
        #     if i != val:
        #         nums[k] = i
        #         k += 1
        # return k
        for i in range(len(nums)-1,-1,-1):
            if nums[i] == val:
                nums.pop(i)
        return len(nums)
```
那为啥一定要逆向呢？正向不可以么？还真不行，因为这个循环的i是固定的，假设列表为[3,2,3,1,4,6,3,1]，需要移除的元素是3，正向是从0,1,...,7,当移除至少一个元素后，就无法访问下标为7的元素，因为不存在了，而逆向是7,6,...0，这个时候，下标是递减的，即使被pop了，接下来访问的都是比它小的下标，一定存在。


