# 类型与对象

## 基本概念

python的对象类型不需要声明，python的对象一律使用引用来标记

```python
x=10
s="string"
```

python的每一个对象有一个id，使用`id()`可以获取对象的id

`is`关键字可以用于判断两个对象的id是否相等

`==`用于判断两个对象的内容是否相等，这与`is`是不同的

```python
a=10
b=20
c=b
d=20
print(a is b)
print(c is b)
print(d is b)
print(a==b)
print(b==c)
print(d==b)
```

## 变量名

python的变量名仅仅是一个名字，名字可以引用任意类型的对象

```python
a=10
print(a)
a="string"
print(a)
a=[1,2,3]
print(a)
```

`del()`可以解除变量名与对象的引用

## 对象的可变性

python有六种标准的数据类型 分别是

1. 数字
2. 字符串
3. 元组
4. 列表
5. 集合
6. 字典

其中1-3类型的对象均不可变，4-6可修改

数字包含integer、float、bool、complex

```python
x=1 #整数的精度是任意的
y=6.25
a,b=True,False
c=10+6j
```





## 对象的类型

可以通过`type()`查询对象的类型



## 对象的构造与销毁

对象在指定时创建

销毁对象由GC自动完成，程序员无法主动销毁



# 控制结构

## 顺序结构

按照程序编写的顺序自上而下执行

## 条件分支

if语句可以实现条件分支，可以使用的关键字是`if`,`elif`,`else`

```python
x=10
if x<10:
	print("condition 1")
elif x>100:
	print("condition 2")
else :
	print("condition 3")
```

注意，**`:`是不可或缺的**

## 循环

### while

python中的循环使用`while`实现

```python
i=0
while i<10:
	print(i)
```

同样的，python也支持`break`与`continue`



### for

python中的`for`用于迭代，它通过迭代器进行遍历

```python
x=[1,2,3]
for i in x :
	print(i)
```

### 迭代器

可以通过`iter()`创建一个迭代器对象，该函数接受的对象必须是可迭代的

只要实现了`__iter__()`和`__next__()`的对象就是一个可迭代对象

`__iter__()`返回迭代器本身，`__next__()`返回下一个容器的下一个元素，直到结尾抛出`StopIteration`异常 

```python
class T :
	i=0
	n=0
	def __init__(self,n):
		self.n=n
	def __iter__(self):
		return self
	def __next__(self):
		if self.i<self.n :
			x=self.i 
			self.i=self.i+1
			return x
		else :
			raise StopIteration

t=T(10)
for i in t :
	print(i)
```



# 运算符

python的运算符基本与c相似

特别的有`//`表示整除，`/`将永远返回浮点数

`**`可以计算幂

逻辑运算符使用`and`、`or`、`not`

`in`与`not in`可以测试成员是否存在与容器中 

```python
l=[1,2,3,4,5]
print(1 in l)
print(10 in l)
```

`is`与`is not`用于测试对象的地址是否相同，即是否是同一个对象

`id()`可以得到对象的地址，`a is b`实际上等价于`id(a)==id(b)`

### 运算符优先级

//TODO

# 函数

函数使用`def`定义

```python
def function_name (argument):
    do something
```

## 参数传递

python不能指定参数的类型，并且所有的传递都是类似于c语言通过指针传递参数

python中传递的参数是否可修改需要看对象是否可修改

```python
def fun1 (x):
	x=20

def fun2 (y):
	y[0]=1

a=10
l=[0]
fun1(a)
fun2(l)
print(a,l)
```

类似于c语言的指针，将x赋值20事实上是创建了一个值为20的整数对象，然后将x指向这个新的对象

而修改y[0]是将y[0]指向了值为1的对象，因而改变了y指向的对象

```c
void fun1(int *x)
{
    int *p=malloc(sizeof(*p));
    *p=20;
    x=p
}
struct T
{
    int y;
};
void fun2(struct T *t)
{
    t->y=1;
}
```



## 参数默认值

python的函数可以指定参数默认指向的对象

```python
def fun(a,b=1,c=2): pass
```

在指定了默认参数后，后续的参数也必须有默认的参数

值得注意的是，默认参数所指向的对象是同一个对象，这在默认对象是可变对象时需要注意

```python
def f(a=0,l=[]):
	l.append(a)
	return l
f(1)
f(2)
l=f(3)
print(l)
```

这段代码最终l为`[1,2,3]`

解决这一问题的方案是在函数内部重新指向一个新的对象

```python
def f(a,l=None):
	l=[]
	l.append(a)
	return l
```

默认参数的原理是将函数参数指向一个对象，因此也可以这么写

```python
i=10
def f(x=i):
	print(x)
i=0
```

这个函数的x默认指向值为10的整数对象



## 不支持重载的原因

重载需要解决的问题是函数功能相同，但参数的类型可能不同，或者参数的个数不确定

对于类型问题，python并不能指定参数的类型，也就是可以传递任意类型的参数

而参数个数问题可以通过缺省参数实现



## 关键字参数

python可以指定参数名调用函数，这与c语言的结构体初始化类似

```python
def f(age,name):pass
f(name="John",age=22)
```

指定参数名后可以不依赖参数的顺序

## 不定长参数

```python
def f(x,*args):
	print(x)
    for i in args:
        print(i)
```

通过在变量名前添加`*`实现不定长参数，参数以元组的方式导入

如果不定长的参数已经存在于元组或列表中，那么需要解压缩参数

解压缩参数在视觉上相当于去掉`[]`或`()`，因此也能用在其他地方 **//待考证**

```python
l=[1,2,3,4,5]
f(10,*l)
```

对于字典，需要使用`**`解压缩

//TODO



## lambda表达式

python的匿名函数功能用于返回一个小的函数对象

```python
sum = lambda a,b : a+b
print(sum(1,2))
```

## 文档字符串



## 函数注解

```python
def f()->"a string" :
	print("Annotations:", f.__annotations__)
```





# 类

将类的实例称为对象，类本身也是对象

## 成员

`python`的所有成员都是`public`的

### 实例属性

python的成员可在任意时刻创建，例如

```python
class T:
	def __init__(self,name="",age=0):
		self.name=name
		self.age=age

t=T("John",22)
```

`__init__()`是构造函数，它的第一个参数表示类的实例自身，相当于c++的this指针，在调用时不需要传入

`self.name`直接创建了一个名为`name`的属性，该属性是每个实例对象独有的

甚至可以在类对象创建之后进行绑定，例如

```python
x = T()
x.othername=0
```

类T在定义时内部的函数并没有`othername`这一成员，但仍然可以在任意时刻绑定指定的对象

不过，在没有引用任何对象之前，第一次出现的成员名会导致错误



## 方法

`python`的所有成员函数都是`virtual`的

`python`中类的方法的第一个参数会传入自身的引用，在调用时会被忽略

```python
class MyClass :
	"""a doc attribute""" # __doc__ 文档字符串
	i=12345  #所有类的实例共享
    def __init__(self,name="a class"):  #构造函数
    	self.name=name  
    def f(self):
        print(self.name)
```



## 访问控制

## 类的运算符重载与一些内置方法

类可以通过编写某些特定名称的函数来重载运算符，例如重载加法

```python
class T :
    def __init__(self,re=0,im=0):
        self.re=re
        self.im=im 
    def __add__(self,rhs):
        return T(self.re+rhs.re,self.im+rhs.im)
    def __str__(self):
        return str(self.re)+"+"+str(self.im)+"i"

a=T(1,2)
b=T(3,4)
print(a+b)
```

`__add__()`方法用于重载`+`，并且`+`左边的类型必须是`T`，右侧可以任意，如果想右侧的类型是`T`，而左侧任意，则需要重写`__radd__()`，另外还有复合赋值运算符如`+=`，对应的函数名是`__iadd__()`.

```python
__add__(self,rhs)        self + rhs         加法
__sub__(self,rhs)        self - rhs         减法
__mul__(self,rhs)        self * rhs         乘法
__truediv__(self,rhs)    self / rhs         除法
__floordiv__(self,rhs)   self //rhs         取整除
__mod__(self,rhs)        self % rhs         取模(求余)
__pow__(self,rhs)        self **rhs         幂运算

__lt__(self,rhs)         self < rhs         小于
__le__(self,rhs)         self <= rhs        小于等于
__gt__(self,rhs)         self > rhs         大于
__ge__(self,rhs)         self >= rhs        大于等于
__eq__(self,rhs)         self == rhs        等于
__ne__(self,rhs)         self != rhs        不等于

__and__(self,rhs)        self & rhs         位与
__or__(self,rhs)         self | rhs         位或
__xor__(self,rhs)        self ^ rhs         位异或
__lshift__(self,rhs)     self <<rhs         左移
__rshift__(self,rhs)     self >>rhs         右移

__neg__(self)            -self              负号
__pos__(self)            +self              正号
__invert__(self)         ~self              取反

__getitem__(self,i)      x=self[i]          索引/切片取值
__setitem__(self,i,v)    self[i]=v          索引/切片赋值
__delitem__(self,i)      del self[i]        del语句删除索引/切片

```

`__str__`方法用于返回对象的字符描述，一般`print`方法中会用到，已到达输出的目的



## 继承

## 迭代器与生成器

`__iter__`与`__next__`用于迭代器，例如设计一个简单的自增1的`range`，命名为`onerange`

```python
class onerange:
    def __init__(self,begin=0,end=0):
        self.begin=begin 
        self.end=end
    def __iter__(self):
        return self 
    def __next__(self):
        if self.begin < self.end:
            r=self.begin
            self.begin+=1
            return r 
        else :
            raise StopIteration
```

另一种方法是使用生成器，生成器可以快速地创建一个迭代器

```python
def nrange(begin,end,step=1):
    while begin < end :
        yield begin 
        begin+=step 
```

`yield`与`return`类似，会返回值，只是如果下次继续调用该函数时，不是重新执行，而是会从`yield`之后的语句再开始执行

### 生成器表达式

与列表表达式类似，只是将`[]`替换为`()`，相对于列表推导式，生成器表达式可节省内存

```python
l=(i*i for i in range(0,10))
print(l)
for i in l:
    print(i)
```



# 模块 

模块是包括 Python 定义和声明的文件。文件名就是模块名加上 `.py` 后缀。模块的模块名（做为一个字符串）可以由全局变量 `__name__` 得到（如果通过`py run.py`执行，那么`__name__`会被设置成`"__main__"`）。 

## import

`import`可以引入模块名，例如在`example.py`文件中有一个`add()`函数，则在另一个`py`文件中导入的方式如下

```python
import example
print(example.add(10,20))
```

`import`可以写在任意位置，只要在使用前声明即可



## from ... import ...

该语句可以直接导入另一个模块的名字

```python
from example import add
print(add(10,20))
```



## 模块中的可执行语句

模块中的可执行语句一般用来初始化模块，在`import`该模块时会开始执行



## 搜索路径

解释器先在当前目录中搜索 ，如果没有找到的话，接着会到 `sys.path`变量中给出的目录列表中查找 



## dir()

内置函数 `dir()` 用于按模块名搜索模块定义，它返回一个字符串类型的存储列表 



## 包

```python
import dir
from dir import dir.module
```



//TODO



# 内置数据结构

## 列表

列表使用`[]`表示

```python
l=[1,2,3]
l.append(4) #向列表对象末尾添加一个元素
sub=l[1:2]  #返回一个新的子列表 范围为左闭右开
sub2=l[:2]  #左下标默认0
sub3=l[2:]  #右下标默认len(l)
sub4=l[:]   #左右下标均默认，相当于一份l的浅拷贝
x=sub[-1]   #负数表示逆序顺序
del l[0]    #解除列表对象下标为0的元素的引用
ex=l+[1,2,3]#生成一个拼接后的列表
l+=[4,5]    #末尾添加
l.pop()     #解除末尾绑定，可指定下标
l[0:2]=[-1,-2]#区间替换
```

列表推导式

```python
l=[x**2 for x in range(1,10)]
```



## 元组

`()` 

元组不可变

```python
empty=()
single=123, #单个元素的元组后面跟一个','
double=1,2
triple=(1,2,3)
```



## 集合

`set()`

`set`支持并、交、差、对称差集运算，分别使用`| & - ^`



## 字典

`{key:value}` `dict()`

字典由键：值组成，键必须是不可变对象

```python
tab={1:1,2:4,3:9}
print(tab[2])
tab[4]=16

x={x:x**2 for x in range(1,10)}

stu={"john":1,"tony":32,"jack":39,"tom":18,"peter":9}
print("john" in stu)
stu["abby"]=29 #向字典添加一个键值对
del std["jack"]#删除"jack"

xstu=stu.items() #返回一个(key,value)的列表(该列表是dict_items类型的，不是内置列表)
```



## 字符串

字符串可以使用`" "`或`''`表示

`r"\n"`可以消除转义

`""" ... """`可以实现多行字符串，如果想消除换行可以在行尾添加一个`\`

```python
s="this is "+"a string" #拼接字符串


```

字符串是不可变对象

切片操作参考列表



# 输入输出





# 结合c语言



# 异常处理

