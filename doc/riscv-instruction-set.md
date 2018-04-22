关于riscv汇编的总结 

为方便描述 需要一定的c语言基础



# 算术运算指令

## 基本指令

### R-型

```assembly
add  rd,rs1,rs2  rd=rs1+rs2
sub  rd,rs1,rs2  rd=rs1-rs2
sll  rd,rs1,rs2  rd=rs1<<rs2 (logic shift)
srl  rd,rs1,rs2  rd=rs1>>rs2 (logic shift)
sra  rd,rs1,rs2  rd=rs1>>rs2 (arithmetic shift)
and  rd,rs1,rs2  rd=rs1&rs2
or   rd,rs1,rs2  rd=rs1|rs2
xor  rd,rs1,rs2  rd=rs1^rs2
slt  rd,rs1,rs2  rd=rs1<rs2 (signed)
sltu rd,rs1,rs2  rd=rs1<rs2 (usigned)
```



### I-型

```
addi  rd,rs1,imm[11:0]    rd=rs1+imm (imm is signed)
slli  rd,rs1,shamt[4:0]   rd=rs1<<shamt
srli  rd,rs1,shamt[4:0]   rd=rs1>>shamt (logic)
srai  rd,rs1,shamt[4:0]   rd=rs1>>shamt (arithmetic)
andi  rd,rs1,imm[11:0]    rd=rs1&imm (imm will signed extend)
ori   rd,rs1,imm[11:0]    rd=rs1|imm (imm will signed extend)
xori  rd,rs1,imm[11:0]    rd=rs1^imm (imm will signed extend)
slti  rd,rs1,imm[11:0]    rd=rs1<imm (signed)
sltiu rd,rs1,imm[11:0]    rd=rs1<imm (unsigned)

```

以上指令需要注意的是三个逻辑操作`andi ori xori` 他们的12位立即数均符号拓展



### U-型

```assembly
lui   rd,imm[31:12]   rd[31:12]=imm,rd[11:0]=0
auipc rd,imm[31:12]   rd=pc+{imm[31:12],0} 
```

这两条指令一般用于获取地址 一般伪指令会用到 不必过于关心这两条指令



# 内存操作

```assembly
lb  rd,imm[11:0](rs1)   rd=*(int8_t*  )(rs1+imm)
lbu rd,imm[11:0](rs1)   rd=*(uint8_t* )(rs1+imm)
lh  rd,imm[11:0](rs1)   rd=*(int16_t* )(rs1+imm)
lhu rd,imm[11:0](rs1)   rd=*(uint16_t*)(rs1+imm)
lw  rd,imm[11:0](rs1)   rd=*(int32_t* )(rs1+imm)
ld  rd,imm[11:0](rs1)   rd=*(int64_t* )(rs1+imm)   (64-bit only)
sb  rs2,imm[11:0](rs1)  *(int8_t* )(rs1+imm)=rs2
sh  rs2,imm[11:0](rs1)  *(int16_t*)(rs1+imm)=rs2
sw  rs2,imm[11:0](rs1)  *(int32_t*)(rs1+imm)=rs2
sd  rs2,imm[11:0](rs1)  *(int64_t*)(rs1+imm)=rs2   (64-bit only)
```

这些指令用于读取或存储对应地址上的数据 例如

```assembly
li a0,0x00008000
lw a1,0(a0)
li a2,0x0
sw a2,0(a0)
```

`li`指令用于给寄存器加载立即数 这是一条伪指令

如果是64位 保存寄存器的值需要使用`ld` 和`sd`指令



# 控制流

```assembly
beq   rs1,rs2,imm[12:1]  if rs1==rs2 then jump  (+-4KiB)
bne   rs1,rs2,imm[12:1]  if rs1!=rs2 then jump  (+-4KiB)
blt   rs1,rs2,imm[12:1]  if rs1<rs2  then jump (signed) (+-4KiB)
bltu  rs1,rs2,imm[12:1]  if rs1<rs2  then jump (unsigned) (+-4KiB)
bge   rs1,rs2,imm[12:1]  if rs1>=rs2  then jump (signed) (+-4KiB)
bgeu  rs1,rs2,imm[12:1]  if rs1>=rs2  then jump (unsigned) (+-4KiB)
jal   rd,imm[20:1]       rd=pc+4 then pc=target (+-1MiB)
jalr  rd,rs1,imm[11:0]   rd=pc+4 then pc=rs1+imm (any address)
```

跳转指令的地址偏移一般不需要程序员编写 而是使用符号 例如

```assembly
label:
	jal func1
	lw  a1,0(sp)
	lw  a2,4(sp)
	beq a1,a2
	addi a1,4
L1:
	//do someting
	
func1:
	//do someting
	ret
```

`jal`和`jalr`可以用来当做函数调用指令 `jal func1`和`ret`均为伪指令 它们默认使用`ra`寄存器链接

`jalr`还可以实现c语言的`switch`语句



# 寄存器

## 通用寄存器

riscv有32个整数寄存器 分别是`x0`至`x31`

其中 `x0`读取总为`0` 写入总忽略

一般不会直接使用`x`前缀的名称 在编写汇编程序时 使用ABI对应的名字

```
Register   ABI Name        Description                         Saver
x0          zero            Hard-wired zero      
x1          ra              Return address                     Caller
x2          sp              Stack pointer                      Callee
x3          gp              Global pointer 
x4          tp              Thread pointer 
x5          t0              Temporary/alternate link register  Caller
x6-7        t1-2            Temporaries                        Caller
x8          s0/fp           Saved register/frame pointer       Callee
x9          s1              Saved register                     Callee
x10-11      a0-1            Function arguments/return values   Caller
x12-17      a2-7            Function arguments                 Caller
x18-27      s2-11           Saved registers                    Callee
x28-31      t3-6            Temporaries                        Caller
```

`Caller`表示调用者

`Callee`表示被调用者

他们描述了这些寄存器需要由调用者保存还是被调用者保存

