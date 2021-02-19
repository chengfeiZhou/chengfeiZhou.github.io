---
title: leetCode算法之栈
date: 2021-02-18 15:15:16
tags:
    - LeetCode
    - 算法
    - 栈
categories:
    - LeetCode
    - 算法
    - 栈
---
# 1021. 删除最外层的括号(易):
## 问题:
[https://leetcode-cn.com/problems/remove-outermost-parentheses/](https://leetcode-cn.com/problems/remove-outermost-parentheses/)
> 有效括号字符串为空 ("")、"(" + A + ")" 或 A + B，其中 A 和 B 都是有效的括号字符串，+ 代表字符串的连接。例如，""，"()"，"(())()" 和 "(()(()))" 都是有效的括号字符串。
> 如果有效字符串 S 非空，且不存在将其拆分为 S = A+B 的方法，我们称其为原语（primitive），其中 A 和 B 都是非空有效括号字符串。
> 给出一个非空有效字符串 S，考虑将其进行原语化分解，使得：S = P_1 + P_2 + ... + P_k，其中 P_i 是有效括号字符串原语。
> 对 S 进行原语化分解，删除分解中每个原语字符串的最外层括号，返回 S 。

## 思路:
[参考](https://leetcode-cn.com/problems/remove-outermost-parentheses/solution/shuang-zhi-zhen-ji-shu-fa-by-simzhou/)
关键在于如何判断哪些括号是**最外层括号**;
## 方法:
### 方法一: 双指针法:
- 设置两个变量用来表示左括号(left)的下表和右括号(right)匹配情况;
- 遍历传入的字符串S;
- 当遇到的是左括号(时, right+1, 表示需要多一个)来匹配;
- 当遇到右括号)时, right-1, 表示有一个)与最近的(进行了匹配;
- 当right==0时, 说明所有的括号都得到了匹配. 此时记录下left和当前位置, 并将left的位置置为下一个位置;
- 最后,剔除记录的括号位置
```python
class Solution:
    def removeOuterParentheses(self, S: str) -> str:
        # 双指针, 寻找原语化的最外层括号下标
        primitive_indices = []
        left, right = 0, 0
        for i in range(len(S)):
            if S[i] == "(":
                right += 1
            elif S[i] == ")":
                right -= 1
            
            if right == 0:
                primitive_indices.append((left, i))
                left = i + 1
            
        # 根据下标, 提取原语
        return "".join(S[m+1: n] for m, n in primitive_indices)
```
⏲ 时间复杂度：O(n)O(n)
💾 空间复杂度：O(n)O(n)，使用了额外数组存放最外层括号下标

### 方法2：单指针计数:
- 定义两个变量, 保存结果的res 和 记录右括号匹配情况的right;
- 遍历字符串S, 获取单个字符c;
- `if c == '(' and right > 0`: 说明右括号匹配不完全, 不是最外层括号, 所以, 将c保存到res;
- `if c == ')' and right > 1`: 虽然遇到了右括号,但还需一个右括号做匹配, 说明不是最外层的括号, 所以, 将c保存到res;
```python
class Solution:
    def removeOuterParentheses(self, S: str) -> str:
        res, right = "", 0
        for c in S:
            if c == '(' and right > 0:
                res += c
            if c == ')' and right > 1:
                res += c
            
            right += 1 if c == '(' else -1  # 三元表达式
        return res
```
⏲ 时间复杂度：O(n)O(n)
💾 空间复杂度：O(n)O(n)，使用了额外数组存放结果

### 方法3: 辅助栈:
定义一个单调栈，它的单调性在于：栈里面只存在左括号，右括号仅仅是用来消灭左括号的;
```python
class Solution:
    def removeOuterParentheses(self, S: str) -> str:
        res, stack = "", []
        for c in S:
            # 什么情况下，某个括号要加入结果中呢？非外层括号.
            # 怎么判断是非外层括号? 1. 左括号加入结果前，栈不为空。2. 右括号加入结果并消括号后，栈不为空
            if c == "(":
                if stack: res += c
                stack.append(c)
            if c == ")":
                stack.pop()
                if stack: res += c
        return res
```
⏲ 时间复杂度：O(n)O(n)
💾 空间复杂度：O(n)O(n)，使用了栈

# 1047. 删除字符串中的所有相邻重复项(易):
## 问题:
[https://leetcode-cn.com/problems/remove-all-adjacent-duplicates-in-string/](https://leetcode-cn.com/problems/remove-all-adjacent-duplicates-in-string/)

> 给出由小写字母组成的字符串 S，重复项删除操作会选择两个相邻且相同的字母，并删除它们。
> 在 S 上反复执行重复项删除操作，直到无法继续删除。
> 在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。

示例：
```
输入："abbaca"
输出："ca"
解释：
例如，在 "abbaca" 中，我们可以删除 "bb" 由于两字母相邻且相同，这是此时唯一可以执行删除操作的重复项。之后我们得到字符串 "aaca"，其中又只有 "aa" 可以执行重复项删除操作，所以最后的字符串为 "ca"。
```
## 思路:
匹配弹出，不匹配留下;

## 方法:
```python
class Solution:
    def removeDuplicates(self, S: str) -> str:
        stack = []
        for c in S:
            if stack and stack[-1] == c:
                stack.pop()
            else:
                stack.append(c)
        return "".join(stack)
```

# 682. 棒球比赛(易)
## 问题:
[https://leetcode-cn.com/problems/baseball-game](https://leetcode-cn.com/problems/baseball-game)
> 你现在是一场采用特殊赛制棒球比赛的记录员。这场比赛由若干回合组成，过去几回合的得分可能会影响以后几回合的得分。
> 比赛开始时，记录是空白的。你会得到一个记录操作的字符串列表 ops，其中 ops[i] 是你需要记录的第 i 项操作，ops 遵循下述规则：
> 1. 整数 x - 表示本回合新获得分数 x
> 2. "+" - 表示本回合新获得的得分是前两次得分的总和。题目数据保证记录此操作时前面总是存在两个有效的分数。
> 2. "D" - 表示本回合新获得的得分是前一次得分的两倍。题目数据保证记录此操作时前面总是存在一个有效的分数。
> 3. "C" - 表示前一次得分无效，将其从记录中移除。题目数据保证记录此操作时前面总是存在一个有效的分数。
>
> 请你返回记录中所有得分的总和

示例:
```
输入：ops = ["5","-2","4","C","D","9","+","+"]
输出：27
解释：
"5" - 记录加 5 ，记录现在是 [5]
"-2" - 记录加 -2 ，记录现在是 [5, -2]
"4" - 记录加 4 ，记录现在是 [5, -2, 4]
"C" - 使前一次得分的记录无效并将其移除，记录现在是 [5, -2]
"D" - 记录加 2 * -2 = -4 ，记录现在是 [5, -2, -4]
"9" - 记录加 9 ，记录现在是 [5, -2, -4, 9]
"+" - 记录加 -4 + 9 = 5 ，记录现在是 [5, -2, -4, 9, 5]
"+" - 记录加 9 + 5 = 14 ，记录现在是 [5, -2, -4, 9, 5, 14]
所有得分的总和 5 + -2 + -4 + 9 + 5 + 14 = 27
```

## 思路:
...
## 方法:
```python
class Solution:
    def calPoints(self, ops: List[str]) -> int:
        stack = []
        for c in ops:
            if c == "+":
                if len(stack) > 1:
                    stack.append(stack[-1]+stack[-2])
            elif c == "D":
                if len(stack) > 0:
                    stack.append(stack[-1]*2)
            elif c == "C":
                if len(stack) > 0:
                    stack.pop()
            else:
                stack.append(int(c))
        return sum(stack)
```

# 1598. 文件夹操作日志搜集器(易)
## 问题:
(https://leetcode-cn.com/problems/crawler-log-folder)[https://leetcode-cn.com/problems/crawler-log-folder]
> 每当用户执行变更文件夹操作时，LeetCode 文件系统都会保存一条日志记录;
> 下面给出对变更操作的说明:
> - "../" ：移动到当前文件夹的父文件夹。如果已经在主文件夹下，则 继续停留在当前文件夹;
> - "./" ：继续停留在当前文件夹;
> - "x/" ：移动到名为 x 的子文件夹中。题目数据 保证总是存在文件夹 x;
>
> 给你一个字符串列表 logs ，其中 logs[i] 是用户在 ith 步执行的操作;
> 文件系统启动时位于主文件夹，然后执行 logs 中的操作。
> 执行完所有变更文件夹操作后，请你找出 返回主文件夹所需的最小步数.

## 思路:
...
## 方法:
### 方法一:
```python
class Solution:
    def minOperations(self, logs: List[str]) -> int:
        res = 0
        for f in logs:
            if f == "../":
                res = max(0, res-1)
            elif f != "./":
                res += 1
        return res
```
## 方法二:
```python
class Solution:
    def minOperations(self, logs: List[str]) -> int:
        stack = []
        for f in logs:
            if f == "./":
                pass
            elif f == "../":
                if stack:
                    stack.pop()
            else:
                stack.append(f)
        return len(stack)
```
# 496. 下一个更大元素I(易):
## 问题:
[https://leetcode-cn.com/problems/next-greater-element-i/](https://leetcode-cn.com/problems/next-greater-element-i/)
> 给你两个 没有重复元素 的数组 nums1 和 nums2 ，其中nums1 是 nums2 的子集。
> 请你找出 nums1 中每个元素在 nums2 中的下一个比其大的值。
> nums1 中数字 x 的下一个更大元素是指 x 在 nums2 中对应位置的右边的第一个比 x 大的元素。如果不存在，对应位置输出 -1;

## 思路:
[参考](https://leetcode-cn.com/problems/next-greater-element-i/solution/dan-diao-zhan-zong-jie-by-wu-xian-sen-2/)
### 单调栈总结:
- 单调递增: 从 栈底 到 栈顶 递增, 栈顶大;
- 单调递减: 从 栈底 到 栈顶 递减, 栈顶小;

### 什么时候使用调用栈:
现有一维数组, 寻找任意一元素右边(左边)第一个比自己大(小)的元素, 且要求O(n)的时间复杂度;
### 模板套路:
```python
# coding=utf-8
# 1) 当前项向右找第一个比自己大的位置 -- 从右向左维护一个单调递减栈
def nextGreaterElement_01(nums: list):
    res, stack = [-1] * len(nums), []

    for i in range(len(nums)-1, -1, -1):
        while stack and stack[-1] <= nums[i]:
            stack.pop()
        if stack:
            res[i] = stack[-1]
        stack.append(nums[i])

    return res

# 当前项向右找第一个比自己大的位置 -- 从左向右维护一个单调递减栈
def nextGreaterElement_011(nums: list):
    res, stack = [-1] * len(nums), []

    for i in range(len(nums)):
        while stack and nums[stack[-1]] < nums[i]:
            res[stack.pop()] = nums[i]
        stack.append(i)
    return res

# 2) 当前项向右第一个比自己小的位置 -- 从右向左维护一个单调递增栈:
def nextGreaterElement_02(nums: list):
    res, stack = [-1] * len(nums), []
    for i in range(len(nums)):
        while stack and stack[-1] >= nums[i]:
            stack.pop()
        if stack:
            res[i] = stack[-1]
        stack.append(nums[i])
    return res


# 3) 当前项向左找第一个比自己大的位置 -- 从左向右维护一个单调递减栈:
def nextGreaterElement_03(nums: list):
    res, stack = [-1] * len(nums), []

    for i in range(len(nums)):
        while stack and stack[-1] <= nums[i]:
            stack.pop()
        if stack:
            res[i] = stack[-1]
        stack.append(nums[i])
    return res


# 4) 当前项向左找第一个比自己小的位置 -- 从左向右维护一个单调递增的栈:
def nextGreaterElement_04(nums: list):
    res, stack = [-1] * len(nums), []

    for i in range(len(nums)):
        while stack and stack[-1] >= nums[i]:
            stack.pop()
        if stack:
            res[i] = stack[-1]
        stack.append(nums[i])
    return res


if __name__ == "__main__":
    nums = [1,3,4,2]
    print(f"目标列表: {nums}")
    res1 = nextGreaterElement_01(nums)
    print(f"->大: {res1}") # ->大: [3, 4, -1, -1]

    res11 = nextGreaterElement_011(nums)
    print(f"->大: {res11}") # ->大: [3, 4, -1, -1]

    res2 = nextGreaterElement_02(nums)
    print(f"->小: {res2}") # ->小: [-1, 1, 3, 1]

    res3 = nextGreaterElement_03(nums)
    print(f"<-大: {res3}") # <-大: [-1, -1, -1, 4]

    res4 = nextGreaterElement_04(nums)
    print(f"<-小: {res4}") # <-小: [-1, 1, 3, 1]
```

## 方法:
### 方法一: 暴力破解:
找元素右侧比自己大的第一个位置
```python
class Solution:
    def nextGreaterElement(self, nums1: List[int], nums2: List[int]) -> List[int]:
        dic = {}

        for i in range(len(nums2)):
            j = i + 1
            while j < len(nums2) and nums2[i] >= nums2[j]:
                j += 1
            if j < len(nums2) and nums2[i] < nums2[j]:
                dic[nums2[i]] = nums2[j]
        return [dic.get(x, -1) for x in nums1]
```
### 方法二: 单调栈递增
> 当前项向右找第一个比自己大的位置 —— 从右向左维护一个单调递减栈
```python
class Solution:
    def nextGreaterElement(self, nums1: List[int], nums2: List[int]) -> List[int]:
        dic, stack = {}, []
        for i in range(len(nums2)-1, -1, -1):
            while stack and stack[-1] <= nums2[i]:
                stack.pop()
            if stack:
                dic[nums2[i]] = stack[-1]
            stack.append(nums2[i])
        return [dic.get(x, -1) for x in nums1]
```
### 方法三: 单调栈递减:
> 从左到右维护单调递减栈, 找元素右侧区域，第一个比自己大的位置
```python
class Solution:
    def nextGreaterElement(self, nums1: List[int], nums2: List[int]) -> List[int]:
        dic, stack = {}, []
        for i in range(len(nums2)):
            while stack and stack[-1] < nums2[i]:
                dic[stack.pop()] = nums2[i]
            stack.append(nums2[i])
        return [dic.get(x, -1) for x in nums1]
```

# 225. 用队列实现栈
## 问题:
[https://leetcode-cn.com/problems/implement-stack-using-queues/](https://leetcode-cn.com/problems/implement-stack-using-queues/)
> 请你仅使用两个队列实现一个后入先出（LIFO）的栈，并支持普通队列的全部四种操作(push、top、pop 和 empty);
> 实现 MyStack 类：
> - void push(int x) 将元素 x 压入栈顶。
> - int pop() 移除并返回栈顶元素。
> - int top() 返回栈顶元素。
> - boolean empty() 如果栈是空的，返回 true ；否则，返回 false 。
>
> **注意:**
> - 你只能使用队列的基本操作 —— 也就是 push to back、peek/pop from f- ront、size 和 is empty 这些操作;
> - 你所使用的语言也许不支持队列; 你可以使用 list(列表) 或者 deque(双端队列)来模拟一个队列, 只要是标准的队列操作即可;

## 思路:
[参考](https://leetcode-cn.com/problems/implement-stack-using-queues/solution/yong-dui-lie-shi-xian-zhan-by-leetcode-solution/)

## 方法:
## 方法一:
```python
class MyStack:
    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.Q1, self.Q2 = [], []


    def push(self, x: int) -> None:
        """
        Push element x onto stack.
        """
        self.Q2.append(x)
        self.Q2.extend(self.Q1)
        self.Q1, self.Q2 = self.Q2, [], 


    def pop(self) -> int:
        """
        Removes the element on top of the stack and returns that element.
        """
        return self.Q1.pop(0)


    def top(self) -> int:
        """
        Get the top element.
        """
        return self.Q1[0]


    def empty(self) -> bool:
        """
        Returns whether the stack is empty.
        """
        return False if self.Q1 else True


# Your MyStack object will be instantiated and called as such:
# obj = MyStack()
# obj.push(x)
# param_2 = obj.pop()
# param_3 = obj.top()
# param_4 = obj.empty()
```
## 方法二:
```python
# coding=utf-8

import collections

class MyStack(object):
    # 两个对列
    def __init__(self):
        self.Q1, self.Q2 = collections.deque(), collections.deque()
    
    def push(self, x: int):
        """
        Push element x onto stack.
        """
        self.Q2.append(x)
        while self.Q1:
            self.Q2.append(self.Q1.popleft())
        self.Q1, self.Q2 = self.Q2, self.Q1
    
    def pop(self):
        """
        Removes the element on top of the stack and returns that element.
        """
        return self.Q1.popleft()


    def top(self):
        """
        Get the top element.
        """
        return self.Q1[0]


    def empty(self):
        """
        Returns whether the stack is empty.
        """
        return not self.Q1

if __name__ == "__main__":
    myStack = MyStack()
    myStack.push(1)
    myStack.push(2)
    print(myStack.top())
    print(myStack.pop())
    print(myStack.empty())
```
