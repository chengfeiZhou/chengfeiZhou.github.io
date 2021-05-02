---
title: 三、Golang之Go并发编程
date: 2021-03-27 22:49:57
tags: 
	- Golang高级
    - 并发编程
categories: 
    - Golang高级
    - 并发编程
---
# 一、概述:
所谓并发编程是指在一台处理器上“同时”处理多个任务;
宏观的并发是指在一段时间内, 有多个程序在同时运行;
并发在微观上, 是指在同一时刻只能有一条指令执行, 但多个程序指令被快速的轮换执行, 使得在宏观上具有多个进程同时执行的效果, 但在微观上并不是同时执行的, 只是把时间分成若干段, 使多个程序快速交替的执行;
![](/images/golang/006_并发.png)
## 1. 并行与并发:
并行(parallel): 指在同一时刻, 有多条指令在**多个处理器**上同时执行;
![](/images/golang/007_并发.png)
并发(concurrency)L指在同一时刻只能有一条指令执行, 但多个进程指令被快速的轮换执行, 使得在宏观上具有多个进程同时执行的效果, 但在微观上并不是同时执行的, 只是把时间分成若干段, 通过**cpu时间片轮转**使多个进程快速交替的执行;
![](/images/golang/008_并行.png)

- 并行是两个队列同时使用两台咖啡机 (真正的多任务)
- 并发是两个队列交替使用一台咖啡机 (假 的多任务)

![](/images/golang/009_ex_coffee.png)

# 二、常见并发编程技术:
## 1.进程并发
### 1.1 程序和进程:
- 程序:
    - 编译成功的得到的二进制文件;
    - 占用: 磁盘
- 进程:
    - 运行起来的程序;
    - 占用系统资源;(内存, 锁, cpu, ...)
    - 一个程序可以起多个线程;
### 1.2 进程状态:
进程基本的状态有5种; 分别为**初始态, 就绪态, 运行态, 挂起态与终止态**; 其中初始态为进程准备阶段; 常与就绪态结合来看;
![](/images/golang/010_线程状态.png)
    
### 1.2 进程并发
在使用进程 实现并发时可能会出现的问题呢: 
- 系统开销比较大, 占用资源比较多，开启进程数量比较少;
- 在unix/linux系统下, 还会产生"孤儿进程"和"僵尸进程";

通过前面查看操作系统的进程信息, 在操作系统中, 可以产生很多的进程; 在unix/linux系统中, 正常情况下, 子进程是通过父进程`fork`创建的, 子进程再创建新的进程;
并且父进程永远无法预测子进程 到底什么时候结束; 当一个 进程完成它的工作终止之后, 它的父进程需要调用系统调用取得子进程的终止状态;

**孤儿进程** :
- 父进程先于子进程结束，则子进程成为孤儿进程，子进程的父进程成为init进程，称为init进程领养孤儿进程。
**僵尸进程** :
- 进程终止，父进程尚未回收，子进程残留资源（PCB）存放于内核中，变成僵尸（Zombie）进程。  
Windows下的进程和Linux下的进程是不一样的，它比较懒惰，从来不执行任何东西，只是为线程提供执行环境。然后由线程负责执行包含在进程的地址空间中的代码。当创建一个进程的时候，操作系统会自动创建这个进程的第一个线程，成为主线程

## 2. 线程并发:
### 2.1 什么是线程:
LWP: light weight process 轻量级的进程, 本质仍是进程 (Linux下)
进程: 独立地址空间，拥有PCB 
线程: 有独立的PCB，但没有独立的地址空间(共享)
区别: 
    - 在于是否共享地址空间;
    - 线程: 最小的执行单位;
    - 进程: 最小分配资源单位, 可看成是只有一个线程的进程; 
Windows系统下, 可以直接忽略进程的概念, 只谈线程; 因为线程是最小的执行单位, 是被系统独立调度和分派的基本单位; 而进程只是给线程提供执行环境;
![](/images/golang/011_线程.png)

### 2.2 线程同步:
同步即协同步调, 按预定的先后次序运行; 
线程同步: 指一个线程发出某一功能调用时, 在没有得到结果之前, 该调用不返回; 同时其它线程为保证数据一致性, 不能调用该功能;

举例: 内存中100字节, 线程T1欲填入全1, 线程T2欲填入全0; 但如果T1执行了50个字节失去cpu, T2执行, 会将T1写过的内容覆盖; 当T1再次获得cpu继续从失去cpu的位置向后写入1, 当执行结束, 内存中的100字节, 既不是全1, 也不是全0;
产生的现象叫做**与时间有关的错误(time related)**; 为了避免这种数据混乱,线程需要同步;
“同步”的目的, 是为了避免数据混乱, 解决与时间有关的错误; 实际上, 不仅线程间需要同步, 进程间、信号间等等都需要同步机制;
因此, 所有“多个控制流, 共同操作一个共享资源”的情况, 都需要同步;

## 3. 锁的应用:
### 3.1 互斥量 mutex:
Linux中提供一把互斥锁mutex(也称之为互斥量);
每个线程在对资源操作前都尝试先加锁, 成功加锁才能操作, 操作结束解锁;
资源还是共享的, 线程间也还是竞争的, 但通过“锁”就将资源的访问变成互斥操作, 而后与时间有关的错误也不会再产生了;
![](/images/golang/012_mutex.png)

应注意: 同一时刻, 只能有一个线程持有该锁;
当A线程对某个全局变量加锁访问, B在访问前尝试加锁, 拿不到锁, B阻塞; C线程不去加锁, 而直接访问该全局变量, 依然能够访问, 但会出现数据混乱;
所以, 互斥锁实质上是操作系统提供的一把“建议锁”(又称“协同锁”), 建议程序中有多线程访问共享资源的时候使用该机制; 但, 并没有强制限定;
因此, 即使有了mutex, 如果有线程不按规则来访问数据, 依然会造成数据混乱;

### 3.2 读写锁
与互斥量类似, 但读写锁允许更高的并行性; 其特性为: 写独占, 读共享:

- 读写锁状态: 读写锁只有一把, 其具备两种状态:
    - 读模式下加锁状态 (读锁); 
    - 写模式下加锁状态 (写锁);

- 读写锁特性: 
    - 读写锁是“写模式加锁”时, 解锁前, 所有对该锁加锁的线程都会被阻塞; 
    - 读写锁是“读模式加锁”时, 如果线程以读模式对其加锁会成功; 如果线程以写模式加锁会阻塞; 
    - 读写锁是“读模式加锁”时, 既有试图以写模式加锁的线程, 也有试图以读模式加锁的线程; 那么读写锁会阻塞随后的读模式锁请求; 优先满足写模式锁; **读锁、写锁并行阻塞, 写锁优先级高**;

读写锁也叫共享-独占锁; 当读写锁以读模式锁住时, 它是以共享模式锁住的; 当它以写模式锁住时, 它是以独占模式锁住的; 写独占、读共享;
读写锁非常适合于对数据结构读的次数远大于写的情况;


## 4.协程并发:
### 4.1 什么是协程:
协程: coroutine, 也叫轻量级线程; 
与传统的系统级线程和进程相比, 协程最大的优势在于“轻量级”; 可以轻松创建上万个而不会导致系统资源衰竭; 而线程和进程通常很难超过1万个; 这也是协程别称“轻量级线程”的原因; 
一个线程中可以有任意多个协程, 但某一时刻只能有一个协程在运行, **多个协程分享该线程分配到的计算机资源**;
多数语言在语法层面并不直接支持协程,, 而是通过库的方式支持, 但用库的方式支持的功能也并不完整, 比如仅仅提供协程的创建、销毁与切换等能力; 如果在这样的轻量级线程中调用一个同步 IO 操作, 比如网络通信、本地文件读写, 都会阻塞其他的并发执行轻量级线程, 从而无法真正达到轻量级线程本身期望达到的目标;
在协程中, 调用一个任务就像调用一个函数一样, 消耗的系统资源最少! 但能达到进程、线程并发相同的效果; 
在一次并发任务中, 进程、线程、协程均可以实现; 从系统资源消耗的角度出发来看, 进程相当多, 线程次之, 协程最少;

### 4.2 Go并发
Go 在语言级别支持协程, 叫`goroutine`; Go 语言标准库提供的所有系统调用操作(包括所有同步IO操作), 都会出让CPU给其他`goroutine`; 这让轻量级线程的切换管理不依赖于系统的线程和进程, 也不需要依赖于CPU的核心数量;
有人把Go比作21世纪的C语言; 第一是因为Go语言设计简单, 第二21世纪最重要的就是并行程序设计, 而Go从语言层面就支持并行; 同时, 并发程序的内存管理有时候是非常复杂的, 而Go语言提供了自动垃圾回收机制;
Go语言为并发编程而内置的上层API基于**顺序通信进程模型CSP(communicating sequential processes)**; 这就意味着显式锁都是可以避免的, 因为Go通过相对安全的通道发送和接受数据以实现同步, 这大大地简化了并发程序的编写;
Go语言中的并发程序主要使用两种手段来实现; `goroutine`和`channel`;
## 5. Goroutine
### 5.1 什么是Goroutine
`goroutine`是Go并行设计的核心; `goroutine`说到底其实就是协程, 它比线程更小, 十几个goroutine可能体现在底层就是五六个线程, Go语言内部实现了这些goroutine之间的内存共享; 执行goroutine只需极少的栈内存(大概是4~5KB), 当然会根据相应的数据伸缩; 也正因为如此, 可同时运行成千上万个并发任务; goroutine比thread更易用、更高效、更轻便; 
一般情况下, 一个普通计算机跑几十个线程就有点负载过大了, 但是同样的机器却可以轻松地让成百上千个goroutine进行资源竞争;

### 5.2 Goroutine的创建
只需在函数调⽤语句前添加 `go` 关键字, 就可创建并发执⾏单元; 开发⼈员无需了解任何执⾏细节, 调度器会自动将其安排到合适的系统线程上执行; 
在并发编程中, 通常想将一个过程切分成几块, 然后让每个goroutine各自负责一块工作, 当一个程序启动时, 主函数在一个单独的goroutine中运行, 叫`main goroutine`; 新的goroutine会用go语句来创建; 而go语言的并发设计, 很轻松就可以达成这一目的;
```go
package main

import (
    "fmt"
    "time"
)

func newTask() {
    i := 0
    for {
        i++
        fmt.Printf("new goroutine: i = %d\n", i)
        time.Sleep(1 * time.Second) //延时1s
    }
}

func main() {
    //创建一个 goroutine，启动另外一个任务
    go newTask()
    i := 0
    //main goroutine 循环打印
    for {
        i++
        fmt.Printf("main goroutine: i = %d\n", i)
        time.Sleep(1 * time.Second) //延时1s
    }
}
```
![](/images/golang/013_goroutine.png)

### 5.3 Goroutine特性:
主goroutine退出后，其它的工作goroutine也会自动退出;

## 6. runtime包:
### 6.1 Gosched
`runtime.Gosched()` 用于让出CPU时间片, 让出当前goroutine的执行权限, 调度器安排其他等待的任务运行, 并在下次再获得cpu时间轮片的时候, 从该出让cpu的位置恢复执行;
```go
package main

import (
"fmt"
"runtime"
)

func main() {
    //创建一个goroutine
    go func(s string) {
        for i := 0; i < 2; i++ {
            fmt.Println(s)
        }
    }("world")

    for i := 0; i < 2; i++ {
        runtime.Gosched()  //import "runtime" 包
        /*
            屏蔽runtime.Gosched()运行结果如下：
                hello
                hello

            有runtime.Gosched()运行结果如下：
                world
                world
                hello
                hello
        */
        fmt.Println("hello")
    }
}
```
主协程进入`main()`函数, 进行代码的执行; 当执行到`go func()`匿名函数时, 创建一个新的协程, 开始执行匿名函数中的代码, 主协程继续向下执行, 执行到`runtime.Gosched()`时会暂停向下执行, 直到其它协程执行完后, 再回到该位置, 主协程继续向下执行;

### 6.2 Goexit 
调用 `runtime.Goexit()` 将立即终止当前 `goroutine` 执⾏, 调度器确保所有已注册 `defer`延迟调用被执行;
```go
package main

import (
"fmt"
"runtime"
)

func main() {
    go func() {
        defer fmt.Println("A.defer")

        func() {
            defer fmt.Println("B.defer")
            runtime.Goexit() // 终止当前 goroutine, import "runtime"
            fmt.Println("B") // 不会执行
        }()

        fmt.Println("A") // 不会执行
    }() 	//不要忘记()

    //死循环，目的不让主goroutine结束
    for {
    }
}
```
结果:
```
B.defer
A.defer
```

### 6.3 GOMAXPROCS
调用 `runtime.GOMAXPROCS()` 用来设置可以并行计算的CPU核数的最大值, 并返回之前的值
```go
package main

import (
    "fmt"
)

func main() {
//n := runtime.GOMAXPROCS(1) 	// 第一次 测试
//打印结果：111111111111111111110000000000000000000011111...

n := runtime.GOMAXPROCS(2)         // 第二次 测试
//打印结果：010101010101010101011001100101011010010100110...
    fmt.Printf("n = %d\n", n)

    for {
        go fmt.Print(0)
        fmt.Print(1)
    }
}
```
在第一次执行`runtime.GOMAXPROCS(1)` 时, 最多同时只能有一个goroutine被执行, 所以会打印很多1; 过了一段时间后, GO调度器会将其置为休眠, 并唤醒另一个goroutine, 这时候就开始打印很多0了, 在打印的时候, goroutine是被调度到操作系统线程上的;
在第二次执行`runtime.GOMAXPROCS(2)` 时, 使用了两个CPU, 所以两个goroutine可以一起被执行, 以同样的频率交替打印0和1;

### 6.4 其他方法:
https://studygolang.com/pkgdoc


# 三、协程间通信与 Channel
## 1. channel同步数据通信:
### 1.1 channel 管道:
补充知识:
每当有一个进程启动时, 系统会自动打开三个文件: **标准输入、标准输出、标准错误**; —— 对应三个文件: **stdin(代号: 0)、stdout(代号: 1)、stderr(代号: 2)**;
当进行运行结束, 操作系统自动关闭三个文件(隐式回收系统资源),
### 1.2 什么是channel:
channel是Go语言中的一个**核心类型**, 可以把它看成管道; 并发核心单元通过它就可以发送或者接收数据进行通讯, 这在一定程度上又进一步降低了编程的难度; 
channel是一个数据类型, 主要用来解决协程的同步问题以及协程之间数据共享(数据传递)的问题;
goroutine运行在相同的地址空间, 因此访问共享内存必须做好同步; **goroutine 奉行通过通信来共享内存, 而不是共享内存来通信**;
引⽤类型 channel可用于多个 goroutine 通讯; 其内部实现了**同步, 确保并发安全**;
![](/images/golang/014_channel通讯.png)

### 1.3 定义channel变量:
和map类似, channel也一个对应`make`创建的底层数据结构的引用;
当复制一个channel或用于函数参数传递时, 只是拷贝了一个channel引用, 因此调用者和被调用者将引用同一个channel对象; 和其它的引用类型一样, channel的零值也是nil; 
定义一个channel时, 也需要定义发送到channel的值的类型; channel可以使用内置的`make()`函数来创建:
    - `chan`是创建channel所需使用的关键字;
    - `Type` 代表指定channel收发数据的类型;

```go
make(chan Type)  // 等价于make(chan Type, 0) => 无缓冲,只能容纳一个变量
make(chan Type, capacity) // => 有缓冲通道
```
当 `capacity = 0` 时, channel 是无缓冲阻塞读写的;
当 `capacity > 0` 时, channel 有缓冲、是非阻塞的, 直到写满 capacity个元素才阻塞写入;

channel一边可以存放东西, 另一边可以取出东西; channel通过操作符 `<-` 来接收和发送数据;
发送和接收数据语法:
```go
channel <- value      //发送value到channel

<-channel             //接收并将其丢弃
x := <-channel        //从channel中接收数据，并赋值给x
x, ok := <-channel    //功能同上，同时检查通道是否已关闭或者是否为空
```
默认情况下, channel接收和发送数据都是阻塞的, 除非另一端已经准备好, 这样就使得goroutine同步变的更加的简单, 而不需要显式的lock;
```go
package main

import (
    "fmt"
)

func main() {
    c := make(chan int)

    go func() {
        defer fmt.Println("子协程结束")

        fmt.Println("子协程正在运行……")

        c <- 666 //666发送到c
    }()

    num := <-c //从c中接收数据，并赋值给num

    fmt.Println("num = ", num)
    fmt.Println("main协程结束")
}
```
结果:
```
子协程正在运行......
子协程结束
num = 666
main协程结束
```

## 2. 无缓冲的channel:
无缓冲的通道(unbuffered channel)是指在接收前没有能力保存任何值的通道;
这种类型的通道要求发送goroutine和接收goroutine同时准备好, 才能完成发送和接收操作; 否则, 通道会导致先执行发送或接收操作的 goroutine 阻塞等待;
这种对通道进行发送和接收的交互行为本身就是同步的; 其中任意一个操作都无法离开另一个操作单独存在; 
- 阻塞：由于某种原因数据没有到达，当前协程（线程）持续处于等待状态，直到条件满足，才接触阻塞; 
- 同步：在两个或多个协程(线程)间, 保持数据内容一致性的机制; 

无缓冲的channel创建格式:
```go
make(chan Type)   //等价于make(chan Type, 0)
```
如果没有指定缓冲区容量, 那么该通道就是同步的, 因此会阻塞到发送者准备好发送和接收者准备好接收;

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    c := make(chan int, 0) //创建无缓冲的通道 c 

    //内置函数 len 返回未被读取的缓冲元素数量，cap 返回缓冲区大小
    fmt.Printf("len(c)=%d, cap(c)=%d\n", len(c), cap(c))

    go func() {
        defer fmt.Println("子协程结束")

        for i := 0; i < 3; i++ {
            c <- i
            fmt.Printf("子协程正在运行[%d]: len(c)=%d, cap(c)=%d\n", i, len(c), cap(c))
        }
    }()

    time.Sleep(2 * time.Second) //延时2s

    for i := 0; i < 3; i++ {
        num := <-c //从c中接收数据，并赋值给num
        fmt.Println("num = ", num)
    }

    fmt.Println("main协程结束")
}
```
结果:
```
lan(c)=0, cap(c)=0
子协程正在运行[0]: len(c)=0, cap(c)=0
num = 0
num = 1
子协程正在运行[1]: len(c)=0, cap(c)=0
子协程正在运行[2]: len(c)=0, cap(c)=0
子协程结束
num = 2
main协程结束
```

## 3. 有缓冲的channel:
有缓冲的通道(buffered channel)是一种在被接收前能存储一个或者多个数据值的通道; 
这种类型的通道并不强制要求 `goroutine` 之间必须同时完成发送和接收; 通道会阻塞发送和接收动作的条件也不同;
只有通道中没有要接收的值时, 接收动作才会阻塞;
只有通道没有可用缓冲区容纳被发送的值时, 发送动作才会阻塞;
这导致有缓冲的通道和无缓冲的通道之间的一个很大的不同:
- 无缓冲的通道保证进行发送和接收的 goroutine 会在同一时间进行数据交换;
- 有缓冲的通道没有这种保证;

有缓冲的channel创建格式：
```go
make(chan Type, capacity)
```
如果给定了一个缓冲区容量, 通道就是**异步的**; 只要缓冲区有未使用空间用于发送数据, 或还包含可以接收的数据, 那么其通信就会无阻塞地进行;
```go
func main() {
    c := make(chan int, 3) //带缓冲的通道

    //内置函数 len 返回未被读取的缓冲元素数量， cap 返回缓冲区大小
    fmt.Printf("len(c)=%d, cap(c)=%d\n", len(c), cap(c))

    go func() {
        defer fmt.Println("子协程结束")

        for i := 0; i < 3; i++ {
            c <- i
            fmt.Printf("子协程正在运行[%d]: len(c)=%d, cap(c)=%d\n", i, len(c), cap(c))
        }
    }()

    time.Sleep(2 * time.Second) //延时2s
    for i := 0; i < 3; i++ {
        num := <-c //从c中接收数据，并赋值给num
        fmt.Println("num = ", num)
    }
    fmt.Println("main协程结束")
}
```
结果:
```
lan(c)=0, cap(c)=3
子协程正在运行[0]: len(c)=0, cap(c)=3
子协程正在运行[1]: len(c)=1, cap(c)=3
子协程正在运行[2]: len(c)=2, cap(c)=3
子协程结束
num = 0
num = 1
num = 2
main协程结束
```

## 4. 关闭channel
如果发送者知道, 没有更多的值需要发送到channel的话, 那么让接收者也能及时知道没有多余的值可接收将是有用的, 因为接收者可以停止不必要的接收等待; 这可以通过内置的`close()`函数来关闭channel实现;
```go

package main

import (
    "fmt"
)

func main() {
    c := make(chan int)

    go func() {
        for i := 0; i < 5; i++ {
            c <- i
        }
        //把 close(c) 注释掉，程序会一直阻塞在 if data, ok := <-c; ok 那一行
        close(c)
    }()

    for {
        //ok为true说明channel没有关闭，为false说明管道已经关闭
        if data, ok := <-c; ok {
            fmt.Println(data)
        } else {
            break
        }
    }

    fmt.Println("Finished")
}
```
结果:
```
0
1
2
3
4
Finished
```
ps:
- channel不像文件一样需要经常去关闭, 只有当你确实没有任何发送数据了, 或者你想显式的结束range循环之类的, 才去关闭channel; 
- 关闭channel后, 无法向channel 再发送数据(引发 panic 错误后导致接收立即返回零值);
- 关闭channel后, 可以继续从channel接收数据;
- 对于nil channel, 无论收发都会被阻塞;


可以使用 `range` 来迭代不断操作channel
```go
package main

import (
    "fmt"
)

func main() {
    c := make(chan int)

    go func() {
        for i := 0; i < 5; i++ {
            c <- i
        }
        //把 close(c) 注释掉，程序会一直阻塞在 for data := range c 那一行
        close(c)
    }()

    for data := range c {
        fmt.Println(data)
    }
    fmt.Println("Finished")
}
```

## 5. 单向channel及应用:
默认情况下, 通道channel是双向的, 也就是, 既可以往里面发送数据也可以同里面接收数据;
但是, 经常见一个通道作为参数进行传递而值希望对方是单向使用的, 要么只让它发送数据, 要么只让它接收数据, 这时候可以指定通道的方向;
![](/images/golang/015_channel.png)

单向channel变量的声明:
```go
var ch1 chan int       // ch1是一个正常的channel，是双向的
var ch2 chan<- float64 // ch2是单向channel，只用于写float64数据
var ch3 <-chan int     // ch3是单向channel，只用于读int数据
```
- `chan<-` 表示数据进入管道, 要把数据写进管道, 对于调用者就是输出;
- `<-chan` 表示数据从管道出来, 对于调用者就是得到管道的数据就是输入;


可以将 **channel 隐式转换为单向队列**, 只收或只发; **不能**将**单向 channel 转换为普通 channel**

```go
c := make(chan int, 3)
var send chan<- int = c // send-only
var recv <-chan int = c // receive-only
send <- 1
//<-send //invalid operation: <-send (receive from send-only type chan<- int)
<-recv
//recv <- 2 //invalid operation: recv <- 2 (send to receive-only type <-chan int)

//不能将单向 channel 转换为普通 channel
d1 := (chan int)(send) //cannot convert send (type chan<- int) to type chan int
d2 := (chan int)(recv) //cannot convert recv (type <-chan int) to type chan int
```

```go
//   chan<- //只写
func counter(out chan<- int) {
    defer close(out)
    for i := 0; i < 5; i++ {
        out <- i //如果对方不读 会阻塞
    }
}

//   <-chan //只读
func printer(in <-chan int) {
    for num := range in {
        fmt.Println(num)
    }
}

func main() {
    c := make(chan int) //   chan   //读写

    go counter(c) //生产者
    printer(c)    //消费者

    fmt.Println("done")
}
```
## 6. 生产者消费者模型:
单向channel最典型的应用是“生产者消费者模型”
所谓**生产者消费者模型**: 某个模块(函数等)负责产生数据, 这些数据由另一个模块来负责处理(此处的模块是广义的, 可以是类、函数、协程、线程、进程等); 产生数据的模块, 就形象地称为生产者; 而处理数据的模块, 就称为消费者;
单单抽象出生产者和消费者, 还够不上是生产者／消费者模型; 该模式还需要有一个缓冲区处于生产者和消费者之间, 作为一个中介; 生产者把数据放入**缓冲区**, 而消费者从缓冲区取出数据; 大概的结构如下图:
![](/images/golang/016_生产者消费者.png)

缓冲区的好处大概如下:
- 解耦:
    - 假设生产者和消费者分别是两个类; 如果让生产者直接调用消费者的某个方法, 那么生产者对于消费者就会产生依赖(也就是耦合); 将来如果消费者的代码发生变化, 可能会直接影响到生产者; 而如果两者都依赖于某个缓冲区, 两者之间不直接依赖, 耦合度也就相应降低了;
- 处理并发:
    - 生产者直接调用消费者的某个方法, 还有另一个弊端; 由于函数调用是同步的(或者叫阻塞的), 在消费者的方法没有返回之前, 生产者只好一直等在那边; 万一消费者处理数据很慢, 生产者只能无端浪费时间;
    - 使用了生产者／消费者模式之后, 生产者和消费者可以是两个独立的并发主体; 生产者把制造出来的数据往缓冲区一丢, 就可以再去生产下一个数据; 基本上不用依赖消费者的处理速度;
    - 其实最当初这个生产者消费者模式, 主要就是用来处理并发问题的;
- 缓存:
    - 如果生产者制造数据的速度时快时慢, 缓冲区的好处就体现出来了; 当数据制造快的时候, 消费者来不及处理, 未处理的数据可以暂时存在缓冲区中; 等生产者的制造速度慢下来, 消费者再慢慢处理掉;


例子:
```go
package main

import "fmt"

// 此通道只能写，不能读。
func producer(out chan<- int)  {
   for i:= 0; i < 10; i++ {
      out <- i*i          		// 将 i*i 结果写入到只写channel
   }
   close(out)
}

// 此通道只能读，不能写
func consumer(in <-chan int)  {
   for num := range in {		// 从只读channel中获取数据
      fmt.Println("num =", num)
   }
}

func main()  {
   ch := make(chan int)		// 创建一个双向channel

   // 新建一个groutine， 模拟生产者，产生数据，写入 channel
   go producer(ch)     		// channel传参， 传递的是引用。

   // 主协程，模拟消费者，从channel读数据，打印到屏幕
   consumer(ch)      		// 与 producer 传递的是同一个 channel
}
```
> 首先创建一个双向的channel, 然后开启一个新的goroutine, 把双向通道作为参数传递到producer方法中, 同时转成只写通道; 
> 子协程开始执行循环, 向只写通道中添加数据, 这就是生产者; 
> 主协程, 直接调用consumer方法, 该方法将双向通道转成只读通道, 通过循环每次从通道中读取数据, 这就是消费者;


ps: channel作为参数传递，是引用传递

### 6.2 模拟订单:
在实际的开发中, 生产者消费者模式应用也非常的广泛, 例如: 在电商网站中, 订单处理, 就是非常典型的生产者消费者模式;
当很多用户单击下订单按钮后, 订单生产的数据全部放到缓冲区(队列)中, 然后消费者将队列中的数据取出来发送者仓库管理等系统;
通过生产者消费者模式, 将订单系统与仓库管理系统隔离开, 且用户可以随时下单(生产数据); 如果订单系统直接调用仓库系统, 那么用户单击下订单按钮后, 要等到仓库系统的结果返回, 这样速度会很慢;
```go
package main

import "fmt"

type OrderInfo struct {     // 创建结构体类型OrderInfo，只有一个id 成员
   id int
}

func producer2(out chan <- OrderInfo)  {   	// 生成订单——生产者

   for i:=0; i<10; i++ {              	// 循环生成10份订单
      order := OrderInfo{id: i+1}
      out <- order                  		// 写入channel
   }
   close(out)				// 写完，关闭channel
}

func consumer2(in <- chan OrderInfo)  {       // 处理订单——消费者

   for order := range in {                   	// 从channel 取出订单
      fmt.Println("订单id为：", order.id)    // 模拟处理订单
   }
}

func main()  {
   ch := make(chan OrderInfo)  // 定义一个双向 channel， 指定数据类型为OrderInfo
   go producer2(ch)            // 建新协程，传只写channel
   consumer2(ch)               // 主协程，传只读channel
}
```

## 7. 定时器:
### 7.1 单次定时器: `time.Timer`
Timer是一个定时器; 代表未来的一个单一事件, 可以告诉timer你要等待多长时间;
```go
type Timer struct {
   C <-chan Time
   r runtimeTimer
}
```
它提供一个channel, 在定时时间到达之前, 没有数据写入`timer.C`会一直阻塞; 直到定时时间到, 向channel写入值, 阻塞解除, 可以从中读取数据;
```go
package main

import (
	"fmt"
	"time"
)

func main() {
    //创建定时器，2秒后，定时器就会向自己的C字节发送一个time.Time类型的元素值
    timer1 := time.NewTimer(time.Second * 2)
    t1 := time.Now() //当前时间
    fmt.Printf("t1: %v\n", t1)

    t2 := <-timer1.C
    fmt.Printf("t2: %v\n", t2)

    //如果只是想单纯的等待的话，可以使用 time.Sleep 来实现
    timer2 := time.NewTimer(time.Second * 2)
    <-timer2.C
    fmt.Println("2s后")

    time.Sleep(time.Second * 2)
    fmt.Println("再一次2s后")

    <-time.After(time.Second * 2)
    fmt.Println("再再一次2s后")

    timer3 := time.NewTimer(time.Second)
    go func() {
        <-timer3.C
        fmt.Println("Timer 3 expired")
    }()

    stop := timer3.Stop() //停止定时器
    if stop {
        fmt.Println("Timer 3 stopped")
    }

    fmt.Println("before")
    timer4 := time.NewTimer(time.Second * 5) 	//原来设置3s
    timer4.Reset(time.Second * 1)            	//重新设置时间
    <-timer4.C
    fmt.Println("after")
}
```
### 7.2 定时器的常用操作 -- 实现延迟功能:
- `time.After`:
```go
<-time.After(2 * time.Second) //定时2s，阻塞2s,2s后产生一个事件，往channel写内容
fmt.Println("时间到")
```
- `time.Sleep`:
```go
time.Sleep(2 * time.Second)
fmt.Println("时间到")
```
- `time.NewTimer`:
延时2s后打印一句话
```go
timer := time.NewTimer(2 * time.Second)
<- timer.C
fmt.Println("时间到")
```

### 7.3 定时器的常用操作 -- 定时器停止:
```go
timer := time.NewTimer(3 * time.Second)
go func() {
    <-timer.C
    fmt.Println("子协程可以打印了，因为定时器的时间到")
}()
timer.Stop() //停止定时器

for {
    ;
}
```

### 7.4 循环定时器 `time.Ticker`:
Ticker是一个周期触发定时的计时器, 它会按照一个时间间隔往channel发送系统当前时间, 而channel的接收者可以以固定的时间间隔从channel中读取事件;
```go
type Ticker struct {
   C <-chan Time 	// The channel on which the ticks are delivered.
   r runtimeTimer
}
```
```go
package main

import (
	"fmt"
	"time"
)

func main() {
    //创建定时器，每隔1秒后，定时器就会给channel发送一个事件(当前时间)
    ticker := time.NewTicker(time.Second * 1)

    i := 0
    go func() {
        for { //循环
            <-ticker.C
            i++
            fmt.Println("i = ", i)

            if i == 5 {
                ticker.Stop() //停止定时器
            }
        }
    }() //别忘了()

    //死循环，特地不让main goroutine结束
    for {
    }
}
```

## 8. select:
### 8.1 select作用
Go里面提供了一个关键字`select`, 通过select可以监听channel上的数据流动;
select的用法与switch语言非常类似, 由select开始一个新的选择块, 每个选择条件由case语句来描述;
与switch语句相比, select有比较多的限制, 其中最大的一条限制就是每个case语句里必须是一个IO操作,
大致的结构如下:
```go
select {
    case <-chan1:
        // 如果chan1成功读到数据，则进行该case处理语句
    case chan2 <- 1:
        // 如果成功向chan2写入数据，则进行该case处理语句
    default:
        // 如果上面都没有成功，则进入default处理流程
}
```
用来监听 channel 上的数据流动方向;  读 or 写

ps:
- 监听的case中, 没有满足监听条件, 阻塞;
- 监听的case中, 有多个满足监听条件, 任选一个执行;
- 可以使用default来处理所有case都不满足监听条件的状况;  通常不用(会产生忙轮询)
- select 自身不带有循环机制, 需借助外层 for 来循环监听;
- break 跳出 select中的一个case选项; 类似于switch中的用法; 

斐波那契额数列:
```go
package main

import (
    "fmt"
)

func fibonacci(c, quit chan int) {
    x, y := 1, 1
    for {
        select {
        case c <- x:
            x, y = y, x+y
        case <-quit:
            fmt.Println("quit")
            return
        }
    }
}

func main() {
    c := make(chan int)
    quit := make(chan int)

    go func() {
        for i := 0; i < 6; i++ {
            fmt.Println(<-c)
        }
        quit <- 0
    }()

    fibonacci(c, quit)
}
```
结果:
```
1
1
2
3
5
8
quit
```
### 8.2 超时
有时候会出现goroutine阻塞的情况, 那么如何避免整个程序进入阻塞的情况呢? 可以利用select来设置超时,
通过如下的方式实现:
```go
package main
import (
	"fmt"
	"time"
)

func main() {
	c := make(chan int)
	o := make(chan bool)
	go func() {
		for {
			select {
			case v := <-c:
				fmt.Println(v)
			case <-time.After(5 * time.Second):
				fmt.Println("timeout")
				o <- true
				return
			}
		}
	}()
	// c <- 666 // 注释掉，引发 timeout
	<-o
}
```

# 四、(扩展)锁和条件变量:
## 1. 死锁
死锁是指两个或两个以上的进程在执行过程中, 由于竞争资源或者由于彼此通信而造成的一种阻塞的现象, 若无外力作用, 它们都将无法推进下去; 此时称系统处于死锁状态或系统产生了死锁;
不是锁的一种!!!是一种错误使用锁导致的现象:
- 单go程自己死锁
    - `channel` 应该在 至少 2 个以上的 go程中进行通信;, 否则死锁;
- go程间`channel`访问顺序导致死锁
    - 使用`channel`一端读(写) 要保证另一端写(读)操作, 同时有机会执行;否则死锁;
- 多go程, 多channel 交叉死锁
    - A go程, 掌握M的同时, 尝试拿N;  B go程, 掌握N的同时尝试拿M;
- 在go语言中, 尽量不要将 `互斥锁、读写锁` 与 `channel` 混用  ——  隐性死锁;

```go
package main

// 死锁1
func main()  {
	ch :=make(chan int)
	ch <- 789
	num := <-ch
	fmt.Println("num = ", num)
}

// 死锁2
func main()  {
	ch := make(chan int)
	go func() {
		ch <- 789
	}()
	num := <- ch
	fmt.Println("num = ", num)
}

// 死锁 3
func main()  {
	ch1 := make(chan int)
	ch2 := make(chan int)
	go func() {				// 子
		for {
			select {
			case num := <-ch1:
				ch2 <- num
			}
		}
	}()
	for {
		select {
			case num := <- ch2:
				ch1 <- num
		}
	}
}
```
## 2. 互斥锁:
每个资源都对应于一个可称为 "互斥锁" 的标记, 这个标记用来保证在任意时刻, 只能有一个协程(线程)访问该资源;其它的协程只能等待;
互斥锁是传统并发编程对共享资源进行访问控制的主要手段, 它由标准库`sync`中的`Mutex`结构体类型表示; `sync.Mutex`类型只有两个公开的指针方法，`Lock`和`Unlock`; `Lock`锁定当前的共享资源; `Unlock`进行解锁;

在使用互斥锁时, 一定要注意: **对资源操作完成后, 一定要解锁, 否则会出现流程执行异常, 死锁等问题; 通常借助`defer`, 锁定后, 立即使用`defer`语句保证互斥锁及时解锁** ;如下所示:
```go
var mutex sync.Mutex // 定义互斥锁变量 mutex

func write(){
   mutex.Lock( )
   defer mutex.Unlock( )
}
```
- 使用channel同步
```go
package main
import (
	"fmt"
	"time"
)
var ch = make(chan int)

func printer(s string) {
	for _, c := range s{
		fmt.Printf("%c", c)
		time.Sleep(time.Millisecond * 300)
	}
}

func person1()  {				// 先
	printer("hello")
	ch <- 1
}

func person2()  {				// 后
	<- ch
	printer("world")
}

func main() {
	go person1()
	go person2()
	for {
		;
	}
}
```
- 使用 “锁” 完成同步 —— 互斥锁:
```go
package main

import (
	"fmt"
	"time"
	"sync"
)

var mutex sync.Mutex

func printer(s string) {
	mutex.Lock()
	for _, c := range s{
		fmt.Printf("%c", c)
		time.Sleep(time.Millisecond * 300)
	}
	mutex.Unlock()
}

func person1()  {				// 先
	printer("hello")
}

func person2()  {				// 后
	printer("world")
}

func main() {
	go person1()
	go person2()
	for {
		;
	}
}
```

## 3. 读写锁:
互斥锁的本质是当一个`goroutine`访问的时候, 其他`goroutine`都不能访问; 这样在资源同步, 避免竞争的同时也降低了程序的并发性能; 程序由原来的并行执行变成了串行执行;
其实, 当对一个不会变化的数据只做“读”操作的话, 是不存在资源竞争的问题的; 因为数据是不变的, 不管怎么读取, 多少goroutine同时读取, 都是可以的; 
所以问题不是出在“读”上, 主要是修改, 也就是“写”; 修改的数据要同步, 这样其他`goroutine`才可以感知到; 所以真正的互斥应该是读取和修改、修改和修改之间, 读和读是没有互斥操作的必要的;
因此, 衍生出另外一种锁, 叫做**读写锁**
读写锁可以让多个读操作并发, 同时读取, 但是对于写操作是完全互斥的; 也就是说, 当一个`goroutine`进行写操作的时候, 其他`goroutine`既不能进行读操作, 也不能进行写操作;
GO中的读写锁由结构体类型`sync.RWMutex`表示, 此类型的方法集合中包含两对方法:
- 一组是对写操作的锁定和解锁，简称“写锁定”和“写解锁”:
```go
func (*RWMutex)Lock()
func (*RWMutex)Unlock()
```
- 另一组表示对读操作的锁定和解锁，简称为“读锁定”与“读解锁”:
```go
func (*RWMutex)RLock()
func (*RWMutex)RUlock()
```

### 读写锁与channel同时使用引起的"隐性死锁":
```go
package main

import (
	"math/rand"
	"time"
	"fmt"
	"sync"
)

var rwMutex sync.RWMutex		// 锁只有一把， 2 个属性 r w

func readGo(in <-chan int, idx int)  {
	for {
		rwMutex.RLock()			// 以读模式加锁
		num := <-in
		fmt.Printf("----%dth 读 go程，读出：%d\n", idx, num)
		rwMutex.RUnlock()		// 以读模式解锁
	}
}

func writeGo(out chan<- int, idx int)  {
	for {
		// 生成随机数
		num := rand.Intn(1000)
		rwMutex.Lock()			// 以写模式加锁
		out <- num
		fmt.Printf("%dth 写go程，写入：%d\n", idx, num)
		time.Sleep(time.Millisecond * 300)		// 放大实验现象
		rwMutex.Unlock()
	}
}

func main()  {
	// 播种随机数种子
	rand.Seed(time.Now().UnixNano())
	ch := make(chan int)			// 用于 数据传递的 channel

	for i:=0; i<5; i++ {
		go readGo(ch, i+1)
	}
	for i:=0; i<5; i++ {
		go writeGo(ch,i+1)
	}
	for{
		;
	}
}
```

### 读写锁实现:
```go
package main

import (
	"math/rand"
	"time"
	"fmt"
	"sync"
)

var rwMutex sync.RWMutex		// 锁只有一把， 2 个属性 r w

var value int		// 定义全局变量，模拟共享数据

func readGo05(idx int)  {
	for {
		rwMutex.RLock()			// 以读模式加锁
		num := value
		fmt.Printf("----%dth 读 go程，读出：%d\n", idx, num)
		rwMutex.RUnlock()		// 以读模式解锁
		time.Sleep(time.Second)
	}
}
func writeGo05(idx int)  {
	for {
		// 生成随机数
		num := rand.Intn(1000)
		rwMutex.Lock()			// 以写模式加锁
		value = num
		fmt.Printf("%dth 写go程，写入：%d\n", idx, num)
		time.Sleep(time.Millisecond * 300)		// 放大实验现象
		rwMutex.Unlock()
	}
}
func main()  {
	// 播种随机数种子
	rand.Seed(time.Now().UnixNano())

	for i:=0; i<5; i++ {			// 5 个 读 go 程
		go readGo05(i+1)
	}
	for i:=0; i<5; i++ {			//
		go writeGo05(i+1)
	}
	for{
		;
	}
}
```
结果:
```
chengfei@bogon 02_并发编程 % go run 读写锁-rwlock.go
----1th 读 go程，读出：0
----3th 读 go程，读出：0
----2th 读 go程，读出：0
----5th 读 go程，读出：0
2th 写go程，写入：159
----4th 读 go程，读出：159
2th 写go程，写入：726
1th 写go程，写入：745
3th 写go程，写入：680
----2th 读 go程，读出：680
----1th 读 go程，读出：680
----3th 读 go程，读出：680
----5th 读 go程，读出：680
4th 写go程，写入：568
----4th 读 go程，读出：568
5th 写go程，写入：677
2th 写go程，写入：804
1th 写go程，写入：290
----5th 读 go程，读出：290
----3th 读 go程，读出：290
----2th 读 go程，读出：290
----1th 读 go程，读出：290
3th 写go程，写入：162
----4th 读 go程，读出：162
.......
```

###
```go
package main

import (
	"math/rand"
	"time"
	"fmt"
)

//var value06 int		// 定义全局变量，模拟共享数据
func readGo06(in <-chan int, idx int)  {
	for {
		num := <-in				// 从 channel 中读取数据
		fmt.Printf("----%dth 读 go程，读出：%d\n", idx, num)
		time.Sleep(time.Second)
	}
}
func writeGo06(out chan<- int, idx int)  {
	for {
		// 生成随机数
		num := rand.Intn(1000)
		out <- num					// 写入channel
		fmt.Printf("%dth 写go程，写入：%d\n", idx, num)
		time.Sleep(time.Millisecond * 300)		// 放大实验现象
	}
}
func main()  {
	// 播种随机数种子
	rand.Seed(time.Now().UnixNano())
	ch := make(chan int)

	for i:=0; i<5; i++ {			// 5 个 读 go 程
		go readGo06(ch, i+1)
	}
	for i:=0; i<5; i++ {			//
		go writeGo06(ch, i+1)
	}
	for{
		;
	}
}
```
结果:
```
chengfei@bogon 02_并发编程 % go run 读写锁-channel.go                
1th 写go程，写入：337
----1th 读 go程，读出：337
2th 写go程，写入：511
----2th 读 go程，读出：577
3th 写go程，写入：577
4th 写go程，写入：596
5th 写go程，写入：514
----5th 读 go程，读出：514
----4th 读 go程，读出：511
----3th 读 go程，读出：596
----5th 读 go程，读出：249
3th 写go程，写入：676
2th 写go程，写入：278
----1th 读 go程，读出：278
1th 写go程，写入：83
----2th 读 go程，读出：83
4th 写go程，写入：249
----4th 读 go程，读出：269
----3th 读 go程，读出：676
5th 写go程，写入：269
----4th 读 go程，读出：241
4th 写go程，写入：816
2th 写go程，写入：395
............
```


## 4. 条件变量:
条件变量: 条件变量的作用并不保证在同一时刻仅有一个协程(线程)访问某个共享的数据资源, 而是在对应的共享数据的状态发生变化时, 通知阻塞在某个条件上的协程(线程); 条件变量不是锁, 在并发中不能达到同步的目的, 因此**条件变量总是与锁一块使用**;

GO标准库中的`sys.Cond`类型代表了条件变量; 条件变量要与锁(互斥锁，或者读写锁)一起使用; 成员变量`L`代表与条件变量搭配使用的锁:
```go
type Cond struct {
   noCopy noCopy
   // L is held while observing or changing the condition
   L Locker
   notify  notifyList
   checker copyChecker
}
```
对应的有3个常用方法, `Wait`, `Signal`, `Broadcast`:
- `func (c *Cond) Wait()`:
	- 该函数的作用可归纳为如下三点：
		- 阻塞等待条件变量满足;
		- 释放已掌握的互斥锁相当于`cond.L.Unlock()`;
			- 注意: **以上两步为一个原子操作**;
		- 当被唤醒, `Wait()`函数返回时, 解除阻塞并重新获取互斥锁; 相当于`cond.L.Lock()`;
- `func (c *Cond) Signal()`
	- 单发通知, 给一个正等待(阻塞)在该条件变量上的`goroutine`(线程)发送唤醒通知;
- `func (c *Cond) Broadcast()`:
	- 广播通知, 给正在等待(阻塞)在该条件变量上的所有`goroutine`(线程)发送唤醒通知;

使用流程:
- 创建 条件变量: `var cond sync.Cond`
- 指定条件变量用的 锁: `cond.L = new(sync.Mutex)`
- `cond.L.Lock()`: 给公共区加锁(互斥量）
- 判断是否到达 阻塞条件(缓冲区满/空) —— `for` 循环判断
	- `for len(ch) == cap(ch) {	cond.Wait() —— 1) 阻塞; 2) 解锁; 3) 加锁; }`
- 访问公共区 —— 读、写数据、操作; 
- 解锁条件变量用的 锁: `cond.L.Unlock()`;
- 唤醒阻塞在条件变量上的 对端;`signal()、 Broadcast()`

```go
package main
import "fmt"
import "sync"
import "math/rand"
import "time"

var cond sync.Cond             // 创建全局条件变量

// 生产者
func producer(out chan<- int, idx int) {
   for {
      cond.L.Lock()           	// 条件变量对应互斥锁加锁
      for len(out) == 3 {          	// 产品区满 等待消费者消费
         cond.Wait()             	// 挂起当前协程， 等待条件变量满足，被消费者唤醒
      }
      num := rand.Intn(1000) 	// 产生一个随机数
      out <- num             	// 写入到 channel 中 （生产）
      fmt.Printf("%dth 生产者，产生数据 %3d, 公共区剩余%d个数据\n", idx, num, len(out))
      cond.L.Unlock()             	// 生产结束，解锁互斥锁
      cond.Signal()           	// 唤醒 阻塞的 消费者
      time.Sleep(time.Second)       // 生产完休息一会，给其他协程执行机会
   }
}
//消费者
func consumer(in <-chan int, idx int) {
   for {
      cond.L.Lock()           	// 条件变量对应互斥锁加锁（与生产者是同一个）
      for len(in) == 0 {      	// 产品区为空 等待生产者生产
         cond.Wait()             	// 挂起当前协程， 等待条件变量满足，被生产者唤醒
      }
      num := <-in                	// 将 channel 中的数据读走 （消费）
      fmt.Printf("---- %dth 消费者, 消费数据 %3d,公共区剩余%d个数据\n", idx, num, len(in))
      cond.L.Unlock()             	// 消费结束，解锁互斥锁
      cond.Signal()           	// 唤醒 阻塞的 生产者
      time.Sleep(time.Millisecond * 500)    	//消费完 休息一会，给其他协程执行机会
   }
}
func main() {
   rand.Seed(time.Now().UnixNano())  // 设置随机数种子

   product := make(chan int, 3)      // 产品区（公共区）使用channel 模拟
   cond.L = new(sync.Mutex)          // 创建互斥锁和条件变量

   for i := 0; i < 5; i++ {          // 5个消费者
      go producer(product, i+1)
   }
   for i := 0; i < 3; i++ {          // 3个生产者
      go consumer(product, i+1)
   }
   for {
	   ;	// 主协程阻塞 不结束
   }
}
```
结果:
```
chengfei@bogon 02_并发编程 % go run 条件变量.go
4th 生产者，产生数据  10, 公共区剩余1个数据
---- 1th 消费者, 消费数据  10,公共区剩余0个数据
5th 生产者，产生数据 629, 公共区剩余1个数据
2th 生产者，产生数据 478, 公共区剩余2个数据
1th 生产者，产生数据 701, 公共区剩余3个数据
---- 3th 消费者, 消费数据 629,公共区剩余2个数据
---- 2th 消费者, 消费数据 478,公共区剩余1个数据
3th 生产者，产生数据 258, 公共区剩余2个数据
---- 2th 消费者, 消费数据 701,公共区剩余1个数据
---- 3th 消费者, 消费数据 258,公共区剩余0个数据
4th 生产者，产生数据 684, 公共区剩余1个数据
---- 1th 消费者, 消费数据 684,公共区剩余0个数据
3th 生产者，产生数据 279, 公共区剩余1个数据
1th 生产者，产生数据 479, 公共区剩余2个数据
5th 生产者，产生数据 125, 公共区剩余3个数据
---- 3th 消费者, 消费数据 279,公共区剩余2个数据
2th 生产者，产生数据 764, 公共区剩余3个数据
---- 2th 消费者, 消费数据 479,公共区剩余2个数据
---- 3th 消费者, 消费数据 125,公共区剩余1个数据
---- 2th 消费者, 消费数据 764,公共区剩余0个数据
4th 生产者，产生数据  70, 公共区剩余1个数据
5th 生产者，产生数据 858, 公共区剩余2个数据
.............
```