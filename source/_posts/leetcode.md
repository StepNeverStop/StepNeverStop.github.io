---
title: LeetCode刷题记录(python)
copyright: true
mathjax: false
top: 1
date: 2020-04-24 11:58:00
categories: Python
tags:
- python
- leetcode
keywords:
description:
---

这篇博客用于学习并记录在LeetCode刷题的过程及其相关题目解决思路。

<!--more-->

# 栈

## 20. [有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

判断字符串内括号顺序是否正确，`()[]`正确，`([)]`不正确，因为左方括号与右圆括号不合法。

解题思路1：

将左括号进栈，遍历到右括号就出栈，判断最后栈中是否还有剩余。

```python
class Solution:
    def isValid(self, s: str) -> bool:
        if len(s) % 2 == 1:
            return False
        d = {')':'(', ']':'[', '}':'{'}
        tmp = ['t']	# 终结符，为了避免tmp为空时执行.pop方法出错
        for i in s:
            if i in d.keys():
                if d.get(i) != tmp.pop():
                    return False
            else:
                tmp.append(i)
        return False if len(tmp) != 1 else True
```



解题思路2：

将`()`,`[]`,`{}`替换为空，如果最终字符串为空，则为真。

```python
class Solution:
    def isValid(self, s: str) -> bool:
        if len(s)%2:
            return False
        while '()' in s or '[]' in s or '{}' in s:
            s = s.replace('[]','').replace('()','').replace('{}','')
        return False if len(s) else True
```

## 42. [接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)

这道题挺有意思的，根据数组判断某个地形可容纳的积水。

![](./leetcode/42.png)

```
输入: [0,1,0,2,1,0,1,3,2,1,2,1]
输出: 6
```

```python
class Solution:
    def trap(self, height: List[int]) -> int:
        '''
        思路：
            1. 先找到左侧制高点，将数组分为两部分，右边部分reversed一下
            2. 在两部分数组都执行“左低右高”式累加即可
        '''
        if len(height) < 3: # 数组至少有三个元素才能积水
            return 0
        max_idx = height.index(max(height))
        h1 = height[:max_idx+1]
        h2 = list(reversed(height[max_idx:]))
        def get_v(arr):
            v = 0
            left = arr[0]
            cv = 0  # 某个区间的累计蓄水
            for h in arr:
                if h < left:
                    cv += left - h
                else:
                    v += cv
                    cv = 0
                    left = h
            return v
        return get_v(h1) + get_v(h2)
```

## 71. [简化路径](https://leetcode-cn.com/problems/simplify-path/)

以 Unix 风格给出一个文件的**绝对路径**。右侧不带斜杠`/`，去除相对路径符号`.`（当前目录），`..`（上级目录）。

```python
class Solution:
    def simplifyPath(self, path: str) -> str:
        '''
        思路：
            1. 按'/'切分
            2. 去除空字符串
            3. 去除当前目录符号'.'
            4. 抵消'..'前的目录
            5. 给最后输入加入左斜杠'/'
        '''
        dirs = path.split('/')
        while '' in dirs:
            dirs.pop(dirs.index(''))
        while '.' in dirs:
            dirs.pop(dirs.index('.'))
        while '..' in dirs:
            idx = dirs.index('..')
            dirs.pop(idx)
            if idx > 0:
                dirs.pop(idx-1)
        return '/' + '/'.join(dirs)
```



# 队列

...

