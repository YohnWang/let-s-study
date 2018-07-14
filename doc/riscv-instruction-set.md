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
sltu rd,rs1,rs2  rd=rs1<rs2 (unsigned)
```



### I-型

```assembly
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



## 控制与状态寄存器（CSR）

CSR有专门的寄存器读写指令 均为`csr`开头

CSR有四种模式 分别为M S H U

这里暂时只列出机器级 即M

### mcpuid

cpu的id寄存器 用于存放cpuid



### mstatus

状态寄存器 该寄存器有中断使能位 

[4:0]分别为[MIE,WPRI,SIE,UIE] 分别控制机器级中断 管理员级中断 用户级中断

WPRI位可以忽略 

其他信息查看riscv的特权级手册



### mtvec

机器自陷向量基址寄存器 用于存放自陷向量地址

例如 

```
_start:
	la t0,trap_vector
	csrw mtvec,t0
	j main
	
trap_vector:
	...
```

mtvec可以实现为硬件固定的只读值，也可以实现为可写入的。

mtvec的最后两位用于模式位，称为MODE，其余高位称为为BASE字段

如果MODE==0，那么所有的异常会将pc设置为BASE

如果MODE==1，那么异步的中断会将pc设置为BASE+4*cause

如果MODE>=2，保留





### mie与mip

mie为机器中断使能 mip在中断发生时保存mie 在中断返回时返还给mie

第7位为机器定时器中断使能位 

软中断由msip寄存器控制，该寄存器是内存映射的，写入1会触发软中断，清除可以解除软中断

注意msip寄存器与MSIP位是不同的



### mepc

该寄存器用于保存机器发生自陷时的指令地址 例如

```
do_something:
	...
	scall
	...
	
trap_vector:
	...
	csrr t0,mepc
	addi t0,t0,4
	csrw mepc,t0
	mret
```

scall(目前已被重命名为ecall)会引发一个自陷 mepc保存该条指令的地址 处理完中断后 要继续执行需要返回至下一条指令的地址

另外 如何区分自陷的类型 需要使用mcause寄存器

当发生中断时 mepc会保存下一条指令的地址 而发生异常时 mepc保存当前指令地址

中断位有mcause的最高位表示



如果是中断发生，那么mepc会保存下一条指令的值

如果是异常发生，那么mepc会保存当前指令的值



因为ecall是异常，所以调用ecall后要继续执行下一条指令需要将mepc加4



### mcause

mcause反映了当前trap的类型 当机器自陷时 需要使用该寄存器的值来处理相应的trap



### mtval

mtval会保存出现异常的指令或地址



### mtime与mtimecmp

mtime与mtimecmp都是内存映射的，并且固定为64位。

mtime会以恒定的频率自增，当mtime大于等于mtimecmp时会触发中断。

mtime也可以被写入。



# 系统指令

```
scall 
sbreak
csrrw  rd,csr,rs1      rd=csr,csr=rs1
csrrc  rd,csr,rs1      rd=csr,then clear the bits of csr by rs1's bits(1 whill be clear)
csrrs  rd,csr,rs1      rd=csr,csr|=rs1
csrrwi rd,csr,imm[4:0]
csrrci rd,csr,imm[4:0]
csrrsi rd,csr,imm[4:0]
```



# 伪指令

```
la    rd,symbol          rd=symbol
l{b,h,w,d} rd,symbol     rd=(type)symbol
s{b,h,w,d} rd,symbol,rt  *symbol=rd (rt assiant)

nop 
li   rd,immediate    rd=immediate
mv   rd,rs           rd=rs
not  rd,rs           rd=~rs
neg  rd,rs           rd=-rs

seqz rd,rs         if rs==0 then rd=1
snez rd,rs         if rs!=0 then rd=1
sltz rd,rs         if rs<0  then rd=1
sgtz rd,rs         if rs>0  then rd=1

beqz rs,offset     if rs==0 then jump
bnez rs,offset     if rs!=0 then jump
blez rs,offset     if rs<=0 then jump
bgez rs,offset     if rs>=0 then jump
bltz rs,offset     if rs<0  then jump
bgtz rs,offset     if rs>0  then jump
bgt rs,rt,offset   if rs>rt then jump
ble rs,rt,offset   if rs<=rt then jump
bgtu rs,rt,offset  (unsigned) 
bleu rs,rt,offset  (unsigned)

j offset           jump to offset
jal offset         jump to offset and link (return address save in ra)
jr rs              jump to rs
jalr rs            jump to rs and link (ra)
ret                return (return address in ra)

csrr  rd,csr       rd=csr
csrw  csr,rs       csr=rs
csrs  csr,rs       set bit csr 
csrc  csr,rs       clear bit csr

csrwi csr,imm
csrsi csr,imm
csrci csr,imm
```

