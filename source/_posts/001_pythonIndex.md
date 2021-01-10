---
title: python标准库01
date: 2021-01-04 16:46:29
tags: 
    - python
categories: 
    - python
    - 标准库
    - 内置函数
---
# 一、概述:
本项目用来记录学习过程中的代码:[https://github.com/chengfeiZhou/PythonLearn](https://github.com/chengfeiZhou/PythonLearn)
python官方包索引:[https://pypi.org/](https://pypi.org/)

# 二、内置函数:
## 1. abs(x):
返回一个数的绝对值. 参数可以是整数、浮点数或任何实现了[`__abs__()`](https://docs.python.org/zh-cn/3/reference/datamodel.html#object.__abs__)的对象. 如果参数是一个复数, 则返回它的模
```python
# coding=utf-8
class Num(object):
    def __init__(self, value):
        self.value = value
    def __abs__(self):
        return "绝对值"

if __name__ == "__main__":
    a = -1
    b = 3.14
    c = 0
    d = Num(666)
    print(f"{a}的绝对值是: {abs(a)}")   # -1的绝对值是: 1
    print(f"{b}的绝对值是: {abs(b)}")   # 3.14的绝对值是: 3.14
    print(f"{c}的绝对值是: {abs(c)}")   # 0的绝对值是: 0
    print(f"{d}的绝对值是: {abs(d)}")   # <__main__.Num object at 0x7feaaca58350>的绝对值是: 绝对值
```
## 2. all(iterable) & any(iterable):
### 2.1 all(iterable):
如果`iterable`的所有元素均为真值(或可迭代对象为空)则返回`True`.
等价于:
```python
def all(iterable):
    for element in iterable:
        if not element:
            return False
    return True
```
### 2.2 any(iterable):
如果`iterable`的任一元素为真值则返回`True`. 如果可迭代对象为空, 返回`False`. 
等价于:
```python
def any(iterable):
    for element in iterable:
        if element:
            return True
    return False
```
### 2.3 例子:
```python
# coding=utf-8
if __name__ == "__main__":
    arr1 = [1,2,3,4,5,6,7,8,9,0]
    arr2 = ["", {}, 0]
    print(f"arr1的all: {all(arr1)}")    # arr1的all: False
    print(f"arr2的all: {all(arr2)}")    # arr2的all: False
    print(f"arr1的any: {any(arr1)}")    # arr1的any: True
    print(f"arr1的any: {any(arr2)}")    # arr1的any: False
```

## 3. ascii(object)
就像函数`repr()`, 返回一个对象可打印的字符串, 但是`repr()`返回的字符串中非 ASCII 编码的字符,会使用 `\x`、`\u`和`\U`来转义. 生成的字符串和 Python2 的`repr()`返回的结果相似.
```python
# coding=utf-8
class Stu(object):
    def __repr__(self):
        return "这是一个类的repr"

class Teacher(object):
    pass

if __name__ == "__main__":
    print(ascii(12345567))  #  12345567
    print(ascii("hello world!")) # 'hello world!'
    print(ascii("你好!"))    # '\u4f60\u597d!'

    s = Stu()
    t = Teacher()
    print(ascii(s)) # \u8fd9\u662f\u4e00\u4e2a\u7c7b\u7684repr
    print(ascii(t)) # <__main__.Teacher object at 0x7fd100259350
```
## 4. bin(x)
将一个整数转变为一个前缀为"0b"的二进制字符串. 结果是一个合法的 Python 表达式. 如果 x 不是 Python 的 `int` 对象，那它需要定义 `__index__()` 方法返回一个整数. 一些例子:
```shell
>>> bin(3)
'0b11'
>>> bin(-10)
'-0b1010'
```
如果不一定需要前缀“0b”，还可以使用如下的方法。
``` shell
>>> format(14, '#b'), format(14, 'b')
('0b1110', '1110')
>>> f'{14:#b}', f'{14:b}'
('0b1110', '1110')
```

## 5. class bool([x]):
`在 3.7 版更改: x 现在只能作为位置参数`
返回一个布尔, `True` 或者 `False`.  x 使用标准的 [真值测试过程](https://docs.python.org/zh-cn/3/library/stdtypes.html#truth) 来转换. 如果 x 是假的或者被省略, 返回 `False`; 其他情况返回 `True`. `bool` 类是 `int` 的子类(参见 [数字类型 --- int, float, complex](https://docs.python.org/zh-cn/3/library/stdtypes.html#typesnumeric)). 其他类不能继承自它.它只有 `False` 和 `True` 两个实例.


## 6. breakpoint(*args, **kws):
`3.7 新版功能`
`简单可以理解为是一个debug打点函数`
此函数会在调用时将你陷入调试器中. 具体来说, 它调用 `sys.breakpointhook()`, 直接传递 `args` 和 `kws`. 默认情况下, `sys.breakpointhook()` 调用 `pdb.set_trace()` 且没有参数. 在这种情况下, 它纯粹是一个便利函数, 因此您不必显式导入 `pdb` 且键入尽可能少的代码即可进入调试器. 但是, `sys.breakpointhook()` 可以设置为其他一些函数并被 `breakpoint()` 自动调用, 以允许进入你想用的调试器.`
引发一个 审计事件 `builtins.breakpoint` 并附带参数 `breakpointhook`.
[详细参看](https://www.jianshu.com/p/5d3c52c5c2a2)

```python
# coding=utf-8
if __name__ == "__main__":
    str1 = "hello world! d你好, 世界!"

    for i in str1:
        print(i)
        breakpoint()
        print(i)
```
![](/images/pythonIndex/06_breakpoint.png)
Tips:
- 命令 `p` 用于查看表达式的值; 
- 命令 `c` 用于退出调试器, 并继续执行程序;

## 7. class bytearray() & class bytes():
### 7.1 class bytearray([source[, encoding[, errors]]]):

返回一个新的 bytes 数组. bytearray 类是一个可变序列, 包含范围为 `0 <= x < 256` 的整数. 它有`可变序列类型`大部分常见的方法；同时有 `bytes` 类型的大部分方法。
可选形参 source 可以用不同的方式来初始化数组：
- 如果是一个`string`,您必须提供`encoding`参数(errors 参数仍是可选的); `bytearray()`会使用 `str.encode()`方法来将 string 转变成 bytes;
- 如果是一个`integer`, 会初始化大小为该数字的数组, 并使用 null 字节填充.
- 如果是一个遵循 `缓冲区接口` 的对象, 该对象的只读缓冲区将被用来初始化字节数组;
- 如果是一个 `iterable` 可迭代对象, 它的元素的范围必须是 `0 <= x < 256` 的整数, 它会被用作数组的初始内容;
- 如果没有实参, 则创建大小为 `0`的数组;
```python
# coding=utf-8

if __name__ == "__main__":
    strB = bytearray("hello world", encoding="utf-8")
    intB = bytearray(4)
    iterB = bytearray([4, 5, 6, 128])
    iterB = bytearray()

    print(strB)
    print(intB)
    print(iterB)
    print(iterB)

    with open("./7_1_bytearray.py", "r") as f:
        bufferB = bytearray(f.read(), encoding="utf-8")
        print(bufferB)
```
![](/images/pythonIndex/7_1_butearray.png)

### 7.2 class bytes([source[, encoding[, errors]]])
返回一个新的`bytes`对象, 是一个不可变序列, 包含范围为 `0 <= x < 256` 的整数. `bytes` 是 `bytearray` 的不可变版本 - 它有其中不改变序列的方法和相同的索引、切片操作;
因此，构造函数的实参和 `bytearray()` 相同;
字节对象还可以用字面值创建;

### 7.3 补充:
另见 [二进制序列类型 --- bytes, bytearray, memoryview](https://docs.python.org/zh-cn/3/library/stdtypes.html#binaryseq), [bytes 对象](https://docs.python.org/zh-cn/3/library/stdtypes.html#typebytes) 和 [bytes 和 bytearray 操作](https://docs.python.org/zh-cn/3/library/stdtypes.html#bytes-methods);