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
# 232. 用栈实现队列(易):
## 问题:
> 请你仅使用两个栈实现先入先出队列。队列应当支持一般队列的支持的所有操作（push、pop、peek、empty）：
>
>实现 MyQueue 类:
> - void push(int x) 将元素 x 推到队列的末尾
> - int pop() 从队列的开头移除并返回元素
> - int peek() 返回队列开头的元素
> - boolean empty() 如果队列为空，返回 true ；否则，返回 false
>
> 说明：
>
> - 你只能使用标准的栈操作 —— 也就是只有 push to top, peek/pop from top, size, 和 is empty 操作是合法的。
> - 你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。
>
> 进阶：
> - 你能否实现每个操作均摊时间复杂度为 O(1) 的队列？换句话说，执行 n 个操作的总时间复杂度为 O(n) ，即使其中一个操作可能花费较长时间。

## 思路:
...
[参考](https://leetcode-cn.com/problems/implement-queue-using-stacks/solution/yong-zhan-shi-xian-dui-lie-by-leetcode/)

## 方法:
### 方法一:
```python
class MyQueue:

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.S1, self.S2 = collections.deque(), collections.deque()


    def push(self, x: int) -> None:
        """
        Push element x to the back of queue.
        """
        self.S2.append(x)
        while self.S1:
            self.S2.append(self.S1.popleft())
        self.S1, self.S2 = self.S2, self.S1


    def pop(self) -> int:
        """
        Removes the element from in front of queue and returns that element.
        """
        return self.S1.pop()


    def peek(self) -> int:
        """
        Get the front element.
        """
        return self.S1[-1]


    def empty(self) -> bool:
        """
        Returns whether the queue is empty.
        """
        return not self.S1



# Your MyQueue object will be instantiated and called as such:
# obj = MyQueue()
# obj.push(x)
# param_2 = obj.pop()
# param_3 = obj.peek()
# param_4 = obj.empty()
```

# 1441. 用栈操作构建数组:
## 问题:
> 给你一个目标数组 target 和一个整数 n。每次迭代，需要从  list = {1,2,3..., n} 中依序读取一个数字。
> 请使用下述操作来构建目标数组 target ：
> - Push：从 list 中读取一个新元素， 并将其推入数组中。
> - Pop：删除数组中的最后一个元素。
> - 如果目标数组构建完成，就停止读取更多元素。
>
>题目数据保证目标数组严格递增，并且只包含 1 到 n 之间的数字。
> 请返回构建目标数组所用的操作序列。
> 题目数据保证答案是唯一的。

示例 1:
```
输入：target = [1,3], n = 3
输出：["Push","Push","Pop","Push"]
解释： 
读取 1 并自动推入数组 -> [1]
读取 2 并自动推入数组，然后删除它 -> [1]
读取 3 并自动推入数组 -> [1,3]
```

## 思路:
...

## 方法:
### 方法一:
```python
class Solution:
    def buildArray(self, target: List[int], n: int) -> List[str]:
        res = []
        curr = 0
        for i in target:
            if i - curr == 1:
                res.append("Push")
            else:
                res.extend(['Push', 'Pop'] * (i - curr - 1))
                res.append('Push')
            curr = i
        return res
```
### 方法二:
```python
class Solution:
    def buildArray(self, target: List[int], n: int) -> List[str]:
        res = []
        curr = 0
        for i in range(1, n+1):
            if curr >= len(target):
                break
            elif i == target[curr]:
                res.append("Push")
                curr +=1
            else:
                res.extend(['Push', 'Pop'])
        return res
```

# 155. 最小栈:
## 问题:
> 设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。
> - push(x) —— 将元素 x 推入栈中。
> - pop() —— 删除栈顶的元素。
> - top() —— 获取栈顶元素。
> - getMin() —— 检索栈中的最小元素。

## 思路:
[参考](https://leetcode-cn.com/problems/min-stack/solution/zui-xiao-zhan-by-leetcode-solution/)

## 方法:
```python
class MinStack:

    def __init__(self):
        """
        initialize your data structure here.
        """
        self.stack,self.minNum = collections.deque(), collections.deque([math.inf])

    def push(self, x: int) -> None:
        self.minNum.append(min(x, self.minNum[-1]))
        self.stack.append(x)

    def pop(self) -> None:
        self.minNum.pop()
        self.stack.pop()
        
    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.minNum[-1]

# Your MinStack object will be instantiated and called as such:
# obj = MinStack()
# obj.push(x)
# obj.pop()
# param_3 = obj.top()
# param_4 = obj.getMin()
```
# 844. 比较含退格的字符串:
## 问题:
> 给定 S 和 T 两个字符串，当它们分别被输入到空白的文本编辑器后，判断二者是否相等，并返回结果。 # 代表退格字符。
> 注意：如果对空文本输入退格字符，文本继续为空。
## 思路:

## 方法:
```python
class Solution:
    def backspaceCompare(self, S: str, T: str) -> bool:
        # 使用栈
        def rebuild(input_str):
            stack = []
            for s in input_str:
                if s != '#':
                    stack.append(s)
                    continue
                if len(stack) > 0:
                    stack.pop()
                    continue
            return "".join(stack)
        
        return rebuild(S) == rebuild(T)
```
# 1544. 整理字符串
## 问题:
> 给你一个由大小写英文字母组成的字符串 s.
> 一个整理好的字符串中，两个相邻字符 s[i] 和 s[i+1]，其中 0<= i <= s.length-2 ，要满足如下条件:
> - 若 s[i] 是小写字符，则 s[i+1] 不可以是相同的大写字符。
> - 若 s[i] 是大写字符，则 s[i+1] 不可以是相同的小写字符。
> 请你将字符串整理好，每次你都可以从字符串中选出满足上述条件的 两个相邻 字符并删除，直到字符串整理好为止。
> 请返回整理好的 字符串 。题目保证在给出的约束条件下，测试样例对应的答案是唯一的。
> **注意**：空字符串也属于整理好的字符串，尽管其中没有任何字符。

示例:
> 输入：s = "abBAcC"
> 输出：""
> 解释：存在多种不同情况，但所有的情况都会导致相同的结果。例如：
"abBAcC" --> "aAcC" --> "cC" --> ""
"abBAcC" --> "abBA" --> "aA" --> ""
## 思路:
...

## 方法:
```python
class Solution:
    def makeGood(self, s: str) -> str:
        res = []
        for c in s:
            if res and res[-1].lower() == c.lower() and res[-1] != c:
                res.pop()
            else:
                res.append(c)
        return "".join(res)
```

# 20. 有效的括号:
## 问题:
> 给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。
> 有效字符串需满足：
> - 左括号必须用相同类型的右括号闭合。
> - 左括号必须以正确的顺序闭合。
## 思路:

## 方法:
```python
class Solution:
    def isValid(self, s: str) -> bool:
        dmap = {'}': '{', ')':'(', ']': '['}
        stack = []
        for c in s:
            if stack and c in dmap:
                if stack[-1] == dmap[c]:
                    stack.pop()
                else: return False
            else: stack.append(c)
        return not stack
```

# 173. 二叉搜索树迭代器(中):
## 问题:
> 实现一个二叉搜索树迭代器。你将使用二叉搜索树的根节点初始化迭代器。
> 调用 next() 将返回二叉搜索树中的下一个最小的数。
![](/images/leetcode/173_BStree.png)

```
BSTIterator iterator = new BSTIterator(root);
iterator.next();    // 返回 3
iterator.next();    // 返回 7
iterator.hasNext(); // 返回 true
iterator.next();    // 返回 9
iterator.hasNext(); // 返回 true
iterator.next();    // 返回 15
iterator.hasNext(); // 返回 true
iterator.next();    // 返回 20
iterator.hasNext(); // 返回 false
```

**提示**:
- next() 和 hasNext() 操作的时间复杂度是 O(1)，并使用 O(h) 内存，其中 h 是树的高度。
- 你可以假设 next() 调用总是有效的，也就是说，当调用 next() 时，BST 中至少存在一个下一个最小的数。

## 思路:
[参考](https://leetcode-cn.com/problems/binary-search-tree-iterator/solution/er-cha-sou-suo-shu-die-dai-qi-by-leetcode/)
二叉搜索树的一个重要的特性是是二叉搜索树的中序序列是升序序列；因此，中序遍历是该解决方案的核心

## 方法: 

### 方法一: 扁平化二叉搜索树
将二叉搜索树按中序遍历开展;
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class BSTIterator:

    def __init__(self, root: TreeNode):
        # 存放排序后的元素
        self.sort_array = []
        # 记录下一个最小值的索引
        self.index = -1
        # 将树元素入列表
        self._inorder(root)
    
    def _inorder(self, root: TreeNode):
        """将树元素按中序遍历"""
        if not root:
            return
        self._inorder(root.left)
        self.sort_array.append(root.val)
        self._inorder(root.right)

    def next(self) -> int:
        self.index += 1
        return self.sort_array[self.index]


    def hasNext(self) -> bool:
        return self.index + 1 < len(self.sort_array)



# Your BSTIterator object will be instantiated and called as such:
# obj = BSTIterator(root)
# param_1 = obj.next()
# param_2 = obj.hasNext()
```
### 方法二: 受控递归:
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class BSTIterator:

    def __init__(self, root: TreeNode):
        # 记录所有的左节点
        self.stack = []
        # 将树元素入列表
        self._left_inorder(root)
    
    def _left_inorder(self, root: TreeNode):
        """将树的左节点遍历"""
        if not root:
            return
        self.stack.append(root)
        self._left_inorder(root.left)

    def next(self) -> int:
        top_node = self.stack.pop()
        if top_node.right:
            # 遍历右节点的左节点
            self._left_inorder(top_node.right)
        return top_node.val


    def hasNext(self) -> bool:
        return len(self.stack) > 0 



# Your BSTIterator object will be instantiated and called as such:
# obj = BSTIterator(root)
# param_1 = obj.next()
# param_2 = obj.hasNext()
```

# 94. 二叉树的中序遍历:
## 问题:
> 给定一个二叉树的根节点 root ，返回它的 中序 遍历
![](/images/leetcode/94_zhongxu.png)
## 思路:
[参考](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/solution/er-cha-shu-de-hou-xu-bian-li-by-leetcode-solution/)
## 方法:
### 方法一: 基本思路
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def inorderTraversal(self, root: TreeNode) -> List[int]:
        return self._sort_nodes(root, []) if self._sort_nodes(root, []) else []
    
    def _sort_nodes(self, root: TreeNode, res: List[int]):
        if not root:
            return
        self._sort_nodes(root.left, res)
        res.append(root.val)
        self._sort_nodes(root.right, res)
        return res
```

### 方法二: 内函数:
```python

# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def inorderTraversal(self, root: TreeNode) -> List[int]:
        res = []

        def _sort_nodes(root: TreeNode):
            if not root:
                return
            _sort_nodes(root.left)
            res.append(root.val)
            _sort_nodes(root.right)
        
        _sort_nodes(root)
        return res
```
### 方法三: 辅助栈:
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def inorderTraversal(self, root: TreeNode) -> List[int]:
        node_list = []
        node_vals = []
        node = root

        while node_list or node:
            while node:
                node_list.append(node)
                node = node.left
            node = node_list.pop()
            node_vals.append(node.val)
            node = node.right
        return node_vals
```

# 145. 二叉树的后序遍历(中)
## 问题:
> 给定一个二叉树，返回它的 *后序* 遍历。
示例:
```
输入: [1,null,2,3]  
   1
    \
     2
    /
   3 

输出: [3,2,1]
```

## 思路:
[参考](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/solution/)
## 方法:
### 方法一: 递归:
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def postorderTraversal(self, root: TreeNode) -> List[int]:
        res = []

        def sort_nodes(root: TreeNode):
            if not root: return
            sort_nodes(root.left)
            sort_nodes(root.right)
            res.append(root.val)
        sort_nodes(root)
        return res
```
### 方法二: 迭代:
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def postorderTraversal(self, root: TreeNode) -> List[int]:
        if not root:
            return []

        res, stack = [], []
        node = None

        while stack or root:
            while root:
                stack.append(root)
                root = root.left
            root = stack.pop()
            if not root.right or root.right == node:
                res.append(root.val)
                node = root
                root = None
            else:
                stack.append(root)
                root = root.right
        return res
```