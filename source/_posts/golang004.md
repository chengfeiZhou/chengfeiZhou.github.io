---
title: 四、Golang之网络编程
date: 2021-04-21 22:06:14
tags: 
	- Golang高级
    - Go网络编程
categories: 
    - Golang高级
    - Go网络编程
---
# 一、网络协议:
## 1. 典型协议:
- 传输层: 常见协议有**TCP/UDP**协议;
- 应用层: 常见的协议有**HTTP协议，FTP协议**;
- 网络层: 常见协议有**IP协议、ICMP协议、IGMP协议**;
- 网络接口层: 常见协议有**ARP协议、RARP协议**;
- TCP传输控制协议(Transmission Control Protocol)是一种面向连接的、可靠的、基于字节流的传输层通信协议;
- UDP用户数据报协议(User Datagram Protocol)是OSI参考模型中一种无连接的传输层协议, 提供面向事务的简单不可靠信息传送服务;
- HTTP超文本传输协议(Hyper Text Transfer Protocol)是互联网上应用最为广泛的一种网络协议;
- FTP文件传输协议(File Transfer Protocol)
- IP协议是因特网互联协议(Internet Protocol)
- ICMP协议是Internet控制报文协议(Internet Control Message Protocol): 它是TCP/IP协议族的一个子协议, 用于在IP主机、路由器之间传递控制消息;
- IGMP协议是 Internet 组管理协议(Internet Group Management Protocol), 是因特网协议家族中的一个组播协议; 该协议运行在主机和组播路由器之间; 
- ARP协议是正向地址解析协议(Address Resolution Protocol) 通过已知的IP, 寻找对应主机的MAC地址;
- RARP是反向地址转换协议, 通过MAC地址确定IP地址;


## 2.分层模型:
### 2.1 网络分层架构:
为了减少协议设计的复杂性, 大多数网络模型均采用分层的方式来组织; 每一层都有自己的功能, 就像建筑物一样, 每一层都靠下一层支持; **每一层利用下一层提供的服务来为上一层提供服务, 本层服务的实现细节对上层屏蔽**;

![](/images/golang/017_net_osi.png)

业内普遍的分层方式有两种: **OSI七层模型** 和 **TCP/IP四层模型**, 可以通过背诵两个口诀来快速记忆：
- OSI七层模型: 物、数、网、传、会、表、应;
- TCP/IP四层模型: 链、网、传、应;

- 物理层: 主要定义物理设备标准, 如网线的接口类型、光纤的接口类型、各种传输介质的传输速率等; 它的主要作用是传输比特流(就是由1、0转化为电流强弱来进行传输, 到达目的地后再转化为1、0, 也就是我们常说的数模转换与模数转换); 这一层的数据叫做**比特**;
- 数据链路层: 定义了如何让格式化数据以**帧**为单位进行传输, 以及如何让控制对物理介质的访问; 这一层通常还提供错误检测和纠正, 以确保数据的可靠传输; 如：串口通信中使用到的115200、8、N、1;
- 网络层: 在位于不同地理位置的网络中的两个主机系统之间提供连接和路径选择; Internet的发展使得从世界各站点访问信息的用户数大大增加, 而网络层正是管理这种连接的层;
- 传输层: 定义了一些传输数据的协议和端口号(WWW端口80等), 如: TCP(传输控制协议, 传输效率低, 可靠性强, 用于传输可靠性要求高, 数据量大的数据); UDP(用户数据报协议, 与TCP特性恰恰相反, 用于传输可靠性要求不高, 数据量小的数据, 如QQ聊天数据就是通过这种方式传输的);  主要是将从下层接收的数据进行分段和传输, 到达目的地址后再进行重组; 常常把这一层数据叫做**段**;
- 会话层: 通过传输层(端口号: 传输端口与接收端口)建立数据传输的通路; 主要在系统之间发起会话或者接受会话请求(设备之间需要互相认识可以是IP也可以是MAC地址或者是主机名)。
- 表示层: 可确保一个系统的应用层所发送的信息可以被另一个系统的应用层读取; 例如: PC程序与另一台计算机进行通信,其中一台计算机使用扩展二一十进制交换码(EBCDIC)，而另一台则使用美国信息交换标准码（ASCII）来表示相同的字符; 如有必要, 表示层会通过使用一种通用格式来实现多种数据格式之间的转换; 
- 应用层: 是最靠近用户的OSI层; 这一层为用户的应用程序(例如电子邮件、文件传输和终端仿真)提供网络服务;

### 2.2 层与协议:
每一层都是为了完成一种功能, 为了实现这些功能, 就需要大家都遵守共同的规则; 大家都遵守这规则, 就叫做**协议(protocol)**;
网络的每一层, 都定义了很多协议; 这些协议的总称, 叫**TCP/IP协议**; TCP/IP协议是一个大家族, 不仅仅只有TCP和IP协议, 它还包括其它的协议, 如下图:
![](/images/golang/018_xieyi.png)

协议功能:
![](/images/golang/019_xieyigongneng.png)

- 链路层:
    - 以太网规定, 连入网络的所有设备, 都必须具有**网卡**接口; 数据包必须是从一块网卡, 传送到另一块网卡; 通过网卡能够使不同的计算机之间连接, 从而完成数据通信等功能; 网卡的地址 —— **MAC 地址**, 就是数据包的物理发送地址和物理接收地址;
- 网络层:
    - 网络层的作用是引进一套新的地址, 使得能够区分不同的计算机**是否属于同一个子网络**; 这套地址就叫做**网络地址(IP地址)**;
    - 网络地址帮助我们确定计算机所在的子网络, **MAC 地址**则将数据包送到该子网络中的目标网卡; 网络层协议包含的主要信息是**源IP**和**目的IP**;
    - 于是, **网络层**出现以后, 每台计算机有了两种地址: 一种是 **MAC 地址**, 另一种是**IP地址**; 两种地址之间没有任何联系, MAC 地址是绑定在网卡上的，网络地址则是管理员分配的，它们只是随机组合在一起。
    - 网络地址帮助我们确定计算机所在的子网络，MAC 地址则将数据包送到该子网络中的目标网卡。因此，从逻辑上可以推断，必定是先处理网络地址，然后再处理 MAC 地址。
- 传输层:
    - 当我们一边聊QQ，一边聊微信，当一个数据包从互联网上发来的时候，我们怎么知道，它是来自QQ的内容，还是来自微信的内容？
    - 也就是说，我们还需要一个参数，表示这个数据包到底供哪个程序（进程）使用。这个参数就叫做“端口”（port），它其实是每一个使用网卡的程序的编号。每个数据包都发到主机的特定端口，所以不同的程序就能取到自己所需要的数据。
    - 端口特点：
        - 对于同一个端口，在不同系统中对应着不同的进程
        - 对于同一个系统，一个端口只能被一个进程拥有
- 应用层:
    - 应用程序收到“传输层”的数据，接下来就要进行解读。由于互联网是开放架构，数据来源五花八门，必须事先规定好格式，否则根本无法解读。“应用层”的作用，就是规定应用程序的数据格式

### 2.3 通信过程
两台计算机通过TCP/IP协议通讯的过程如下所示:
![](/images/golang/020_tongxinguocheng.png)

### 2.4 总结通信过程：
- mac地址(不需要用户指定) --> (ARP 协议)Ip ——> mac
- IP 地址(需要用户指定) ——> 确定主机
- port 端口号(需要用户指定)	——> 确定程序
    - 不能使用系统占用的默认端口; 建议使用5000+ 端口;
    - 65535为端口上限;

# 二、Socket编程:
## 1.什么是Socket:
Socket, 英文含义是**插座、插孔**, 一般称之为**套接字**, 用于描述IP地址和端口; 可以实现不同程序间的数据通信;
Socket起源于Unix, 而Unix基本哲学之一就是“一切皆文件”, 都可以用“打开open –> 读写write/read –> 关闭close”模式来操作; 
Socket就是该模式的一个实现, 网络的Socket数据传输是一种特殊的I/O, Socket也是一种文件描述符; Socket也具有一个类似于打开文件的函数调用: `Socket()`, 该函数返回一个整型的Socket描述符, 随后的连接建立、数据传输等操作都是通过该Socket实现的;

![](/images/golang/021_socket.png)

在TCP/IP协议中, "IP地址+TCP或UDP端口号"唯一标识网络通讯中的一个进程; "IP地址+端口号"就对应一个socket; 欲建立连接的两个进程各自有一个socket来标识, 那么这两个socket组成的socket pair就唯一标识一个连接; 因此可以用Socket来描述网络连接的一对一关系;
- 常用的Socket类型有两种: **流式Socket(SOCK_STREAM)和数据报式Socket(SOCK_DGRAM)**; 
    - 流式是一种面向连接的Socket, 针对于面向连接的TCP服务应用;
    - 数据报式Socket是一种无连接的Socket, 对应于无连接的UDP服务应用; 

## 2. 网络应用程序设计模式:
### 2.1 模式:
- C/S模式:
    - 传统的网络应用设计模式, 客户机(client)/服务器(server)模式; 需要在通讯两端各自部署客户机和服务器来完成数据通信;
- B/S模式:
    - 浏览器(Browser)/服务器(Server)模式; 只需在一端部署服务器, 而另外一端使用每台PC都默认配置的浏览器即可完成数据的传输;

- 优缺点:
    - 对于C/S模式来说, 其优点明显; 
        - 客户端位于目标主机上可以保证性能, 将数据缓存至客户端本地, 从而**提高数据传输效率**;
        - 一般来说客户端和服务器程序由一个开发团队创作, 所以他们之间所**采用的协议相对灵活**; 
        - 可以在标准协议的基础上根据需求裁剪及定制; 例如, 腾讯所采用的通信协议,即为ftp协议的修改剪裁版;
    - 因此, 传统的网络应用程序及较大型的网络应用程序都首选C/S模式进行开发; 如, 知名的网络游戏魔兽世界; 3D画面, 数据量庞大, 使用C/S模式可以提前在本地进行大量数据的缓存处理, 从而提高观感;
    - C/S模式的缺点也较突出: 
        - 由于客户端和服务器都需要有一个开发团队来完成开发; 
        - **工作量将成倍提升**, 开发周期较长; 
        - 从用户角度出发, 需要将客户端安插至用户主机上, 对用户主机的**安全性构成威胁**; 
        - 这也是很多用户不愿使用C/S模式应用程序的重要原因;
    - B/S模式相比C/S模式而言, 由于它没有独立的客户端, 使用标准浏览器作为客户端, 其工作**开发量较小**; 只需开发服务器端即可; 另外由于其采用浏览器显示数据, 因此移植性非常好, **不受平台限制**; 如早期的偷菜游戏, 在各个平台上都可以完美运行;
    - B/S模式的缺点也较明显:
        - 由于使用第三方浏览器, 因此**网络应用支持受限**;
        - 另外, 没有客户端放到对方主机上, **缓存数据不尽如人意**, 从而传输数据量受到限制; 应用的观感大打折扣; 
        - 第三, 必须与浏览器一样, 采用标准http协议进行通信, **协议选择不灵活**;
	- 因此在开发过程中, 模式的选择由上述各自的特点决定; 根据实际需求选择应用程序设计模式;

### 2.2 TCP的C/S架构:
![](/images/golang/022_tcp_cs.png)

### 2.3 简单的C/S模型通信:
#### a.Server端:
Listen函数:
```go
func Listen(network, address string) (Listener, error)
```
- network: 选用的协议: TCP、UDP, 如：“tcp”或 “udp”;
- address: IP地址+端口号,如: “127.0.0.1:8000”或 “:8000”;
Listener 接口：
```go
type Listener interface {
			Accept() (Conn, error)
			Close() error
			Addr() Addr
}
```
Conn 接口:
```go
type Conn interface {
	Read(b []byte) (n int, err error)
	Write(b []byte) (n int, err error)
	Close() error
	LocalAddr() Addr
	RemoteAddr() Addr
	SetDeadline(t time.Time) error
	SetReadDeadline(t time.Time) error
	SetWriteDeadline(t time.Time) error
}
```
参看 [https://studygolang.com/pkgdoc](https://studygolang.com/pkgdoc) 中文帮助文档中的demo:

- TCP-CS服务器:
	- 创建监听`socket`: `listener := net.Listen("TCP", "IP+port")` => IP+port --服务器自己的IP 和 port;
	- 启动监听 `conn := listener.Accept()`: `conn`用于 通信的 `socket`;
	- `conn.Read()`;
	- 处理使用 数据;
	- 返回数据: `conn.Write()`;
	- 关闭`listener`、`conn`;

TCP_Server.go
```go
package main

import (
	"fmt"
	"net"
)

func main() {
	// 指定服务器
	listener, err := net.Listen("tcp", "0.0.0.0:9000")
	if err != nil {
		fmt.Println("net.Listen err: ", err)
		return
	}
	defer listener.Close()

	fmt.Println("服务器等待客户端建立连接...")
	// 阻塞监听客户端请求
	conn, err := listener.Accept()
	if err != nil {
		fmt.Println("listener.Accept err: ", err)
		return
	}
	defer conn.Close()

	buf := make([]byte, 4096)
	n, err := conn.Read(buf)
	if err != nil {
		fmt.Println("conn.Read err: ", err)
		return
	}
	// 打印数据
	fmt.Println("服务器读取到: ", string(buf[:n]))

    // 回复数据
	if _, err := conn.Write([]byte("server is ok")); err != nil {
		fmt.Println("conn.Write err: ", err)
	}
}
```
在整个通信过程中, 服务器端有两个socket参与进来, 但用于通信的只有 conn 这个socket; 它是由 listener创建的; 隶属于服务器端
![](/images/golang/023_tcp_cs.png)

#### b.Client 端:
Dial函数:
```go
func Dial(network, address string) (Conn, error)
```
- network: 选用的协议：TCP、UDP, 如：“tcp”或 “udp”;
- address: 服务器IP地址+端口号, 如: “121.36.108.11:8000”或 “www.itcast.cn:8000”;
Conn 接口:
```go
type Conn interface {
	Read(b []byte) (n int, err error)
	Write(b []byte) (n int, err error)
	Close() error
	LocalAddr() Addr
	RemoteAddr() Addr
	SetDeadline(t time.Time) error
	SetReadDeadline(t time.Time) error
	SetWriteDeadline(t time.Time) error
}
```

- TCP-CS客户端: 
	- `conn, err := net.Dial("TCP", 服务器的IP+port)`;
	- 写数据给 服务器 `conn.Write()`
	- 读取服务器回发的 数据 `conn.Read()`
	- `conn.Close()`

TCP_Client.go
```go
package main

import (
	"fmt"
	"net"
)

func main() {
	// 主动连接请求
	conn, err := net.Dial("tcp", "127.0.0.1:9000")
	if err != nil {
		fmt.Println("net.Dial err: ", err)
		return
	}
	defer conn.Close()

	// 发送数据
	_, err = conn.Write([]byte("R U ok??"))
	if err != nil {
		fmt.Println("onn.Write err: ", err)
		return
	}

    // 接收数据
	buf := make([]byte, 4096)
	n, err := conn.Read(buf)
	if err != nil {
		fmt.Println("conn.Read err: ", err)
		return
	}
	// 打印数据
	fmt.Println("客户端读取到: ", string(buf[:n]))
}
```

### 2.4 并发的C/S模型通信:
#### a.并发server:
现在已经完成了客户端与服务端的通信, 但是服务端只能接收一个用户发送过来的数据, 怎样接收多个客户端发送过来的数据, 实现一个高效的并发服务器呢？
`Accept()`函数的作用是等待客户端的链接, 如果客户端没有链接, 该方法会阻塞; 如果有客户端链接, 那么该方法返回一个Socket负责与客户端进行通信; 所以, 每来一个客户端, 该方法就应该返回一个Socket与其通信, 因此, 可以使用一个死循环, 将`Accept()`调用过程包裹起来;
ps:
- 实现并发处理多个客户端数据的服务器, 就需要**针对每一个客户端连接, 单独产生一个Socket, 并创建一个单独的goroutine与之完成通信**;

- TCP-CS并发服务器:
	- 创建 监听套接字 `listener := net.Listen("tcp"， 服务器的IP+port)`
	- `defer listener.Close()`
	- `for` 循环 阻塞监听 客户端连接事件: `conn := listener.Accept()`
	- 创建 go程 对应每一个 客户端进行数据通信`go HandlerConnet()`
	- 实现 `HandlerConnet(conn net.Conn)`
		- `defer conn.Close()`
		- 获取成功连接的客户端 `Addr`: `conn.RemoteAddr()`
		- for 循环 读取 客户端发送数据: `conn.Read(buf)`
		- 处理数据 小 -> 大	`strings.ToUpper()`
		- 回写转化后的数据: `conn.Write(buf[:n])` 

并发Service.go
```go
package main

import (
	"fmt"
	"io"
	"net"
	"strings"
)

func Handler(conn net.Conn) {
	defer conn.Close()
	// 获取客户端的网络地址
	addr := conn.RemoteAddr().String()
	fmt.Println(addr, "connect successful!!!")

	buf := make([]byte, 4096)
	for {
		n, err := conn.Read(buf)
		if err != nil {
			if err == io.EOF {
				fmt.Println("client close!!!")
				return
			}
			fmt.Println("conn.Read err: ", err)
			return
		}
		// 打印数据
		fmt.Println("服务器读取到: ", string(buf[:n]))

		// 回复数据
		if _, err := conn.Write([]byte(strings.ToUpper(string(buf[:n])))); err != nil {
			fmt.Println("conn.Write err: ", err)
		}

	}

}

func main() {
	// 指定服务器
	listener, err := net.Listen("tcp", "0.0.0.0:9000")
	if err != nil {
		fmt.Println("net.Listen err: ", err)
		return
	}
	defer listener.Close()

	fmt.Println("服务器等待客户端建立连接...")
	for {
		// 阻塞监听客户端请求
		conn, err := listener.Accept()
		if err != nil {
			fmt.Println("listener.Accept err: ", err)
			return
		}
		go Handler(conn)
	}
}
```

#### b.并发Client
客户端不仅需要持续的向服务端发送数据, 同时也要接收从服务端返回的数据; 因此可将发送和接收放到不同的协程中; 
主协程循环接收服务器回发的数据(该数据应已转换为大写), 并打印至屏幕; 子协程循环从键盘读取用户输入数据, 写给服务器; 读取键盘输入可使用 `os.Stdin.Read(str)`; 定义切片str, 将读到的数据保存至str中;

```go
package main
 
import (
	"fmt"
	"net"
	"os"
)

func main() {
	// 主动连接请求
	conn, err := net.Dial("tcp", "127.0.0.1:9000")
	if err != nil {
		fmt.Println("net.Dial err: ", err)
		return
	}
	defer conn.Close()

	go func() {
		str := make([]byte, 1024)
		for {
			n, err := os.Stdin.Read(str)
			if err != nil {
				fmt.Println("os.Stdin.Read err:", err)
				return
			}
			// 将从键盘读到的数据，发送给服务器
			_, err = conn.Write(str[:n]) // 读多少，写多少
			if err != nil {
				fmt.Println("conn.Write err:", err)
				return
			}
		}
	}()
	// 主协程, 接收服务器数据
	buf := make([]byte, 4096)
	for {
		n, err := conn.Read(buf)
		if err != nil {
			fmt.Println("conn.Read err: ", err)
			return
		}
		// 打印数据
		fmt.Println("客户端读取到: ", string(buf[:n]))
	}
}
```
## 3. TCP通信
下图是一次TCP通讯的时序图; TCP连接建立断开; 包含熟知的**三次握手**和**四次握手**:

![](/images/golang/024_sanwosihui.png)

### 3.1 三次握手:
...

### 3.2 四次挥手:
...

## 4. UDP:
### 4.1 UDP服务器
由于UDP是**无连接**的, 所以, 服务器端不需要额外创建监听套接字, 只需要指定好IP和port, 然后监听该地址, 等待客户端与之建立连接, 即可通信;
- 创建监听地址:
	- `func ResolveUDPAddr(network, address string) (*UDPAddr, error) `;
- 创建监听连接:
	- `func ListenUDP(network string, laddr *UDPAddr) (*UDPConn, error) `;
- 接收udp数据:
	- `func (c *UDPConn) ReadFromUDP(b []byte) (int, *UDPAddr, error)`;
- 写出数据到udp:
	- `func (c *UDPConn) WriteToUDP(b []byte, addr *UDPAddr) (int, error)`;

udp_server.go
```go
package main

import (
   "fmt"
   "net"
)

func main() {
   //创建监听的地址，并且指定udp协议
   udp_addr, err := net.ResolveUDPAddr("udp", "127.0.0.1:8002")
   if err != nil {
      fmt.Println("ResolveUDPAddr err:", err)
      return
   }
   conn, err := net.ListenUDP("udp", udp_addr)    //创建监听链接
   if err != nil {
      fmt.Println("ListenUDP err:", err)
      return
   }
   defer conn.Close()

   buf := make([]byte, 1024)
   n, raddr, err := conn.ReadFromUDP(buf)        //接收客户端发送过来的数据，填充到切片buf中。
   if err != nil {
      return
   }
   fmt.Println("客户端发送：", string(buf[:n]))

   _, err = conn.WriteToUDP([]byte("nice to see u in udp"), raddr) //向客户端发送数据
   if err != nil {
      fmt.Println("WriteToUDP err:", err)
      return
   }
}
```

### 4.2 UDP客户端:
UDP_client.go

```go
package main

import (
   "net"
   "fmt"
)

func main() {
   conn, err := net.Dial("udp", "127.0.0.1:8002") 
   if err != nil {
      fmt.Println("net.Dial err:", err)
      return
   }
   defer conn.Close()

   conn.Write([]byte("Hello! I'm client in UDP!"))

   buf := make([]byte, 1024)
   n, err1 := conn.Read(buf)
   if err1 != nil {
      return
   }
   fmt.Println("服务器发来：", string(buf[:n]))
}
```

### 4.3 UDP并发:
UDP_async_server.go:
```go
package main

import (
   "net"
   "fmt"
)

func main() {
   // 创建 服务器 UDP 地址结构。指定 IP + port
   laddr, err := net.ResolveUDPAddr("udp", "127.0.0.1:8003")
   if err != nil {
      fmt.Println("ResolveUDPAddr err:", err)
      return
   }
   // 监听 客户端连接
   conn, err := net.ListenUDP("udp", laddr)
   if err != nil {
      fmt.Println("net.ListenUDP err:", err)
      return
   }
   defer conn.Close()

   for {
      buf := make([]byte, 1024)
      n, raddr, err := conn.ReadFromUDP(buf)
      if err != nil {
         fmt.Println("conn.ReadFromUDP err:", err)
         return
      }
      fmt.Printf("接收到客户端[%s]：%s", raddr, string(buf[:n]))

      conn.WriteToUDP([]byte("I-AM-SERVER"), raddr) // 简单回写数据给客户端
   }
}
```

UDP_async_client.go
```go
package main

import (
   "net"
   "os"
   "fmt"
)

func main() {
   conn, err := net.Dial("udp", "127.0.0.1:8003")
   if err != nil {
      fmt.Println("net.Dial err:", err)
      return
   }
   defer conn.Close()
   go func() {
      str := make([]byte, 1024)
      for {
         n, err := os.Stdin.Read(str) //从键盘读取内容， 放在str
         if err != nil {
            fmt.Println("os.Stdin. err1 = ", err)
            return
         }
         conn.Write(str[:n])       // 给服务器发送
      }
   }()
   buf := make([]byte, 1024)
   for {
      n, err := conn.Read(buf)
      if err != nil {
         fmt.Println("conn.Read err:", err)
         return
      }
      fmt.Println("服务器写来：", string(buf[:n]))
   }
}
```

## 5.UDP与TCP的差异
|TCP	|UDP|
|-|-|
|面向连接|	面向无连接|
|要求系统资源较多	|要求系统资源较少|
|TCP程序结构较复杂|	UDP程序结构较简单|
|使用流式	|使用数据包式|
|保证数据准确性	|不保证数据准确性|
|保证数据顺序	|不保证数据顺序|
|通讯速度较慢|	通讯速度较快|

# 三、实例 -- 文件传输:
## 1. 流程简析
借助TCP完成文件的传输, 基本思路如下: 
- 发送方（客户端）向服务端发送文件名, 服务端保存该文件名;
- 接收方（服务端）向客户端返回一个消息ok, 确认文件名保存成功;
- 发送方（客户端）收到消息后, 开始向服务端发送文件数据;
- 接收方（服务端）读取文件内容, 写入到之前保存好的文件中;

![](/images/golang/025_fileserver.png)

## 2. 获取文件属性:
借助`os`包中的`stat()`函数来获取文件属性信息; 在函数返回的文件属性中包含文件名和文件大小; `Stat`参数`name`传入的是文件访问的绝对路径; `FileInfo`中的`Name()`函数可以将文件名单独提取出来;
```go
func Stat(name string) (FileInfo, error) 
type FileInfo interface {
   Name() string       
   Size() int64        
   Mode() FileMode     
   ModTime() time.Time 
   IsDir() bool        
   Sys() interface{}   
}
```
获取文件属性:
```go
package main

import (
	"fmt"
	"os"
)

func main() {
	args := os.Args
	if len(args) != 2 {
		fmt.Println("格式为: run go xxx.go 文件名")
		return
	}
	fileName := args[1]

	file_info, err := os.Stat(fileName)
	if err != nil {
		fmt.Println("os.Stat err:", err)
		return
	}
	fmt.Println("文件name为：", file_info.Name()) // 得到文件名(不含路径)
	fmt.Println("文件size为：", file_info.Size()) // 得到文件大小。单位字节
}
```

## 3. 客户端实现
实现流程大致如下: 
- 提示用户输入文件名, 接收文件名path(含访问路径);
- 使用`os.Stat()`获取文件属性, 得到纯文件名(去除访问路径);
- 主动连接服务器, 结束时关闭连接;
- 给接收端(服务器)发送文件名`conn.Write()`;
- 读取接收端回发的确认数据`conn.Read()`;
- 判断是否为“ok”; 如果是, 封装函数`SendFile()` 发送文件内容, 传参path和conn;
- 只读Open文件, 结束时`Close`文件
- 循环读文件, 读到`EOF`终止文件读取;
- 将读到的内容原封不动`Write`给接收端(服务器)

file_client.go
```go
package main

import (
	"fmt"
	"io"
	"net"
	"os"
)

const ip_port = "127.0.0.1:8005"

func SendFile(path string, conn net.Conn) {
	// 以只读方式打开文件
	f, err := os.Open(path)
	if err != nil {
		fmt.Println("os.Open err:", err)
		return
	}
	defer f.Close() // 发送结束关闭文件

	// 循环读取文件，原封不动的写给服务器
	buf := make([]byte, 4096)
	for {
		n, err := f.Read(buf) // 读取文件内容到切片缓冲中
		if err != nil {
			if err == io.EOF {
				fmt.Println("文件发送完毕")
			} else {
				fmt.Println("f.Read err:", err)
			}
			return
		}
		conn.Write(buf[:n]) // 原封不动写给服务器
	}
}

func main() {
	// 提示输入文件名
	fmt.Println("请输入需要传输的文件：")
	var path string
	fmt.Scan(&path)

	// 获取文件名   fileInfo.Name()
	fileInfo, err := os.Stat(path)
	if err != nil {
		fmt.Println("os.Stat err:", err)
		return
	}

	// 主动连接服务器
	conn, err := net.Dial("tcp", ip_port)
	if err != nil {
		fmt.Println("net.Dial err:", err)
		return
	}
	defer conn.Close()

	// 给接收端，先发送文件名
	_, err = conn.Write([]byte(fileInfo.Name()))
	if err != nil {
		fmt.Println("conn.Write err:", err)
		return
	}

	// 读取接收端回发确认数据 —— ok
	buf := make([]byte, 1024)
	n, err := conn.Read(buf)
	if err != nil {
		fmt.Println("conn.Read err:", err)
		return
	}

	// 判断如果是ok，则发送文件内容
	if "ok" == string(buf[:n]) {
		SendFile(path, conn)
	}

}
```

## 4.服务端实现:
实现流程大致如下: 
- 创建监听`listener`, 程序结束时关闭;
- 阻塞等待客户端连接, 程序结束时关闭conn;
- 读取客户端发送文件名, 保存fileName;
- 回发`“ok”`给客户端做应答
- 封装函数 `RecvFile`接收客户端发送的文件内容, 传参`fileName` 和 `conn`;
- 按文件名`Create`文件, 结束时`Close`;
- 循环`Read`客户端发送的文件内容, 当读到`EOF`说明文件读取完毕;
- 将读到的内容原封不动`Write`到创建的文件中;

file_server.go
```go
package main

import (
	"fmt"
	"io"
	"net"
	"os"
)

const port = 8005

func RecvFile(fileName string, conn net.Conn) {
	// 创建新文件
	f, err := os.Create(fileName)
	if err != nil {
		fmt.Println("Create err:", err)
		return
	}
	defer f.Close()

	// 接收客户端发送文件内容，原封不动写入文件
	buf := make([]byte, 4096)
	for {
		n, err := conn.Read(buf)
		if err != nil {
			if err == io.EOF {
				fmt.Println("文件接收完毕")
			} else {
				fmt.Println("Read err:", err)
			}
			return
		}
		f.Write(buf[:n]) // 写入文件，读多少写多少
	}
}

func main() {
	// 创建监听
	listener, err := net.Listen("tcp", "0.0.0.0:8005")
	if err != nil {
		fmt.Println("Listen err:", err)
		return
	}
	defer listener.Close()

	// 阻塞等待客户端连接
	conn, err := listener.Accept()
	if err != nil {
		fmt.Println("Accept err:", err)
		return
	}
	defer conn.Close()
	fmt.Printf("%s已连接...", conn.RemoteAddr())

	buf := make([]byte, 1024)
	n, err := conn.Read(buf)
	if err != nil {
		fmt.Println("conn.Read err:", err)
		return
	}
	fileName := string(buf[:n]) // 保存文件名
	// 回复 0k 给发送端
	conn.Write([]byte("ok"))
	// 接收文件内容
	RecvFile(fileName, conn) // 封装函数接收文件内容， 传fileName 和 conn
}
```

# 四、示例 - 并发聊天室:
## 1 模块简述
整个聊天室程序可简单划分为如下模块, 都分别使用协程来实现: 
- 主协程(服务器);
	- 负责监听、接收用户(客户端)连接请求, 建立通信关系; 同时启动相应的协程处理任务;
- 处理用户连接协程: `HandleConnect`
	- 负责新上线用户的存储, 用户消息读取、发送，用户改名、下线处理及超时处理;
	- 为了提高并发效率, 同时给一个用户维护多个协程来并行处理上述任务;
- 用户消息广播协程: `Manager`
	- 负责在线用户遍历, 用户消息广播发送; 需要与HandleConnect协程及用户子协程协作完成;
- 协程间应用数据及通信:
	- `map`: 存储所有登录聊天室的用户信息 => `key`: 用户的ip+port; `Value`: Client结构体;
	- Client结构体: 包含成员: 用户名Name, 网络地址Addr(ip+port), 发送消息的通道C(channel);
	- 通道message:协调并发协程间消息的传递;

## 2. 广播用户上线
首先, 服务器启动, 等待用户建立通信连接; 当有用户连接上来, 将其存储到`map`中, 这样就维护了一个"在线用户”的列表; 当再有新用户连接上来时, 应向该列表中所有用户进行广播通知, 提示"xxx用户上线"; 
简单实现手法可以循环读取列表中的用户, 依次向其发送消息通知新用户上线; 但这种方式无疑是一种串行的通信手段, 实现简单, 但执行效率较低; 
在go语言中, 利用协程轻便、高效、并发性好的特性, 给每个登录用户维护多个协程来进行数据通信, 借助`channel`不需要使用同步锁, 就可以实现高效的并发通信;
下图充分利用`goroutine`和`channel`实现了新用户登录, 向所有在线用户进行广播通知:

![](/images/golang/026_guangbo.png)
分析上图, 主要分为几大模块: 
- 全局位置定义用户结构体类型 `Client`, 存储登录用户信息; 成员包含`channel`、`Name`、`Addr`:
```go
type Client struct {
	C chan string
	Name string
	Addr string
}
```
- 定义全局通道message处理消息;
- 定义全局`map` 存储在线用户信息; `Key`为用户网络地址; `Value`为用户结构体;
- 主协程, 监听客户端连接请求, 当有新的客户端连接, 创建新协程`handleConnet`处理用户连接;
- `handleConnet`协程, 获取用户网络地址(Ip+port), 创建新用户结构体, 包含成员`C、Name、Addr`; 新用户的`Name`和`Addr`初值都是用户网络地址(Ip+port); 将用户结构体存入map中, 并创建`WriteMsgToClient`协程, 专门负责给当前用户发送消息; 组织新用户上线广播消息内容; 写入全局通道`	message`中;
- `WriteMsgToClient`协程, 读取用户结构体`C`中的数据, 没有则阻塞等待, 有数据写出给登录用户;
- `Manager`协程, 给`map`分配空间; 循环读取 `message` 通道中是否有数据; 没有, 阻塞等待; 有则解除阻塞, 将`message`通道中读到的数据写到用户结构体中的`C`通道;

广播用户上线: 
- 主go程中, 创建监听套接字;  记得`defer`
- `for` 循环监听客户端连接请求; `Accept()`
- 有一个客户端连接, 创建新 go 程 处理客户端数据 `HandlerConnet(conn)`;
- 定义全局结构体类型: `C 、Name、Addr`;
- 创建全局`map、channel`;
- 实现`HandlerConnet`, 获取客户端`IP+port —— RemoteAddr()`; 初始化新用户结构体信息; 
- 创建 `Manager` 实现管理go程 --- `Accept()` 之前;
- 实现 `Manager`:  初始化 在线用户 `map`; 循环 读取全局 `channel`, 如果无数据, 阻塞; 如果有数据, 遍历在线用户 `map`, 将数据写到 用户的 `C` 里;
- 将新用户添加到 在线用户 `map` 中, `Key == IP+port`  `value= 新用户结构体`;
- 创建 `WriteMsgToClient` go程，专门给当前用户写数据。 —— 来源于 用户自带的 C 中; 
- 实现 `WriteMsgToClient(clnt，conn)`; 遍历自带的`C`, 读数据, `conn.Write` 到 客户端; 
- `HandlerConnet`中, 结束位置, 组织用户上线信息, 将 用户上线信息 写 到全局 `channel`  —— `Manager` 的读就被激活(原来一直阻塞)
- `HandlerConnet`中, 结尾 加 `for {  ；}`;

```go
package main

import (
	"fmt"
	"net"
)

// 创建用户结构体
type Client struct {
	C    chan string
	Name string
	Addr string
}

// 创建全局map, 存储在线用户
var onlineMap map[string]Client

// 创建全局channel, 传递用户消息
var message = make(chan string)

func HandlerConnect(conn net.Conn) {
	defer conn.Close()
	// 获取用户地址
	netAddr := conn.RemoteAddr().String()
	// 创建新连接用户
	clnt := Client{
		C:    make(chan string),
		Name: netAddr,
		Addr: netAddr,
	}
	// 将新连接用户, 添加到在线用户map中
	onlineMap[netAddr] = clnt

	// 创建专门用来给当前用户发送消息的协程
	go WriteMsgToClient(clnt, conn)

	// 发送用户上线消息到全局channel
	message <- "[" + netAddr + "]" + clnt.Name + " login!!!"
}

func WriteMsgToClient(clnt Client, conn net.Conn) {
	// 监听用户消息通道,发送给用户
	for msg := range clnt.C {
		conn.Write([]byte(msg + "\r\n"))
	}
}

func Mannager() {
	// 初始化map
	onlineMap = make(map[string]Client)

	// 循环读通道
	for {
		msg := <-message
		// 将数据发送给全部用户
		for _, clnt := range onlineMap {
			clnt.C <- msg
		}
	}
}

func main() {
	fmt.Println("聊天室启动...")
	// 创建套接字
	listener, err := net.Listen("tcp", "0.0.0.0:8000")
	if err != nil {
		fmt.Println("Listen err: ", err)
		return
	}
	defer listener.Close()

	// 创建管理者协程, 用来管理map和channel
	go Mannager()

	// 循环监听客户端连接请求
	for {
		conn, err := listener.Accept()
		if err != nil {
			fmt.Println("Accept err: ", err)
			break
		}
		// 启动协程处理客户端数据请求
		go HandlerConnect(conn)
	}
}
```

## 3. 广播用户消息
当某个客户端向服务端发送消息后, 服务端应将该消息广播给其它的客户端, 达到聊天室的群聊效果;
开启一个新的协程, 为方便传参, 可以选择匿名协程; 专门负责接收从客户端传递过来的数据, 然后将接收到的数据写到`messaage`通道中;
在实现“广播用户上线”时, 已经完成: `Manager`协程会阻塞读`message`通道, 一旦有数据, 则遍历map中的在线用户; 将数据写到结构体成员的`C`通道中; `WriteMsgToClient`协程会迭代`C`这个`channel`, 最终将数据发送给客户端; 
```go
package main

import (
	"fmt"
	"net"
)

type Client struct {
	C    chan string
	Name string
	Addr string
}

var onlineMap map[string]Client

var message = make(chan string)

func MakeMsg(clnt Client, msg string) (buf string) {
	buf = "[" + netAddr + "]" + clnt.Name + ": " + msg
	return
}

func HandlerConnect(conn net.Conn) {
	defer conn.Close()
	netAddr := conn.RemoteAddr().String()
	clnt := Client{
		C:    make(chan string),
		Name: netAddr,
		Addr: netAddr,
	}
	onlineMap[netAddr] = clnt

	go WriteMsgToClient(clnt, conn)

	// 专门处理用户发送的消息
	go func() {
		buf := make([]byte, 4096)
		for {
			n, err := conn.Read(buf)
			if n == 0 {
				fmt.Printf("检测到客户端:%s退出!!!\n", clnt.Name)
				return
			}
			if err != nil {
				fmt.Println("conn.Read error: ", err)
				return
			}
			// 将读到的消息广播给用户
			message <- MakeMsg(clnt, string(buf[:n]))
		}
	}()

	// 发送用户上线消息到全局channel
	message <- MakeMsg(clnt, "login!!!")
}

func WriteMsgToClient(clnt Client, conn net.Conn) {
	for msg := range clnt.C {
		conn.Write([]byte(msg + "\r\n"))
	}
}

func Mannager() {
	onlineMap = make(map[string]Client)

	for {
		msg := <-message
		for _, clnt := range onlineMap {
			clnt.C <- msg
		}
	}
}

func main() {
	fmt.Println("聊天室启动...")
	listener, err := net.Listen("tcp", "0.0.0.0:8000")
	if err != nil {
		fmt.Println("Listen err: ", err)
		return
	}
	defer listener.Close()

	go Mannager()

	for {
		conn, err := listener.Accept()
		if err != nil {
			fmt.Println("Accept err: ", err)
			break
		}
		go HandlerConnect(conn)
	}
}
```

## 4. 展示在线用户:

```go
package main

import (
	"fmt"
	"net"
)

type Client struct {
	C    chan string
	Name string
	Addr string
}

var onlineMap map[string]Client

var message = make(chan string)

func MakeMsg(clnt Client, msg string) (buf string) {
	buf = "[" + netAddr + "]" + clnt.Name + ": " + msg
	return
}

func HandlerConnect(conn net.Conn) {
	defer conn.Close()
	netAddr := conn.RemoteAddr().String()
	clnt := Client{
		C:    make(chan string),
		Name: netAddr,
		Addr: netAddr,
	}
	onlineMap[netAddr] = clnt
	go WriteMsgToClient(clnt, conn)

	// 专门处理用户发送的消息
	go func() {
		buf := make([]byte, 4096)
		for {
			n, err := conn.Read(buf)
			if n == 0 {
				fmt.Printf("检测到客户端:%s退出!!!\n", clnt.Name)
				return
			}
			if err != nil {
				fmt.Println("conn.Read error: ", err)
				return
			}
			msg := string(buf[:n])
			// 提取在线列表
			if msg == "who" && len(msg) == 3 {
				conn.Write([]byte("online user list: \n"))
				for _, user := range onlineMap {
					userInfo := user.Addr + ":" + user.Name + "\n"
					conn.Write([]byte(userInfo))
				}
			} else {
				// 将读到的消息广播给用户
				message <- MakeMsg(clnt, msg)
			}
		}
	}()

	message <- MakeMsg(clnt, "login!!!")
}

func WriteMsgToClient(clnt Client, conn net.Conn) {
	for msg := range clnt.C {
		conn.Write([]byte(msg + "\r\n"))
	}

}

func Mannager() {
	onlineMap = make(map[string]Client)

	for {
		msg := <-message
		for _, clnt := range onlineMap {
			clnt.C <- msg
		}
	}
}

func main() {
	fmt.Println("聊天室启动...")
	listener, err := net.Listen("tcp", "0.0.0.0:8000")
	if err != nil {
		fmt.Println("Listen err: ", err)
		return
	}
	defer listener.Close()
	go Mannager()
	for {
		conn, err := listener.Accept()
		if err != nil {
			fmt.Println("Accept err: ", err)
			break
		}
		go HandlerConnect(conn)
	}
}
```

## 5. 修改用户名:
当用户成功登录上来可以通过给服务器发送消息, 来修改自己的用户名;
设定, 如果用户发送`rename | Iron man`指令，既是想修改自己的用户名为“Iron man”; 判断用户消息, 是否包含`rename|`关键字: `if len(msg) >= 8 && msg[:6] == "rename" {}`; 如果是, 那么拆分用户意欲修改的用户名保存; 
`strings.Split()`函数可以完成拆分字符串操作;
```go
package main

import (
	"fmt"
	"net"
	"strings"
)

type Client struct {
	C    chan string
	Name string
	Addr string
}

var onlineMap map[string]Client

var message = make(chan string)

func MakeMsg(clnt Client, msg string) (buf string) {
	buf = "[" + netAddr + "]" + clnt.Name + ": " + msg
	return
}

func HandlerConnect(conn net.Conn) {
	defer conn.Close()
	netAddr := conn.RemoteAddr().String()
	clnt := Client{
		C:    make(chan string),
		Name: netAddr,
		Addr: netAddr,
	}
	onlineMap[netAddr] = clnt

	go WriteMsgToClient(clnt, conn)

	go func() {
		buf := make([]byte, 4096)
		for {
			n, err := conn.Read(buf)
			if n == 0 {
				fmt.Printf("检测到客户端:%s退出!!!\n", clnt.Name)
				return
			}
			if err != nil {
				fmt.Println("conn.Read error: ", err)
				return
			}
			msg := string(buf[:n])
			// 提取在线列表
			if msg == "who" && len(msg) == 3 {
				conn.Write([]byte("online user list: \n"))
				for _, user := range onlineMap {
					userInfo := user.Addr + ":" + user.Name + "\n"
					conn.Write([]byte(userInfo))
				}
			} else if len(msg) > 8 && strings.HasPrefix(msg, "reame|") {
				newName := strings.Split(msg, "|")[1]
				clnt.Name = newName
				// 更新到全局map中
				onlineMap[clnt.Addr] = clnt
				// 发送消息
				conn.Write([]byte("rename successful!!"))
			} else {
				message <- MakeMsg(clnt, msg)
			}
		}
	}()
	message <- MakeMsg(clnt, "login!!!")
}

func WriteMsgToClient(clnt Client, conn net.Conn) {
	for msg := range clnt.C {
		conn.Write([]byte(msg + "\r\n"))
	}
}

func Mannager() {
	onlineMap = make(map[string]Client)
	for {
		msg := <-message
		for _, clnt := range onlineMap {
			clnt.C <- msg
		}
	}
}

func main() {
	fmt.Println("聊天室启动...")
	listener, err := net.Listen("tcp", "0.0.0.0:8000")
	if err != nil {
		fmt.Println("Listen err: ", err)
		return
	}
	defer listener.Close()

	go Mannager()

	for {
		conn, err := listener.Accept()
		if err != nil {
			fmt.Println("Accept err: ", err)
			break
		}
		go HandlerConnect(conn)
	}
}
```
## 5. 用户退出与超时处理:
**超时处理:**
如果客户端没有主动退出, 并且长时间没有发送消息, 会一直占用服务端的资源; 服务器通常针对这种用户添加“超时强踢”机制, 强制将该客户端与服务器连接断开;
可以借助并发编程的`select`超时机制来实现; `Select`监听`time.After(60 * time.Second)` 通道上的数据流动; 如果在计时期间一直没有数据, 通道中会被写入当前系统时间, `select` 的`case`满足读条件, 不再阻塞; 但, 有一个问题, 用户如果持续在输入数据, 这个计时器依然在计时, 时间到, 依然会强制踢出用户;
因此, 另外创建一个通道`hasData`来检测用户是否有数据发送, 让`Select`也来监听这个`channel`; 这样, 当用户有数据输入时, `select`监听的这个`hasData`通道会满足`case`条件得以执行, 但不做任何处理; 目的是使得监听在`select`中的计时器被重新计时;


```go
package main

import (
	"fmt"
	"net"
	"strings"
	"time"
)

type Client struct {
	C    chan string
	Name string
	Addr string
}

var onlineMap map[string]Client

var message = make(chan string)

func MakeMsg(clnt Client, msg string) (buf string) {
	buf = "[" + clnt.Addr + "]" + clnt.Name + ": " + msg
	return
}

func HandlerConnect(conn net.Conn) {
	defer conn.Close()
	netAddr := conn.RemoteAddr().String()
	clnt := Client{
		C:    make(chan string),
		Name: netAddr,
		Addr: netAddr,
	}
	onlineMap[netAddr] = clnt

	go WriteMsgToClient(clnt, conn)

	// 创建一个 channel, 用来判断用退出状态
	isQuit := make(chan bool)
	// 创建channel 判断, 用户是否活跃
	hasData := make(chan bool)

	go func() {
		buf := make([]byte, 4096)
		for {
			n, err := conn.Read(buf)
			if n == 0 {
				isQuit <- true
				fmt.Printf("检测到客户端:%s退出!!!\n", clnt.Name)
				return
			}
			if err != nil {
				fmt.Println("conn.Read error: ", err)
				return
			}
			msg := string(buf[:n])
			if strings.HasPrefix(msg, "who") {
				conn.Write([]byte("online user list: \n"))
				for _, user := range onlineMap {
					userInfo := user.Addr + ":" + user.Name + "\n"
					conn.Write([]byte(userInfo))
				}
			} else if strings.HasPrefix(msg, "rename|") {
				newName := strings.Split(msg, "|")[1]
				clnt.Name = newName
				onlineMap[clnt.Addr] = clnt
				conn.Write([]byte("rename successful!!"))
			} else {
				message <- MakeMsg(clnt, msg)
			}
			hasData <- true
		}
	}()
	// 发送用户上线消息到全局channel
	message <- MakeMsg(clnt, "login!!!")
	for {
		select {
		case <-isQuit:
			delete(onlineMap, clnt.Addr)
			message <- MakeMsg(clnt, "logout") // 写入用户退出消息到全局channel
			return
		case <-hasData:
			// 什么都不做。 目的是重置 下面 case 的计时器。
		case <-time.After(time.Second * 60):
			delete(onlineMap, clnt.Addr)                // 将用户从 online移除
			message <- MakeMsg(clnt, "time out leaved") // 写入用户退出消息到全局channel
			return
		}
	}
}

func WriteMsgToClient(clnt Client, conn net.Conn) {
	for msg := range clnt.C {
		conn.Write([]byte(msg + "\r\n"))
	}
}

func Mannager() {
	onlineMap = make(map[string]Client)

	for {
		msg := <-message
		for _, clnt := range onlineMap {
			clnt.C <- msg
		}
	}
}

func main() {
	fmt.Println("聊天室启动...")
	listener, err := net.Listen("tcp", "0.0.0.0:8000")
	if err != nil {
		fmt.Println("Listen err: ", err)
		return
	}
	defer listener.Close()

	go Mannager()

	for {
		conn, err := listener.Accept()
		if err != nil {
			fmt.Println("Accept err: ", err)
			break
		}
		go HandlerConnect(conn)
	}
}
```