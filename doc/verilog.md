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

