---
title: '[LeetCode]波义尔摩尔投票算法'
date: '2020-10-02'
# lastmode: '2022-01-04'
tags: ['log', 'algorithm']
# draft: true
summary: 'LeetCode 169. Majority Element'
# images: ['']
authors: ['default']
layout: PostLayout
---

## 1. 简介

> The Boyer–Moore majority vote algorithm is an algorithm for finding the majority of a sequence of elements using linear time and constant space. It is named after Robert S. Boyer and J Strother Moore, who published it in 1981, and is a prototypical example of a streaming algorithm.

波义尔摩尔投票算法是一种使用线性时间复杂度和常数空间复杂度来找到数组的主要元素（出现超过一半次数的元素）[^1]

## 2. 描述

简单来说就是对数组（假设存在主要元素，即出现次数超过一半的元素）进行遍历**计数**，遇到相同元素就加一，不同元素就减一，减到零则重新开始计数。最后计数不为零的元素就是数组的主要元素。

## 3. 代码示例

```python
def majority_element(nums):
    count = 0
    res = None
    for num in nums:
        if count == 0:
            res = num
        count += 1 if num == res else -1
    return res
```

## 4. QA

Q: 为什么最后计数不为零的元素就是数组的主要元素？
A: 可以这样理解：其他元素都抵消不了该元素，换言之这个元素就比其他元素之和还要多。

Q：如果最后计数为零怎么办？
A: 在**存在主要元素**的假设前提下，不会出现这种情况。

Q: 在例子`[1, 2, 3, 4, 5, 5, 5]`中，最后计数不为零，但 5 不是主要元素。
A: 同上，不符合假设前提。

## 5. 参考

[^1]: [Wiki](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_majority_vote_algorithm)
