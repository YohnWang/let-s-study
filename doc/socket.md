# socket

socket用于建立连接，并且将使用类似于文件系统的方式进行IO通信，完成socket创建与连接成功之后，可以通过文件IO的方式进行读写



# 一个简单的客户端程序

客户端用于向服务端发送请求，建立客户端的步骤为

1. 创建socket
2. 建立连接
3. 读写
4. 关闭

## 创建socket

```c
int socket_file_id=socket(AF_INET,SOCK_STREAM,0);
```

该步骤用于创建一个socket，与文件操作函数`open`一样，返回一个socket文件描述符

```c
#include<sys/socket.h>
int socket(int domain,int type,int protocol);
```

`domain`参数用于指定地址族，主要包括四个：

1. AF_INET，IPv4因特网域
2. AF_INET6，IPv6因特网域
3. AF_UNIX，UNIX域
4. AF_UPSPEC，未指定

*其中AF是address family的缩写*

`type`参数用于指定套接字类型，包括：

1. SOCK_DGRAM，固定长度、无连接、不可靠的报文传递
2. SOCK_RAM，IP协议的数据报接口
3. SOCK_SEQPACKET，固定长度、有序、可靠、面向连接的报文传递
4. SOCK_STREAM，有序、可靠、双向、面向连接的字节流

`protocol`表示协议，一般为0，表示默认

## 建立连接

```c
int ret=connect(socket_file_id,(void*)&socket_addr,sizeof(socket_addr));
```

该步骤用于连接**指定地址与端口号**的进程，在完成连接之前，该步骤进入阻塞

首先会连接指定的ip地址，连接成功后，会寻找指定的端口号，如果没有找到该端口号，则连接失败，该函数会返回小于0的值，并且设置全局变量`errno`为111

```c
#include<sys/socket.h>
int connect(int sockfd,const struct sockaddr *addr,socklen_t len);
```

`sockfd`为socket的文件描述符，`len`为addr的字节数，一般该参数均为`sizeof(addr)`

这里重点的部分是第二个参数，用于表示地址格式

### 地址格式

由于地址格式与特定的通信域（地址族）相关，因此不同的通信域有不同的地址格式，为了能够统一不同的地址格式，所有的地址格式都会被转换成`struct sockaddr`

> 这么做的实际原因主要是c的类型系统过于简陋所致，而特地使用`struct sockaddr*`而不是`void*`这个通用指针的原因是socket函数在ANSI C之前就被定义。
>
> 这种写法有点类似于面向对象的思路

由于上述原因，我们不关心这个结构具体的定义

#### IPv4的地址结构

```c
#include<netinet/in.h>
struct sockaddr_in
{
    uint8_t        sin_len;    //该结构体长度，非POSIX规定
    sa_family_t    sin_family; //应当设置为AF_INET，用于表示该结构体为IPv4的地址结构
    in_port_t      sin_port;   //16 bit的端口号，网络字节序
    struct in_addr sin_addr;   //32 bit的IPv4地址，网络字节序
    char           sin_zero[8];//填充，应当初始化为0，非POSIX规定
};

struct in_addr
{
    in_addr_t s_addr;
};

typedef unsigned short sa_family_t;
typedef uint32_t in_addr_t;
typedef uint16_t in_port_t; 
```

由于该结构存在一些非POSIX规定的字段，因此在使用前最好对其进行初始化，以防止发生意外

#### 字节序函数

```c
#include<netinet/in.h>
uint16_t htons(uint16_t host16bitvalue);
uint32_t htonl(uint32_t host32bitvalue);
uint16_t ntohs(uint16_t net16bitvalue);
uint32_t ntohl(uint32_t net32bitvalue);
```

h代表host，n代表net，s代表short，l代表long

由于该函数出现较早，s和l应视为16位和32位，因为有其他的数据模型可能这两个类型不是这些比特数

#### 地址格式转换函数

```c
#include<arpa/inet.h>
int inet_pton(int family,const char *str,void *ip_addr); //返回值 -1 出错，0 格式错误，1成功
const char* inet_ntop(int family,const void *ip_addr,char *str,size_t len);//返回值 NULL 失败
```

其中p代表presentation，n代表numeric

`family`代表地址族，`str`代表十进制点分式字符串，`ip_addr`代表网络字节序的ip地址，`len`代表字符数组长度

