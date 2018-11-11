# Hello World

这里使用`iverilog`进行仿真与测试

`sudo apt-get install iverilog`

`sudo apt-get install gtkwave`

下面进行第一个verilog的描述与仿真

```verilog
module hello;
    
    initial 
    begin 
        $display("Hello World\n"); //该函数用法与printf类似，其中16进制输出使用%h而不是%x
    end

endmodule
```

使用`iverilog`进行测试

```verilog
iverilog hello.v -o hello
vvp hello
```

波形图使用`gtkwave`查看，需要使用`$dumpfile`和`$dumpvars`函数生成`.vcd`文件

```verilog
module test;

reg clk;

always #1 clk=~clk;

initial 
begin 
	$dumpfile("signal.vcd");
	$dumpvars();
	clk=0;
	#10;
	$finish;
end 

endmodule
```

```
iverilog test.v -o a.out
./a.out
gtkwave signal.vcd

或者使用另外的格式 -fst适合gtkwave使用，-lxt -lxt2是更先进的格式
vvp a.out -lxt2
gtkwave signal.vcd
```

`gtkwave`会启动一个图形界面，选择模块和指定信号后点击`append`即可查看波形图

*如果信号过多或者仿真时间过长，vcd文件可能会很大，可以控制输出信号的模块与时间，具体的查看其它资料*

# module

一个文件只能有一个模块，它结构是

```verilog
module <module_name>
(
    input wire[31:0] in,
    output reg[4:0]  out,
    ...
);

<module_body>
    
endmodule
```

模块的`input`必须是`wire` ，而`output`可以是`wire`也可以是`reg`

调用模块需要实例化，例如

```verilog
module add
(
	input wire[31:0] rs1,
	input wire[31:0] rs2,
	output wire[31:0] res
);

assign res=rs1+rs2;

endmodule
```

另一个文件调用该模块

```verilog
module test;

reg[31:0] rs1,rs2;
wire[31:0] rd;
    
add addor
(
    .rs1(rs1),
    .rs2(rs2),
    .res(rd)
);
    
initial 
begin 
    rs1=10;
    rs2=20;
    $display("result = %d ",rd);
    $finish();
end
    
endmodule
```

与模块定义相反，实例化模块后，为了连接该模块，它的输入端可以连接`wire`和`reg`，而输出端口只能连`wire`



# 类型

`wire`表示连线，可以看成是电路中的导线

`reg`可以保值，按实际情况可被综合成导线（中间变量可能会这样）或寄存器

`integer`可以声明一个至少32位的有符号整数

`real`用于定义浮点数

`time`可以定义仿真时间，例如`time t=$time;`可以获取当前仿真时刻，`%t`可以进行格式输出

默认情况下 `wire`和`reg`是一位的，如果需要多位需要使用向量

向量定义格式如下`[<hign>:<low>]`，例如

```verilog
wire[31:0] a; //32位的连线，最高位编号31 最低位编号0
reg[31:0] b; //32位的寄存器，最高位编号31 最低位编号0
reg[0:31] c; //32位的寄存器，最高位编号0 最低位编号31

initial
begin 
    b[7:4]=4'b0011; //选择第4,5,6,7位进行赋值 ，最高位是第7位
    c[0:4]=5'b11011;//选择第4,3,2,1,0位进行赋值，最高位是第0位
end
```

数组的定义类似于向量，只是向量的定义`[]`放在`wire`或`reg`后面，而他放在数组名的后面

*这个说法不完全准确，因为有一些默认的表示方法，例如不指明是wire或reg会默认wire，但为了语义上更加清晰，这里不使用一些缺省的语法以减少阅读上的负担*

```verilog
reg[31:0] arr[127:0];

initial arr[0]=1; //数组的用法和c语言类似，注意与verilog向量的用法加以区分
```



# assign

`assign`用于连续赋值，改语句可综合成组合逻辑电路，该语句的赋值对象必须是`wire`

```verilog
wire out;
assign out=in1&in2;
```



# 行为级建模

## initial

`initial`只执行一次，每个`initial`语句均从仿真时刻0开始执行

## always

`always`循环执行，每个`always`语句均从仿真时刻0开始执行，并一直执行

## 串行块

`begin ... end`构成一个串行块，内部的语句按顺序执行。

## 并行块

`fork ... join`构成一个并行块，内部的语句并发执行。

## 阻塞赋值语句

`=` 表示阻塞赋值

```verilog
a=0;
b=1;
c=2;
```

仿真器会按顺序执行

## 非阻塞赋值语句

`<=` 表示非阻塞赋值

```verilog
a<=0;
b<=1;
c<=2;
```

如果该语句在串行块中，那么首先会按顺序计算所有的`<=`右侧的值，在这一仿真时刻结束后再统一赋值给左侧

即他不会因为需要等待将右值赋值给左值而阻塞下一条语句的执行

```verilog
a<= #10 0;
b<= #15 1;
c<= #1  2;
```

首先右侧的值0计算完成后（可以为其他表达式 例如 x+y）需要10个时间单位后再赋值给左侧，如果这里是阻塞赋值，那么需要等待，而非阻塞赋值不阻塞，直接进行下一个右值的计算

*在一个always中，不要同时使用阻塞与非阻塞赋值*

## 事件控制

事件控制使用@表示

```verilog
@(y) x=y; //y一旦改变就立即赋值给x
@(posedge clk) x=y; //clk发生正向跳变时x=y
x= @(posedge clk) y;//立即计算y的值，在clk正向跳变时赋值给x
```

当有多个事件而只要其中一个发生就执行时使用`or`或`,`进行表示 ，例如`@(a or b or c)`

当设计组合逻辑时，可能需要控制的输入过多，可以使用`@*`或`@(*)`表示，他会将其后紧接着的语句块的所有输入加入到敏感列表

```verilog
always@(a,b,c,d,e,f)
begin 
	out1=a?b:c;
	out2=d?e:f;
end
```

等价于

```verilog
always@(*)
begin 
	out1=a?b:c;
	out2=d?e:f;
end
```

## 条件控制

使用`if` ,`else if` ,`else`

## 多路分支

改语句的格式如下

```verilog
case (exp)
	x1: statement1;
	x2: statement2;
	x3: statement3;
	default: default_statement;
endcase
```

`x<...>`的值必须是常量，`statement<...>`可以是一条表达式，也可以是一个语句块

改表达式实际上类似于多路选择器

`casex`表示不关心候选项中的位为x的部分

`casez`表示不关心候选项中的位为z的部位

## 循环语句

循环语句大部分用于仿真，一般不可综合

`while`语句同c语言

`for`语句同ANSI C标准的c语言，如果循环次数确定，那么可以综合

`repeat`语句用于循环指定次数次，例如`repeat(128) x=x+1;`他的输入次数是执行开始时确定的，之后改变不影响，例如`repeat(x) x=x-1;`，如果x初试值为10，那么重复10次，即使x的值之后变化了

`forever`用于表示无限循环

## 命名块

块是可以被命名的

```verilog
begin : BLOCK1
	...
	...
end
```

`disable` 可以禁用某个指定的命名块，例如`#10 disable BLOCK1;`表示10个时间单位后禁用`BLOCK1`块，相当于不执行块内的语句

