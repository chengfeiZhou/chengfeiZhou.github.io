---
title: 一、Golang之指针&切片&字典&结构体
date: 2021-03-18 11:20:03
tags: 
	- Golang高级
    - 指针&切片&字典&结构体
categories: 
    - Golang高级
    - 指针&切片&字典&结构体
---
# 一、指针:
指针是一个代表着某个内存地址的值. 这个内存地址往往是在内存中存储的另一个变量的值的起始位置. Go语言对指针的支持介于java和c/c++之间, 它既没有向java那样取消了代码对指针的直接操作, 也避免了c/c++中由于对指针的滥用而造成的安全和可靠性问题;

## 1. Go语言中的指针:
Go语言保留了指针, 与C语言指针有所不同; 主要体现在:
- Go语言默认值`nil`, C默认值`NULL`;
- 操作符: `&`取变量指针; `*`通过指针访问变量的值;
- 不支持指针运算, 不支持`->`运算符, 直接用`.`访问对象;
```go
package main

import "fmt"

func main() {
	var a int = 100                     // 声明int类型变量a
	fmt.Printf("&a = %p\n", &a)         // &取a的地址: &a = 0xc0000140a0

	var p *int = nil                    // 声明变量p, 类型为*int
	p = &a                              // p指向a
	fmt.Printf("p = %p\n", p)           // p = 0xc0000140a0
	fmt.Printf("a=%d, *p=%d\n", a, *p)  // a=100, *p=100

	*p = 234 // 修改*p的值
	fmt.Printf("a=%d, *p=%d\n", a, *p)	// a=234, *p=234
}
```

## 2. 栈帧:
### 2.1 计算机内存区域划分:
以32位操作系统4GB内存为例:
- 3G~4GB:kernel(内核)区域: 主要存放操作系统内核相关数据(如: 硬件驱动);
- 0~3GB: user(用户区)
    - 0~255: 操作系统预留区域;
    - `.text`: 代码区, "只读"读写属性
    - `.rodata`: 只读常量数据区, "只读"读写属性
    - `.data`: 数据区, 存放初始化完的全局变量;
    - `.bss`: 未初始化数据区, 存放声明但未初始化的全局变量;
    - `heap`: 堆区
    - 共享数据区:
    - `stack`: 栈区
![](/images/golang/001_memory.png)

### 2.2 栈帧:
用来给函数运行提供内存空间, 取内存于`stack` 上; 当函数调用时, 产生栈帧. 函数调用结束, 释放栈帧;
栈帧存储: 
- 局部变量;
- 形参; (形参与局部变量存储地位等同;
- 内存字段描述值; (栈顶指针, 栈基指针)
![](/images/golang/002_栈帧.png)


## 3. 函数new
表达式`new(T)`将创建一个T类型的匿名变量, 所做的事为T类型的新值分配并清理一块内存空间, 然后将这块空间的地址作为返回值返回, 而这个结果就是指向这个新的T类型值的指针值, 返回的指针类型为`*T`;
new创建的内存空间位于heap[堆]上, 空间的默认值为数据类型默认值; 如: `new(int)`则`*p`为0; `new(bool)`则`*p`为`false`;
```go
package main

import "fmt"

func main() {
	var p1 *int
	p1 = new(int)            // p1为*int类型, 指向匿名的int变量
	fmt.Println("p1 = ", p1) // p1 =  0xc0000b4008

	p2 := new(bool)          // p2位*bool类型, 指向匿名的bool变量
	fmt.Println("*p2=", *p2) // *p2= false
	*p2 = true
	fmt.Println("*p2=", *p2) // *p2= true
}
```
只需要使用`new()`函数, 无需担心其内存的生命周期或怎样将其删除, 因为Go语言的内存管理(GC)会处理一切;

## 4. 指针作为函数参数:
## 4.1 函数传值:
- 传值: 数据据, 将实参的 ***值*** 拷贝一份给形参;
- 传引用: 传地址值, 将实参的地址值作为函数参数传递;
## 4.2 例:
```go
package main
import "fmt"

func swap01(a, b int) {
	a, b = b, a
	fmt.Printf("swap01 a=%d, b=%d\n", a, b)
}

func swap02(a, b *int) {
	*a, *b = *b, *a
}

func main() {
	a, b := 10, 20
	fmt.Println(a, b) // 10 20

	swap01(a, b) // 传值 => swap01 a=20, b=10

	swap02(&a, &b)    // 传引用
	fmt.Println(a, b) // 20 10
}
```
## 4. 指针使用注意：
- 空指针：未被初始化的指针; `var p *int   *p --> err`
- 野指针：被一片无效的地址空间初始化;

# 二、slice -- 切片:
## 1. 切片简述:
数组的长度在定义之后无法修改; 数组是值类型, 每次传递都将产生一份副本; 所以, Go提供数组切片(slice)来弥补数据组;
slice(切片)代表变长序列, 序列中每个元素都有固定的类型. 一个slice乐行一般写作`[]T`, 其中`T`代表slice中元素的类型;slice的语法和array(数组)很像, 只是没有固定长度;
数组和slice之间有着紧密的联系. 一个slice是一个轻量级的数据结构, 提供了访问数组子序列(或者全部)元素的功能, 而且slice的底层确实引用一个数组对象; 一个slice由三个部分构成：`指针(data)、长度(len)和容量(cap)`; 指针指向第一个slice元素对应的底层数组元素的地址, 要注意的是slice的第一个元素并不一定就是数组的第一个元素;
切片并不是数组或数组指针, 它通过内部指针和相关属性引⽤数组⽚段, 以实现变⻓⽅案;
slice并不是真正意义上的动态数组, 而是一个引用类型; slice总是指向一个底层array, slice的声明也可以像array一样, 只是不需要长度;
![](/images/golang/003_slice.png)

## 2. 创建slice:
### 2.1 声明slice与array的区别:
- 声明数组时, `[ ]`内写明了数组的长度或使用`...`自动计算长;
- 声明slice时，`[ ]`内没有任何字符;
### 2.2 经常使用的创建slice的方法:
- 自动推导类型创建slice:
    - `s1 := [] int {1, 2, 3, 4}  创建 有 4 个元素的切片，分别为：1234`
- 借助make创建 slice: 格式: `make(切片类型，长度，容量)`
    - `s2 := make([]int, 5, 10)	len(s2) = 5, cap(s2) = 10`
- make时，没有指定容量，那么 长度==容量
    - `s3 := make([]int, 5)  len(s3) = 5, cap(s3) = 5`

```go
package main

import "fmt"

func main() {
	s1 := []int{1, 2, 3, 4} // 创建 有4个元素的切片
	fmt.Println("s1=", s1)  // s1= [1 2 3 4]

	s2 := make([]int, 5, 10) // 借助make创建 slice，格式：make(切片类型，长度，容量)
	s2[4] = 7
	//s2[5] = 9          		// 报错：panic: runtime error: index out of range
	fmt.Println("s2=", s2)                                   // s2= [0 0 0 0 7]
	fmt.Printf("len(s2)=%d, cap(s2)=%d\n", len(s2), cap(s2)) // len(s2)=5, cap(s2)=10

	s3 := make([]int, 5) // make时，没指定容量，那么 长度 == 容量
	s3[2] = 3
	fmt.Println("s3=", s3)                                   // s3= [0 0 3 0 0]
	fmt.Printf("len(s2)=%d, cap(s2)=%d\n", len(s3), cap(s3)) // len(s2)=5, cap(s2)=5
}
```
**PS**:
- make只能创建slice、map和channel，并且返回一个有初始值(非零)的对象;

## 3. slice操作:
### 3.1 切片截取:
|操作|	含义|
|-|-|
|s[n]	|切片s中索引位置为n的项|
|s[:]|	从切片s的索引位置0到len(s)-1处所获得的切片|
|s[low:]	|从切片s的索引位置low到len(s)-1处所获得的切片|
|s[:high]	|从切片s的索引位置0到high处所获得的切片，len=high|
|s[low:high]	|从切片s的索引位置low到high处所获得的切片，len=high-low|
|s[low : high : max]|	从切片s的索引位置low到high处所获得的切片，len=high-low，cap=max-low|
|len(s)|	切片s的长度，总是<=cap(s)|
|cap(s)|	切片s的容量，总是>=len(s)|

截取可表示为`s[low：high：max]`; 
- low: 表示下标的起点;
- high: 表示下标的终点(左闭右开, 不包括此下标); 
- 长度: `len = high – low`;
- 容量: `cap = max – low`;

长度对应slice中元素的数目; 长度不能超过容量, 容量一般是从slice的开始位置到底层数据的结尾位置; 
内置的`len()`和`cap()` 函数分别返回slice的长度和容量;

**示例**:
```go
array := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
```
|操作|	结果|	len	|cap|	说明|
|-|-|-|-|-|
|array[:6:8]|	[0 1 2 3 4 5]|	6|	8	|省略 low|
|array[5:]|	[5 6 7 8 9]|	5|	5|	省略 high、 max|
|array[:3]|	[0 1 2]|	3|	10|	省略 high、 max|
|array[:]|	[0 1 2 3 4 5 6 7 8 9]|	10	|10|	全部省略|

### 3.2 切片和底层数组关系:
```go
package main

import "fmt"

func main() {
	arr := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
	s1 := arr[2:5]         // 从arr[2]开始，取 5-2 个元素，组成切片s1。
	fmt.Println("s1=", s1) // s1= [2 3 4]

	s1[1] = 666              // 这样将arr数组中 3 --> 666。
	fmt.Println("arr=", arr) // arr= [0 1 2 666 4 5 6 7 8 9]

	s2 := s1[2:7]          // 从s1[2]开始， 取 7-2 个元素，组成 s2。
	fmt.Println("s2=", s2) // 实际上还是取的 数组arr。   s2= [4 5 6 7 8]

	s2[2] = 777              // 这会将arr中的 6 --> 777
	fmt.Println("arr=", arr) // arr= [0 1 2 666 4 5 777 7 8 9]
}
```
利用数组创建切片, 切片在操作过程中, 是直接操作原数组; **切片是数组的引用**; 因此, 在go语言中, 常常使用切片代替数组;
### 3.3 切片做函数参数:
切片作为函数参数时, **传引用**
```go
package main

import "fmt"

func testFunc(s []int) { // 切片做函数参数
	s[0] = -1 // 直接修改 main中的 slice
}

func main() {
	slice := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
	fmt.Println(slice) // [0 1 2 3 4 5 6 7 8 9]

	testFunc(slice)    // 传引用
	fmt.Println(slice) // [-1 1 2 3 4 5 6 7 8 9]
}
```

## 4. 常用操作函数:

### 4.1 append函数:
`append()` 函数可以向 `slice` 尾部添加数据, 可以自动为切片扩容; 常常会返回新的 `slice `对象:
```go
package main

import "fmt"

func main() {
	var s1 []int             //创建nil切片，或者：s1 := make([]int, 0)
	s1 = append(s1, 1)       //追加1个元素
	s1 = append(s1, 2, 3)    //追加2个元素
	s1 = append(s1, 4, 5, 6) // 追加3个元素
	fmt.Println(s1)          // [1 2 3 4 5 6]

	s2 := make([]int, 5)
	s2 = append(s2, 6)
	fmt.Println(s2) // [0 0 0 0 0 6]

	s3 := []int{1, 2, 3}
	s3 = append(s3, 4, 5)
	fmt.Println(s3) // [1 2 3 4 5]
}
```
append函数会智能的将底层数组的容量增长, 一旦超过原底层数组容量, 通常以2倍(1024以下)容量重新分配底层数组, 并复制原来的数据;因此, 使用append 给切片做扩充时, 切片的地址可能发生变化; 但, 数据都被重新保存了, 不影响使用;
```go
func main() {
	s := make([]int, 0, 1)
	c := cap(s)
	for i := 0; i < 100; i++ {
		s = append(s, i)
		if n := cap(s); n > c {
			fmt.Printf("cap: %d -> %d\n", c, n)
			c = n
		}
	}
}

// cap: 1 -> 2
// cap: 2 -> 4
// cap: 4 -> 8
// cap: 8 -> 16
// cap: 16 -> 32
// cap: 32 -> 64
// cap: 64 -> 128
```
**练习1**:
给定一个字符串列表, 在原有slice上返回不包含空字符串的列表, 如:
```
{"red", "", "black", "", "", "pink", "blue"}
——> {"red", "black", "pink", "blue"}
```

```go
package main
import "fmt"

// 直接在原串上操作
func noEmpty2(data []string) []string {
	i := 0
	for _, str := range data {
		if str != "" {
			data[i] = str
			i++
		}
		// 取到空字符串，不作为。
	}
	return data[:i]
}

func main() {
	ss1 := []string{"red", "", "black", "", "", "pink", "blue"}
	ss2 := noEmpty2(ss1)
	fmt.Printf("ss2 = %v\n", ss2) // ss2 = [red  black   pink blue]
}
```

**练习2**:
写一个函数, 就地消除`[]string`中重复字符串, 如: 
```
{"red", "black", "red", "pink", "blue", "pink", "blue"}
——>	{"red", "black", "pink", "blue"}
```

```go
package main
import "fmt"

func noSame(data []string) []string {
	out := data[:1]
	// 遍历原始切片字符串
	for _, word := range data {
		i := 0
		// 比较取出的 word 是否在 out 中存在 -- for
		for ; i < len(out); i++ {
			if word == out[i] {
				break
			}
		}
		if i == len(out) {
			out = append(out, word)
		}
	}
	return out
}

func main() {
	rr1 := []string{"red", "black", "red", "pink", "blue", "pink", "blue"}
	rr2 := noSame(rr1)
	fmt.Printf("srr2 = %v\n", rr2) // srr2 = [red black pink blue]
}
```

### 4.2 copy函数:
函数 copy 在两个 slice 间复制数据, 复制⻓度以 len 小的为准, 两个 slice 指向同⼀底层数组。直接对应位置覆盖;
```go
package main
import "fmt"

func main() {
	data := [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
	s1 := data[8:]    //{8, 9}
	s2 := data[:5]    //{0, 1, 2, 3, 4}
	copy(s2, s1)      // dst:s2, src:s1
	fmt.Println(s2)   //[8 9 2 3 4]
	fmt.Println(data) //[8 9 2 3 4 5 6 7 8 9]
}
```

**练习3**
要删除slice中间的某个元素并保存原有的元素顺序, 如:
`{5, 6, 7, 8, 9} ——> {5, 6, 8, 9}`

```go
package main
import "fmt"

func remove(data []int, idx int) []int {
	copy(data[idx:], data[idx+1:])
	return data[:len(data)-1]
}

func main() {
	dd := []int{5, 6, 7, 8, 9}
	dd2 := remove(dd, 2)
	fmt.Println(dd2)	// [5 6 8 9]

}
```

# 三、map -- 字典:
## 1. map简述:
Go语言中的map(映射、字典)是一种内置的数据结构, 它是一个无序的key-value对的集合, 比如以身份证号作为唯一键来标识一个人的信息;Go语言中并没有提供一个set类型, 但是map中的key也是不相同的, 可以用map实现类似set的功能;
![](/images/golang/004_map.png)
map格式为：
`map[keyType]valueType`

在一个map里所有的键都是唯一的, 而且必须是支持`==`和`!=`操作符的类型, 切片、函数以及包含切片的结构类型这些类型由于具有引用语义, 不能作为映射的键, 使用这些类型会造成编译错误:
`dict := map[ []string ]int{} //err, invalid map key type []string`
map值可以是任意类型, 没有限制; map里所有键的数据类型必须是相同的, 值也必须如此, 但键和值的数据类型可以不相同;
**ps**:
- map是无序的, 无法决定它的返回顺序, 所以, 每次打印结果的顺利有可能不同;
## 2. 创建,初始化map:
### 2.1 创建map:
```go
package main
import "fmt"

func main() {
	var m1 map[int]string  //只是声明一个map，没有初始化, 为空(nil)map
	fmt.Println(m1 == nil) //true
	//m1[1] = "Luffy" 		//nil的map不能使用err, panic: assignment to entry in nil map

	m2 := map[int]string{} //m2, m3的创建方法是等价的
	m3 := make(map[int]string)
	fmt.Println(m2, m3) //map[] map[]

	m4 := make(map[int]string, 10) //第2个参数指定容量
	fmt.Println(m4)                // map[]
}
```
创建m4的方法指定了map的初始创建容量; 与slice类似, 后期在使用过程中, map可以自动扩容; 只不过map更方便一些, 不用借助类似append的函数, 直接赋值即可; 如, `m1[17] = "Nami"`; 赋值过程中, key如果与已有map中key重复, 会将原有map中key对应的value覆盖;
但是, 对于map而言, 可以使用`len()`函数, 但不能使用`cap()`函数;

### 2.2 初始化map:
也可以直接指定初值, 要保证key不重复
```go
package main

import "fmt"

func main() {
	//1、定义同时初始化
	var m5 map[int]string = map[int]string{1: "Luffy", 2: "Sanji"}
	fmt.Println(m5) //map[1:Luffy 2:Sanji]
	//2、自动推导类型 :=
	m6 := map[int]string{1: "Luffy", 2: "Sanji"}
	fmt.Println(m6) // map[1:Luffy 2:Sanji]
}
```

## 3. 常用操作:
### 3.1 赋值:
```go
package main
import "fmt"

func main() {
	m1 := map[int]string{1: "Luffy", 2: "Sanji"}
	m1[1] = "Nami"  //修改
	m1[3] = "Zoro"  //追加， go底层会自动为map分配空间
	fmt.Println(m1) //map[1:Nami 2:Sanji 3:Zoro]

	m2 := make(map[int]string, 10) //创建map
	m2[0] = "aaa"
	m2[1] = "bbb"
	fmt.Println(m2)           //map[0:aaa 1:bbb]
	fmt.Println(m2[0], m2[1]) //aaa bbb
}
```

### 3.2 遍历:
Map的迭代顺序是不确定的, 并且不同的哈希函数实现可能导致不同的遍历顺序; 在实践中, 遍历的顺序是随机的, 每一次遍历的顺序都不相同. 这是故意的, 每次都使用随机的遍历顺序可以强制要求程序不会依赖具体的哈希函数实现:
```go
package main
import "fmt"

func main() {
	m3 := map[int]string{1: "Luffy", 2: "Sanji"}
	//遍历1，第一个返回值是key，第二个返回值是value
	for k, v := range m3 {
		fmt.Printf("%d ----> %s\n", k, v)
		//1 ----> Luffy
		//2 ----> yoyo
	}

	//遍历2，第一个返回值是key，第二个返回值是value（可省略）
	for k := range m3 {
		fmt.Printf("%d ----> %s\n", k, m3[k])
		//1 ----> Luffy
		//2 ----> Sanji
	}
}
```

### 3.3 获取值:
有时候可能需要知道对应的元素是否真的是在map之中; 可以使用下标语法判断某个key是否存在; map的下标语法将产生两个值, 其中第二个是一个布尔值, 用于报告元素是否真的存在; 
如果key存在, 第一个返回值返回value的值; 第二个返回值为 true;
```go
value, ok := m1[1]
fmt.Println("value = ", value, ", ok = ", ok) //value =  mike , ok =  true
```
如果key不存在, 第一个返回值为空, 第二个返回值为false;
```go
value2, has := m1[3]
fmt.Println("value2 = ", value2, ", has = ", has) //value2 =   , has =  false
```

### 3.4 删除:
使用`delete()`函数, 指定key值可以方便的删除map中的k-v映射;
```go
package main
import "fmt"

func main() {
    m4 := map[int]string{1: "Luffy", 2: "Sanji", 3: "Zoro"}
	for k, v := range m4 { //遍历，第一个返回值是key，第二个返回值是value
		fmt.Printf("%d ----> %s\n", k, v)
	}
	//1 ----> Sanji
	//2 ----> Sanji
	//3 ----> Zoro
	delete(m4, 2) //删除key值为2的map

	for k, v := range m4 {
		fmt.Printf("%d ----> %s\n", k, v)
	}
	//1 ----> Luffy
	//3 ----> Zoro
}
```
`delete()`操作是安全的, 即使元素不在map中也没有关系; 如果查找删除失败将返回value类型对应的零值

## 4. map做函数参数:
与slice 相似, 在函数间传递映射并不会制造出该映射的一个副本, 不是值传递, 而是**引用传递**
```go
func DeleteMap(m map[int]string, key int) {
    delete(m, key) //删除key值为2的map
    for k, v := range m {
        fmt.Printf("len(m)=%d, %d ----> %s\n", len(m), k, v)
}
    //len(m)=2, 1 ----> Luffy
    //len(m)=2, 3 ----> Zoro
}

func main() {
    m := map[int]string{1: "Luffy", 2: "Sanji", 3: "Zoro"}
    DeleteMap(m, 2) 	//删除key值为2的map

    for k, v := range m {
        fmt.Printf("len(m)=%d, %d ----> %s\n", len(m), k, v)
}
    //len(m)=2, 1 ----> Luffy
    //len(m)=2, 3 ----> Zoro
}
```
## 5. map做函数返回值
返回的依然是引用
```go
func test() map[int]string {
	// m1 := map[int]string{1: "Luffy", 2: "Sanji", 3: "Zoro"}
   m1 := make(map[int]string, 1)     // 创建一个初创容量为1的map
   m1[1] = "Luffy"
   m1[2] = "Sanji"                   // 自动扩容
   m1[67] = "Zoro"
   m1[2] = "Nami"                	// 覆盖 key值为2 的map
   fmt.Println("m1 = ", m1) // m1 =  map[1:Luffy 2:Nami 67:Zoro]
   return m1
}

func main() {
   m2 := test()                  	// 返回值 —— 传引用
   fmt.Println("m2 = ", m2) // m2 =  map[2:Nami 67:Zoro 1:Luffy]
}
```

# 四、struct -- 结构体:
## 1. 结构体类型:
有时需要将不同类型的数据组合成一个有机的整体, 如: 一个学生有学号/姓名/性别/年龄/地址等属性; 显然单独定义以上变量比较繁琐, 数据不便于管理
![](/images/golang/005_student_struct.png)
结构体是一种聚合的数据类型, 它是由一系列具有相同类型或不同类型的数据构成的数据集合; 每个数据称为结构体的成员;
## 2. 结构体初始化
### 2.1 普通变量:
```go
type Student struct {
    id   int
    name string
    sex  byte
    age  int
    addr string
}

func main() {
    //1、顺序初始化，必须每个成员都初始化
    var s1 Student = Student{1, "Luffy", 'm', 18, "EastSea"}
    s2 := Student{2, "Sanji", 'f', 20, "EastSea"}
    //s3 := Student{2, "Nami", 'm', 20} //err, too few values in struct initializer

    //2、指定初始化某个成员，没有初始化的成员为零值
    s4 := Student{id: 2, name: "Zoro"}
}
```
### 2.2 指针变量:

```go
type Student struct {
    id   int
    name string
    sex  byte
    age  int
    addr string
}

func main() {
    var s5 *Student = &Student{3, "Nami", 'm', 16, "EastSea"}
    s6 := &Student{4, "ro", 'm', 3, "NorthSea"}
}
```
## 3. 使用结构体成员
### 3.1 普通变量
```go
package main

import "fmt"

func main() {
	//===============结构体变量为普通变量
    //1、打印成员
    var s1 Student = Student{1, "Luffy", 'm', 18, "EastSea"}
    //结果：id = 1, name = Luffy, sex = m, age = 18, addr = EastSea
    fmt.Printf("id = %d, name = %s, sex = %c, age = %d, addr = %s\n", s1.id, s1.name, s1.sex, s1.age, s1.addr)

    //2、成员变量赋值
    var s2 Student
    s2.id = 2
    s2.name = "Sanji"
    s2.sex = 'f'
    s2.age = 16
    s2.addr = "EastSea"
    fmt.Println(s2) //{2 yoyo 102 16 EastSea}
}
```

### 3.2 指针变量
```go
package main

import "fmt"

func main() {
	//===============结构体变量为指针变量
    //3、先分配空间，再赋值
    s3 := new(Student)
    s3.id = 3
    s3.name = "Nami"
    fmt.Println(s3) //&{3 Nami 0 0 }

    //4、普通变量和指针变量类型打印
    var s4 Student = Student{4, "Sanji", 'm', 18, "EastSea"}
    fmt.Printf("s4 = %v, &s4 = %v\n", s4, &s4) //s4 = {4 Sanji 109 18 sz}, &s4 = &{4 Sanji 109 18 EastSea}

    var p *Student = &s4
    //p.成员 和(*p).成员 操作是等价的
    p.id = 5
    (*p).name = "ro"
    fmt.Println(p, *p, s4) //&{5 ro 109 18 EastSea} {5 ro 109 18 EastSea} {5 ro 109 18 EastSea}
}
```
在Go语言中, **普通结构体变量** 和 **结构体指针变量**访问成员的方法一致;不需要加以区分;

## 4. 结构体比较
如果结构体的全部成员都是可以比较的, 那么结构体也是可以比较的, 那样的话两个结构体将可以使用 `==` 或 `!=` 运算符进行比较, 但不支持 `>` 或 `<`;
```go
func main() {
    s1 := Student{1, "Luffy", 'm', 18, "EastSea"}
    s2 := Student{1, "Luffy", 'm', 18, "EastSea"}

    fmt.Println("s1 == s2", s1 == s2) //s1 == s2 true
    fmt.Println("s1 != s2", s1 != s2) //s1 != s2 false
}
```
## 5.作函数参数
### 5.1 传值
```go
func printValue(stu Student) {
    stu.id = 250
    //printValue stu =  {250 Luffy 109 18 s EastSea}
    fmt.Println("printValue stu = ", stu)
}

func main() {
var s Student = Student{1, "Luffy", 'm', 18, "EastSea"}

    printValue(s)        //值传递，形参修改不会影响到实参值
    fmt.Println("main s = ", s) //main s =  {1 Luffy 109 18 EastSea}
}
```
传参过程中, 实参会将自己的值拷贝一份给形参; 因此结构体“传值”操作几乎不会在实际开发中被使用到;
近乎100%的使用都采用“传址”的方式, 将结构体的引用传递给所需函数;

### 5.2 传引用
```go
func printPointer(p *Student) {
    p.id = 250
    //printPointer p =  &{250 Luffy 109 18 EastSea}
    fmt.Println("printPointer p = ", p)
}

func main() {
    var s Student = Student{1, "Luffy", 'm', 18, "EastSea"}

    printPointer(&s)      //传引用(地址)，形参修改会影响到实参值
    fmt.Println("main s = ", s) //main s = {250 Luffy 109 18 EastSea}
}
```