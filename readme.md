这是一篇关于c语言的教程 大致描述c的常用语法和一些技巧 为了保持精简 我会提供一些搜索的关键词 以保证可以搜索到其他没有讲解的问题 

由于c语言标准有几个 包括但不限于c89 c99 c11 默认情况下 这里讲解c99标准 而c11与c99语法上大致相同 主要注意c89与c99的区别 具体的不同会被指出

另外 为了打字方便 这里统一使用空格分隔句子 不会出现任何标点符号

# 快速入门



快速入门会提到大部分常用的语法 但细节之处会略去 如果发现了一些写法错误或者发生奇怪的现象 不要惊讶 后面会给出详细的细节



### 编译器 编辑器 集成开发环境

这是编写程序的必要准备 编写程序的过程中 我们需要编辑器 其中 你可以选择一款自己喜欢的编辑器 比如 Sublime 或者NotePad++ 以及其他的编辑器

编写好的程序 我们需要编译器将代码翻译成机器语言 这里 只使用 gcc 

由于gcc是linux的编译器 linux系统一般自带gcc 而windows上运行gcc 可以下载TDM64 

或者Mingw-w64 搜索之后下载即可

集成开发环境[IDE] 是既可以编辑也可以编译的软件 比如 Codeblocks ,Visual Studio ,Eclipse

由于是c语言的编写 推荐codeblocks 



由于集成开发环境直接使用比较简单 这里不介绍如何使用 只讲如何用gcc编译编写的代码

linux下直接打开bash 输入

`gcc test.c `

这样会产生一个a.out的可执行文件 运行只需输入

`./a.out`

windows下的cmd并不好用 而cygwin过于庞大 可以使用较小的 git bash for windows 本身是给git使用的 不过编译c语言绰绰有余

打开git bash后 输入和linux下相同 这是生成的文件是a.exe 因为windows下 可执行文件的后缀必须是.exe 而linux并无限制 运行方式同linux下相同

`./a.exe`

如果想指定生成的可执行文件的名字 可以输入

`gcc test.c -o yourname.exe`

通过-o指定生成的名字

 关于gcc的使用方式 可以参考相关博客 这里有一个[常用的gcc编译选项整理](https://github.com/YohnWang/documents/blob/master/gcc%E5%B8%B8%E7%94%A8%E7%BC%96%E8%AF%91%E9%80%89%E9%A1%B9.pdf)



### c程序的基本结构

一个最小的c程序 是这样的

```c
int main()
{

}
```

main函数是一个程序的入口 int是main函数的返回值 该返回值是返回给操作系统的 一般返回0表示程序正常运行 

这个程序什么都干不了 我们让程序输出一段话 显示在终端上 

```c
#include<stdio.h>
int main()
{
    printf("Hello World\n");
}
```

`#include<stdio.h>` 表示包含一个头文件 其中 std是standard的缩写 表示标准 io表示输入输出

包含该头文件是因为使用了printf用于输出`Hello World\n`

其中" "括起来的部分被称为字符串 \n表示换行

注意 c语言的一条语句需要用`;`结尾



### 基本的数据表示

一个程序至少要能计算 而计算就需要数 c语言提供了一些数据类型用于表示数据

char 用于表示字符

int    用于表示整数 [注意是有范围的 现代的PC机上大致是-20亿到+20亿之间 具体的范围后序讲解]

double 用于表示浮点数[并不是小数 是一种对小数的近似的表示 并不精确 注意整数是精确的]

有了数据后 我们便可以进行一些数值计算了 

```c
#include<stdio.h>
int main()
{
    int a=10,b=20;
    printf("a+b=%d\n",a+b);
}
```

`int a=10,b=20;`表示定义两个int型的变量 名字为a和b  并分别初始化为10和20

最后输出a+b的值 `%d`表示输出的是int类型 

浮点数的使用 与int类型相似 只是输出的时候用`%f`代替`%d`

注意 二者不能相互代替 是什么类型就要对应相应的符号

字符类型需要使用`%c`表示 

`char c='A';` 我们要输出c 则`printf("%c",c);`这样终端上会显示一个A

' '用于将相应的字符转码 注意 ' '之间只能存放一个字符 多个字符需要使用字符串 后序讲解

### 循环

暴力求解是计算机最擅长做的事情 他的计算速度极快 例如 我们考虑一下高斯小学遇到的问题

1+2+3+...+100=？

利用for循环可以很容地编写这个式子

```c
#include<stdio.h>
int main()
{
    int sum=0;
    for(int i=1;i<=100;i++)
        sum=sum+i;
    printf("sum=%d\n",sum);
}
```

定义一个sum用于保存求和结果 for循环的形式是

`for(exp1;exp2;exp3) `

进入for循环首先执行exp1 之后将不再执行 然后执行exp2 此处一般放置条件判断 如果条件成立 则执行for循环下面的语句 执行完成后 会执行exp3 此处i++表示对i自增1 相当于`i=i+1` 之后再执行exp2以确定是否继续执行循环

注意`sum=sum+i`此处的`=`是赋值 注意与初始化区别开来 只有在定义的时候的`=`是初始化 初始化会与赋值有一些区别 具体细节后序描述

另外 `=`表示的是赋值 而不是数学中的相等 相当于把`=`右侧的计算结果赋值给左侧 

所以不能写成`sum+i=sum` 这样会编译错误 

能写在`=`左侧的值叫做左值 



另一个循环表达式是`while`

```c
#include<stdio.h>
int main()
{
    int sum=0;
    int now=1;
    while(now<=100)
    {
        sum+=now;
        now++;
    }
    printf("sum=%d\n",sum);
}
```

他比for的形式更为简单 `while(exp)`  

只要exp的求值结果不为0 就会一直执行下面的语句块

用`{}`括起来的叫做语句块 这里有两条语句[有几个分号就有几条语句] 所以必须使用`{}`

否则 只能像上例的for 后面只能接一条语句[即一个`;`]



下面看一个用`while`循环计算最大公约数的例子

由欧几里得辗转相除法公式 

函数 gcd(a,b)可以计算a和b的最大公约数

其中 gcd(a,b)=gcd(b,a%b) 并且gcd(a,0)=a  [其中`%`在c语言中表示求模 即取余数 如 10%3=1]

由以上公式和性质 我们可以把问题迭代至最后一个公式以化简问题的复杂度

```c
#include<stdio.h>
#include<stdlib.h>

int main(int argc,char *argv[])
{
    int a=15,b=10;
    while(b!=0)
    {
        int c=a%b;
        a=b;
        b=c;
    }
    printf("%d",a);
}
```

在求完`a%b`之后 需要把原来的`a`设置成`b`  再把`b` 设置成`a%b`

其中 `!=` 代表不等于 如果不相等成立 则会返回一个非0值 表示循环可以继续 [`!`和`=`必须紧挨在一起 是一个整体]

如果要表示相等 则用`==` 也是紧挨在一起的 [一个`=`表示赋值或初始化 它会修改位于它左侧变量的值]





### 条件判断

实际情况中 往往有一些在某一条件成立下 才执行的动作 比如求一个数的绝对值

如果这个数大于0 则不作任何动作 而小于0 需要求他的相反数

```c
#include<stdio.h>
int main()
{
    int a=-10;
    if(a<0)
        a=-a;
    printf("%d\n",a);
}
```

考虑一个更复杂的问题 一些学校往往会根据成绩划分等地 那么该怎么做呢

```c
#include<stdio.h>
int main()
{
    int score=89;
    if(score<60)
        printf("failed\n");
    else if(score<70)
        printf("pass\n");
    else if(score<80)
        printf("mean\n");
    else if(score<90)
        printf("nice\n");
    else
        printf("excellent\n");
}
```

通过`if` `else if` `else` 可以组合成一系列的判断 其中一个成立 那么就不再执行剩余的 注意 执行顺序是从上至下的 如果条件比较简单`else if`不是必须的



### 自定义函数

c语言允许自定义的函数 可以很方便的调用 例如 输出调用`printf`一样

一个函数的结构是这样的 

```
返回类型 自定义函数名(参数列表)
{
    执行相关动作  
}
```



看一个具体的例子 这个函数计算(x+y)*(x-y)

```c
int fun(int x,int y)
{
    return (x+y)*(x-y);
}
```

函数定义之后 如何调用他呢

```c
#include<stdio.h>

int fun(int x,int y)
{
    return (x+y)*(x-y);
}

int main()
{
    int a=20,b=10;
    int result=fun(a,b);
    printf("%d",result);
}
```

注意参数一一对应

有一个值得注意的地方是 **c语言的函数只有值传递 即换入函数的参数都是一份副本 对副本的改变不会影响到原来的变量**

另外 c语言的函数比数学的函数功能更多一点 他不仅可以返回一个值 还可以执行一些动作 但这些需要其他的一些东西 



### 指针

c语言的指针是一种复合类型 这种类型的变量本身也保存一个值 他保存的值是一个内存的地址

内存的编址一般以8个bit为一个单位 称为一个字节 

每一个字节都有一个唯一的地址 就像班级的每一个学生都有一个唯一的学号

指针的形式如下

```c
int a=20,b=10;
int *ptr1=&a,*ptr2=&b;
```

`int *`表示一个指向int类型的指针 这里注意ptr2前面也有`*`这是因为指针是复合类型 所以必须再写一次 a之前的&表示获取a的首地址 

`ptr1`与`ptr2`存储的是`a`和`b`的地址 那么如何获取到他们存储的值呢

```c
#include<stdio.h>

int main()
{
    int a=20,b=10;
    int *ptr1=&a,*ptr2=&b;
    printf("a=%d b=%d",*ptr1,*ptr2);
}
```

只需在指针变量名字之前加上`*` 即可获取到他们的

是访问指针指向的内容之前 指针必须初始化为或被赋予合法的地址 合法的地址即程序可用的地址 包括变量的地址 申请的内存的地址 合法地址之外的地址的访问 往往会导致程序出错或崩溃

由于变量存储的位置是不变的 即地址不变 那么只需传递给函数他们的地址 就能在另一个函数中修改调用者的变量 

例如 现在要交换a和b的值

```c
#include<stdio.h>

void swap(int *a,int *b)
{
    int tmp=*a;
    *a=*b;
    *b=tmp;
}

int main()
{
    int a=20,b=10;
    printf("%d %d\n",a,b);
    swap(&a,&b);
    printf("%d %d\n",a,b);  
}
```

`void`表示函数不返回任何值

这里主函数也没有显式地返回任何值 但是主函数比较特殊 不标明返回何值 则默认返回0

这是其他函数所没有的

一定要注意的是 **指针必须指向类型一致的对象** `int*`的指针对象必须指向`int`  否则 不保证程序正确性



### 数组

有时候 需要使用大量的同一类型的变量 不可能一个一个地定义 这时候就可以使用数组

数组表示一组同一类型的变量  形式如下

`int arr[1024];`这样 就定义了一个长度为1024的`int`类型数组

数组的下标从`0`开始 所以不能使用`a[1024]` 这样就越界了 即访问了不合法的地址空间

假设现在有一组数据 需要输入 之后按递增顺序输出

```c
#include<stdio.h>

void swap(int *a,int *b)
{
    int tmp=*a;
    *a=*b;
    *b=tmp;
}

int find_min(int a[],int begin,int end)
{
    int min=begin;
    for(int i=begin+1;i<=end;i++)
    {
        if(a[min]>a[i])
            min=i;
    }
    return min;
}

void select_sort(int a[],int n)
{
    for(int i=0;i<n;i++)
    {
        int index=find_min(a,i,n-1);
        swap(&a[i],&a[index]);
    }
}

int main()
{
    int a[1024];
    int n;
    scanf("%d",&n);
    for(int i=0;i<n;i++)
        scanf("%d",&a[i]);
    select_sort(a,n);
    for(int i=0;i<n;i++)
        printf("%d ",a[i]);
}
```

这是一个简单的选择排序 每一次都找到未排序部分的最小值 然后把他放在第一个

`scanf`表示输入 形式上与`printf`类似 稍微有一点区别 %d表示输入的是int类型的数

注意 由于传入函数的参数都是副本 所以只能传入地址来修改本地的值 所以`scanf`里的参数需要`&`



c语言没有内置的字符串类型 所以只能通过字符数组表示

```c
#include<stdio.h>

int main()
{
    char str[1024]="this is a string\n";
    printf("%s",str);
}
```

字符数组的空间有限 如果字符个数超出字符数组长度再减去1 那么会发生错误

字符数组使用`'\0'`表示字符串结束  所以存储空间会占用掉一个

`"this is a string\n"`可以被称为字符串 不过他是只读的 不可修改 同样 他也是以`'\0'`结束



```c
int main()
{
    char str[1024];
    str="this is a string\n";
    printf("%s",str);
}
```

str是一个字符数组 但是由于初始化和赋值的区别 上述代码是**错误**的 



### 结构体

数组表示了一类相同类型的集合 实际情况中 往往还有一些不同类型的信息集合 

例如 一个学生的信息 一般有 名字 学号 成绩

这类由不同数据类型结合起来的集合可以由结构体表示

```c
#include<stdio.h>

struct student
{
    char name[20];
    int id;
    int score;
};

int main()
{
    struct student John={"John",17,85};
    struct student Lee={.id=10,.name="Lee",.score=89};
    printf("%s id:%d\n",John.name,John.id);
    printf("%s id:%d\n",Lee.name,Lee.id);
}
```

`struct`关键字表示结构体 用于定义一个结构体 注意 结尾有个`;`

在定义完类型后 可以使用`struct student`定义一个变量 

上例中分别使用了两种初始化 第一种需要一一对应 第二种可以指定初始化

注意 只有初始化才能写成如上两种形式 

赋值时不可使用 但可以这么写

`Jack=(struct student){"Jack",9,84};`

`=`右边的部分为一个`struct student`类型的临时变量 将他赋值给Jack

`struct`关键字必须出现在 `student`之前 不可省略 

但可以使用`typedef`来定义一个别名

`typedef struct student stu;`

这样 之后可以用`stu`来定义 

例如`stu Pikachu`

在访问结构体内部的变量是 使用`.`来指定具体的内容

