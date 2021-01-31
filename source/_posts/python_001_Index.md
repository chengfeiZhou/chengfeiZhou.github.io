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

## 13.delattr(object, name): 删除object的属性;
`setattr()`相关的函数;实参是一个对象和一个字符串;该字符串必须是对象的某个属性;如果对象允许, 该函数将删除指定的属性;
例如 `delattr(x, 'foobar')` 等价于`del x.foobar`
```python
# coding=utf-8
class Test(object):
    def __init__(self, name, age, addr):
        self.name = name
        self.age = age
        self.addr = addr
    
    def __str__(self):
        return f"name: {self.name}, age: {self.age}, addr: {self.addr}"


if __name__ == "__main__":
    tt = Test("zhangsan", 18, "北京")
    print(f"source: {tt}")  # source: name: zhangsan, age: 18, addr: 北京
    delattr(tt, "addr")  # delattr(tt, 'addr') 等价于 del tt.addr
    print(tt.name)  # 张三
    print(tt.age)   # 18
    print(tt.addr)  # AttributeError: 'Test' object has no attribute 'addr'
```

## 14. class dict(): 字典对象
`class dict(**kwarg)`;
`class dict(mapping, **kwarg)`;
`class dict(iterable, **kwarg)`;
创建一个新的字典; dict 对象是一个字典类;

## 15. dir([object]): 返回调用列表:
- 如果没有实参，则返回当前本地作用域中的名称列表;
- 如果有实参，它会尝试返回该对象的有效属性列表;

如果对象有一个名为 `__dir__()` 的方法, 那么该方法将被调用, 并且必须返回一个属性列表; 这允许实现自定义 `__getattr__()` 或 `__getattribute__()` 函数的对象能够自定义 `dir()` 来报告它们的属性。

如果对象不提供 `__dir__()`, 这个函数会尝试从对象已定义的 `__dict__` 属性和类型对象收集信息; 结果列表并不总是完整的, 如果对象有自定义 `__getattr__()`，那结果可能不准确;

默认的 `dir()` 机制对不同类型的对象行为不同, 它会试图返回最相关而不是最全的信息:
- 如果对象是模块对象, 则列表包含模块的属性名称;
- 如果对象是类型或类对象, 则列表包含它们的属性名称, 并且递归查找所有基类的属性; 
- 否则, 列表包含对象的属性名称, 它的类属性名称, 并且递归查找它的类的所有基类的属性;

返回的列表按字母表排序:
```python
# coding=utf-8
import struct


class Shape(object):
    def __dir__(self):
        return ['area', 'perimeter', 'location']


if __name__ == "__main__":
    print(dir())  # 打印当前本地作用域中的名称列表
    """
    ['Shape', '__annotations__', '__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'struct']
    """
    print(dir(struct))  # 打印struct模块的名称列表
    """
    ['Struct', '__all__', '__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', '_clearcache', 'calcsize', 'error', 'iter_unpack', 'pack', 'pack_into', 'unpack', 'unpack_from']
    """
    ss = Shape()
    print(dir(ss))
    """
    ['area', 'location', 'perimeter']
    """
```
> 因为 `dir()` 主要是为了便于在交互式时使用, 所以它会试图返回人们感兴趣的名字集合, 而不是试图保证结果的严格性或一致性, 它具体的行为也可能在不同版本之间改变; 例如, 当实参是一个类时, `metaclass` 的属性不包含在结果列表中;

## 16. divmod(a,b): 返回商和余数:
将两个(非复数)数字作为实参, 并在执行整数除法时返回一对"商"和"余数"; 
- 对于混合操作数类型, 适用双目算术运算符的规则; 
- 对于整数，结果和 `(a // b, a % b)` 一致;
- 对于浮点数，结果是 `(q, a % b)` , `q` 通常是 `math.floor(a / b)` 但可能会比 1 小; 
    - 在任何情况下, `q * b + a % b` 和 `a` 基本相等; 
    - 如果 `a % b` 非零, 它的符号和 `b` 一样，并且 `0 <= abs(a % b) < abs(b)`;

```python
# coding=utf-8

if __name__ == "__main__":
    print(divmod(5, 2))  # (2, 1)
    print(divmod(3.3, 2.2))  # (1.0, 1.0999999999999996)
```
## 17. enumerate(iterable, start=0): 返回一个枚举对象
`通常用于for循环`
- `iterable` 必须是一个序列, 或`iterator`, 或其他支持迭代的对象;
- `enumerate()` 返回的迭代器的 `__next__()` 方法返回一个元组, 里面包含一个计数值(从 `start` 开始，默认为 0)和通过迭代 `iterable` 获得的值;

```python
# coding=utf-8

if __name__ == "__main__":
    seasons = ['Spring', 'Summer', 'Fall', 'Winter']
    print(list(enumerate(seasons)))  # [(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
    print(list(enumerate(seasons, start=1)))  # [(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
```

## 18. eval & exec:
### 18.1 eval(expression[, globals[, locals]]): 返回表达式结果
- `expression`实参是一个字符串;
- 可选的`globals`实参必须是一个字典;
- 可选的`locals`可以是任何映射对象;

`expression` 参数会作为一个 Python 表达式(从技术上说是一个条件列表)被解析并求值, 并使用 `globals` 和 `locals` 字典作为全局和局部命名空间;
如果`globals` 字典存在且不包含以 `__builtins__` 为键的值, 则会在解析 `expression` 之前插入以此为键的对内置模块 `builtins` 的引用; 这意味着 `expression` 通常具有对标准 `builtins` 模块的完全访问权限且受限的环境会被传播;
如果省略 `locals` 字典则其默认值为 `globals` 字典;
如果两个字典同时省略, 则表达式执行时会使用 `eval()` 被调用的环境中的 `globals` 和 `locals`;
> 请注意, `eval(`) 并没有对外围环境下的 (非局部)嵌套作用域 的访问权限;
### 18.2 exec(object[, globals[, locals]]): 动态执行 Python 代码;
- `object` 必须是字符串或者代码对象;
    - 如果是字符串, 那么该字符串将被解析为一系列 Python 语句并执行(除非发生语法错误);
    - 如果是代码对象, 它将被直接执行; 
    - 在任何情况下，被执行的代码都需要和文件输入一样是有效的;

> 请注意即使在传递给 `exec()` 函数的代码的上下文中, `return` 和 `yield` 语句也不能在函数定义之外使用; 该函数返回值是 `None`


## 19. filter(function, iterable): 列表过滤
用 `iterable` 中函数 `function` 返回"真"的那些元素, 构建一个新的迭代器;
- `iterable` 可以是一个序列, 一个支持迭代的容器, 或一个迭代器; 如果 `function` 是 `None` , 则会假设它是一个身份函数,即 `iterable` 中所有返回假的元素会被移除;
> 请注意, `filter(function, iterable)` 相当于一个生成器表达式, 当 `function` 不是 `None` 的时候为 `(item for item in iterable if function(item))`; `function` 是 `None` 的时候为 `(item for item in iterable if item)`

```python
# coding-utf-8
def func(x):
    return x % 2 == 0


if __name__ == "__main__":
    ll = [0, 1, 2, 3, 4, 5, 6, 7, 8]
    tl = filter(func, ll)
    print(ll, list(tl))  # [0, 1, 2, 3, 4, 5, 6, 7, 8] [0, 2, 4, 6, 8]
```
> 请参阅 [`itertools.filterfalse()`](https://docs.python.org/zh-cn/3/library/itertools.html#itertools.filterfalse) 了解, 只有 `function` 返回 `false` 时才选取 `iterable` 中元素的补充函数

## 20. class float([x]): 返回从数字或字符串 x 生成的浮点数
- 如果实参是字符串, 则它必须是包含十进制数字的字符串, 字符串前面可以有符号, 之前也可以有空格;
- 可选的符号有 '+' 和 '-'; '+' 对创建的值没有影响;
- 实参也可以是 `NaN(非数字)`、正负无穷大的字符串;
- 除去首尾的空格后，输入必须遵循以下语法：

|说明|表示|符号|
|-|-|-|
|sign|签名(符号)|`"+" | "-"`|
|infinity|无穷|`"Infinity" | "inf"`
|nan|非数字|"nan"|
|numeric_value|数值|`floatnumber | infinity | nan`|
|numeric_string|数值字符串|[sign] numeric_value|
```python
# coding=utf-8

if __name__ == "__main__":
    print(float(21))  # 21.0
    print(float("25"))  # 25.0
    print(float("nan"))  # nan
    print(float("-29"))  # -29.0
    print(float("inf"))  # inf
    print(float('+1E6'))  # 1000000.0
```

## 21. format(value[, format_spec]): 格式化字符串
将 `value` 转换为 `format_spec` 控制的“格式化”表示;
`format_spec` 的解释取决于 `value` 实参的类型, 但是大多数内置类型使用标准格式化语法: [格式规格迷你语言](https://docs.python.org/zh-cn/3/library/string.html#formatspec)

## 22. class frozenset([iterable]): 返回一个新的 frozenset 对象
它包含可选参数 `iterable` 中的元素;
`frozenset` 是一个内置的类;
[具体文档](https://docs.python.org/zh-cn/3/library/stdtypes.html#types-set)

## 23. getattr(object, name[, default]): 返回对象命名属性的值
`name` 必须是字符串; 如果该字符串是对象的属性之一, 则返回该属性的值. 
例如, `getattr(x, 'foobar')` 等同于 `x.foobar`
如果指定的属性不存在, 且提供了 `default` 值, 则返回`default` 值, 否则触发 `AttributeError`;

## 24. globals(): 返回表示当前全局符号表的字典
这总是当前模块的字典(在函数或方法中, 不是调用它的模块, 而是定义它的模块)
```python
# coding=utf-8
class Test(object):
    def __init__(self):
        self.name = "zhangsan"

    def run(self):
        print("runing")


if __name__ == "__main__":
    print(globals())
    """
    {'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x7f9e5edb6fd0>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, '__file__': '24_globals.py', '__cached__': None, 'Test': <class '__main__.Test'>}
    """
```

## 25.hasattr(object, name): 判断name是object的属性
该实参是一个对象和一个字符串; 
如果字符串是对象的属性之一的名称, 则返回 True, 否则返回 False; (此功能是通过调用 `getattr(object, name)` 看是否有 `AttributeError` 异常来实现的)


## 26.hash(object): 返回该对象的哈希值
返回该对象的哈希值(如果它有的话); 哈希值是整数; 它们在字典查找元素时用来快速比较字典的键; 相同大小的数字变量有相同的哈希值(即使它们类型不同, 如 1 和 1.0)
> 注解 如果对象实现了自己的 `__hash__()` 方法, 请注意, `hash()` 根据机器的字长来截断返回;

## 27. help([object]): 启动内置的帮助系统(此函数主要在交互式中使用)
如果没有实参, 解释器控制台里会启动交互式帮助系统; 
如果实参是一个字符串, 则在模块、函数、类、方法、关键字或文档主题中搜索该字符串，并在控制台上打印帮助信息;
如果实参是其他任意对象, 则会生成该对象的帮助页;

## 28. hex(x): 将整数十六进制字符串
将整数转换为以“0x”为前缀的小写十六进制字符串;
如果 `x` 不是 Python `int` 对象, 则必须定义返回整数的 `__index__()` 方法;
```python
hex(255)
'0xff'
hex(-42)
'-0x2a'
```
如果要将整数转换为大写或小写的十六进制字符串, 并可选择有无“0x”前缀, 则可以使用如下方法:
```python
'%#x' % 255, '%x' % 255, '%X' % 255
('0xff', 'ff', 'FF')
format(255, '#x'), format(255, 'x'), format(255, 'X')
('0xff', 'ff', 'FF')
f'{255:#x}', f'{255:x}', f'{255:X}'
('0xff', 'ff', 'FF')
```

## 29. id(object):返回对象的“标识值”
该值是一个整数, 在此对象的生命周期中保证是唯一且恒定的; 两个生命期不重叠的对象可能具有相同的 `id()` 值
```python
id(123)
# 4487555232
```

## 30. input([prompt]): 写入标准输出
如果存在 `prompt` 实参, 则将其写入标准输出, 末尾不带换行符; 接下来, 该函数从输入中读取一行，将其转换为字符串(除了末尾的换行符)并返回; 当读取到 EOF 时, 则触发 EOFError;
```python
>>> s = input('--> ')  
--> Monty Python's Flying Circus
>>> s  
"Monty Python's Flying Circus"
```
> 如果加载了`readline` 模块, `input()` 将使用它来提供复杂的行编辑和历史记录功能

## 31. class int: 返回一个整数对象
- `class int([x])`
- `class int(x, base=10)`

返回一个基于数字或字符串 x 构造的整数对象, 或者在未给出参数时返回 0; 
如果 x 定义了 `__int__()`, `int(x)` 将返回`x.__int__()`; 
如果 x 定义了 `__index__()`, 它将返回 `x.__index__()`;
如果 x 定义了 `__trunc__()`, 它将返回 `x.__trunc__()`; 
对于浮点数, 它将向零舍入;

如果 x 不是数字, 或者有 `base` 参数, x 必须是"字符串、bytes、表示进制为 base 的 整数字面值 的 bytearray 实例";
该文字前可以有 + 或 - (中间不能有空格), 前后可以有空格; 
一个进制为 n 的数字包含 0 到 `n-1` 的数, 其中 a 到 z(或 A 到 Z )表示 10 到 35; 
默认的 base 为 10, 允许的进制有 0、2-36; 2、8、16 进制的数字可以在代码中用 0b/0B 、 0o/0O 、 0x/0X 前缀来表示;
进制为 0 将安照代码的字面量来精确解释, 最后的结果会是 2、8、10、16 进制中的一个; 所以 int('010', 0) 是非法的，但 int('010') 和 int('010', 8) 是合法的;