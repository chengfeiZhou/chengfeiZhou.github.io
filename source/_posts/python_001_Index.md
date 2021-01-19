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
## 1. abs(x): 绝对值:
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
## 2. all(iterable) & any(iterable): 判断元素真值:
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

## 3. ascii(object): 返回对象可打印的字符串的转义: 
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
## 4. bin(x): 整数二进制:
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


## 6. breakpoint(*args, **kws):断点调试:
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
```python

# coding=utf-8

if __name__ == "__main__":
    strB = bytes("hello world", encoding="utf-8")
    intB = bytes(4)
    iterB = bytes([4, 5, 6, 128])
    iterB = bytes()

    print(strB)
    print(intB)
    print(iterB)
    print(iterB)

    with open("./06_breakpoint.py", "r") as f:
        bufferB = bytes(f.read(), encoding="utf-8")
        print(bufferB)
```

### 7.3 补充:
另见 [二进制序列类型 --- bytes, bytearray, memoryview](https://docs.python.org/zh-cn/3/library/stdtypes.html#binaryseq), [bytes 对象](https://docs.python.org/zh-cn/3/library/stdtypes.html#typebytes) 和 [bytes 和 bytearray 操作](https://docs.python.org/zh-cn/3/library/stdtypes.html#bytes-methods);

## 8.callable(object): 判断可调用:
如果返回`Flase`, 则`object`不可被调用; 如果返回`True`,则`object`可被调用,但有可能失败; 请注意类是可调用的(调用类将返回一个新的实例); 如果实例所属的类有 `__call__()` 则它就是可调用的;
```python
# coding=utf-8

def test1():
    print("调用test1")

class Test2():
    def __init__(self):
        print("实例化Test2")

test3 = "hello"

if __name__ == "__main__":
    if callable(test1): # 调用test1
        test1()
    else:
        print("不可调用")

    if callable(Test2): # 实例化Test2
        t2 = Test2()
    else:
        print("不可调用")

    if callable(test3): # 不可调用
        test3()
    else:
        print("不可调用")
```

## 9. chr(i): 返回i对应的unicode:
返回 Unicode 码位为整数 i 的字符的字符串格式;例如,`chr(97)` 返回字符串 `'a'`，`chr(8364)` 返回字符串 `'€'`; 这是 `ord()` 的逆函数;
实参的合法范围是 0 到 1,114,111(16 进制表示是 0x10FFFF); 如果 i 超过这个范围，会触发 `ValueError` 异常;

## 10. @classmethod: 一个方法封装成类方法:
把一个方法封装成类方法;
一个类方法把类自己作为第一个实参, 就像一个实例方法把实例自己作为第一个实参; 请用以下习惯来声明类方法:
```python
class C:
    @classmethod
    def f(cls, arg1, arg2, ...):
        ...
```
`@classmethod` 这样的形式称为函数的 [decorator](https://docs.python.org/zh-cn/3/glossary.html#term-decorator) -- 详情参阅 [函数定义](https://docs.python.org/zh-cn/3/reference/compound_stmts.html#function)

类方法的调用可以在类上进行 (例如 `C.f()`) 也可以在实例上进行 (例如 `C().f()`); 其所属类以外的类实例会被忽略。 如果类方法在其所属类的派生类上调用，则该派生类对象会被作为隐含的第一个参数被传入;
```python
# coding=utf-8

class Test(object):
    name = "李四"
    def __init__(self):
        self.name = "张三"
        print("实例化Test")
    
    def say(self):
        print(f"hello, {self.name}")
    
    @classmethod
    def hello(cls):
        print(f"你好, {cls.name}")

if __name__ == "__main__":
    Test.hello()    # 你好, 李四

    Test().say()
    """
    实例化Test
    hello, 张三
    """

    Test().hello()
    """
    实例化Test
    你好, 李四
    """

    Test.name = "王五"
    Test.hello() # 你好, 王五

    Test().hello()
    """
    实例化Test
    你好, 王五
    """

```
类方法与 C++ 或 Java 中的静态方法不同;

在 3.9 版更改: 类方法现在可以包装其他 [装饰器](https://docs.python.org/zh-cn/3/glossary.html#term-decorator);


## 11. compile(source, filename, mode, flags=0, dont_inherit=False, optimize=-1): 编译成代码或AST对象:
将 `source` 编译成代码或 `AST` 对象;代码对象可以被 `exec()` 或 `eval()` 执行; 
- `source`: 可以是常规的字符串、字节字符串，或者 `AST` 对象;
- `filename`: 实参需要是代码读取的文件名; 如果代码不需要从文件中读取, 可以传入一些可辨识的值(经常会使用 `'<string>'`);
- `mode`:  实参指定了编译代码必须用的模式;
    - `source` 是语句序列, 可以是 `'exec'`;
    - 单一表达式, 可以是 `'eval'`;
    - 单个交互式语句, 可以是 `'single'`;
    - 表达式执行结果不是 `None` 将会被打印出来;
- `flags` 和 `dont_inherit`: 可选参数, 制应当激活哪个 [编译器选项](https://docs.python.org/zh-cn/3/library/ast.html#module-ast) 以及应当允许哪个 [future 特性](https://docs.python.org/zh-cn/3/reference/simple_stmts.html#future)
    - 编译器选项和 future 语句是由比特位来指明的; 比特位可以通过一起按位 `OR` 来指明多个选项; 
        - 指明特定 `future` 特性所需的比特位可以在 [`__future__`](https://docs.python.org/zh-cn/3/library/__future__.html#module-__future__) 模块的 `_Feature` 实例的 `compiler_flag` 属性中找到;
        - 编译器旗标 可以在 [`ast`](https://docs.python.org/zh-cn/3/library/ast.html#compiler-flags) 模块中查找带有 `PyCF_` 前缀的名称;
- `optimize` 实参指定编译器的优化级别:
    - 默认值 `-1` 选择与解释器的 `-O` 选项相同的优化级别;
    - 显式级别为`0`: 没有优化; `__debug__` 为真;
    - 显式级别为`1`: 断言被删除, `__debug__` 为假;
    - 显式级别为`2`: (文档字符串也被删除);

如果编译的源码不合法, 此函数会触发 `SyntaxError`异常;
如果源码包含 `null` 字节，则会触发 `ValueError` 异常;
*历史修改:*
- 在 3.2 版更改: 允许使用 Windows 和 Mac 的换行符; 在 `'exec'` 模式不再需要以换行符结尾; 增加了 optimize 形参;
- 在 3.5 版更改: 之前 source 中包含 null 字节的话会触发 `TypeError `异常;
- 3.8 新版功能: `ast.PyCF_ALLOW_TOP_LEVEL_AWAIT`现在可在旗标中传入以启用对最高层级 `await`, `async for` 和 `async with` 的支持;
**警告:**
在将足够大或者足够复杂的字符串编译成 `AST` 对象时, `Python` 解释器有可能因为 `Python AST` 编译器的栈深度限制而崩溃;


## 12. class complex([real[, imag]]): 复数类:
返回值为 `real + imag*1j` 的复数, 或将字符串或数字转换为复数;
- 如果第一个形参是字符串, 则它被解释为一个复数, 并且函数调用时必须没有第二个形参;
- 第二个形参不能是字符串; 
- 每个实参都可以是任意的数值类型(包括复数);
- 如果省略了 `imag`, 则默认值为零, 构造函数会像 `int` 和 `float`一样进行数值转换;
如果两个实参都省略,则返回 `0j`;

对于一个普通 Python 对象 `x`，`complex(x)` 会委托给 `x.__complex__()`;如果 `__complex__()` 未定义则将回退至 `__float__()`; 如果 `__float__()` 未定义则将回退至` __index__()`

> **注解** 当从字符串转换时, 字符串在 `+` 或 `-` 的周围必须不能有空格; 
> 例如 `complex('1+2j')` 是合法的，但 `complex('1 + 2j')` 会触发 `ValueError` 异常;

**历史:**
- 在 3.6 版更改: 您可以使用下划线将代码文字中的数字进行分组。
- 在 3.8 版更改: 如果 `__complex__()` 和 `__float__()` 未定义则回退至 `__index__()`;
```python
# coding=utf-8
class Test3(object): 
    def __init__(self):
        print("实例化test3")

class Test4():
    def __init__(self):
        print("实例化test4")

    def __complex__(self):
        return complex(3, 4)

if __name__ == "__main__":
    t1 = complex("2+3j")
    t2 = complex(4, 6)

    print(t1)   # (2+3j)
    print(t2)   # (4+6j)

    # TypeError: complex() first argument must be a string or a number, not 'Test3'
    # t3 = complex(Test3())   
    # print(t3)

    t4 = complex(Test4())   # 实例化test4
    print(t4)   # (3+4j)
```