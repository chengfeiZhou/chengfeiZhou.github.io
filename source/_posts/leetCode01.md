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

