# X86ASM
《X86汇编语言：从实模式到保护模式》配套源码学习与注释

## 相关资料
[作者李忠的博客](http://www.lizhongc.com/)  
[配套源码及工具](http://www.lizhongc.com/tools/download.asp?id=201)  
[课后检测题答案](http://www.lizhongc.com/x86asm/download/%BC%EC%B2%E2%B5%E3%BA%CD%CF%B0%CC%E2%B4%F0%B0%B8.txt)  
[IBM Linux汇编语言开发指南](https://www.ibm.com/developerworks/cn/linux/l-assembly/index.html)  
[Intel 80386 Programmer's Reference Manual](http://www.logix.cz/michal/doc/i386/)  
[Intel 80386 Programmer's Reference Manual PDF版](https://github.com/DylanLiuH2O/X86ASM/blob/main/PDF/i386.pdf)
    
## 常用工具
[VirtualBox虚拟机](https://www.virtualbox.org/)  
[Emu8086汇编仿真器（只支持MASM/TASM语法格式）](https://emu8086-microprocessor-emulator.en.softonic.com/)  
[Bochs虚拟机（用于调试VirtualBox生成的VHD，用法见作者教程）](http://bochs.sourceforge.net/)  
[jdoodle在线NASM IDE](https://www.jdoodle.com/compile-assembler-nasm-online/)  
[CodingGround在线NASM IDE](https://www.tutorialspoint.com/compile_assembly_online.php)  
[myCompiler在线NASM IDE](https://www.mycompiler.io/new/asm-x86_64)  

## 笔记[更新中]
### 第2章 处理器、内存和指令

#### 寄存器

8086处理器有8个16位的通用寄存器，分别是AX、BX、CX、DX、SI、DI、BP、SP。

| 寄存器名 | 全称                             |
| -------- | -------------------------------- |
| AX       | Accumulator Register 累加寄存器  |
| BX       | Base Register 基址寄存器         |
| CX       | Count Register 计数寄存器        |
| DX       | Data Register 数据寄存器         |
| SI       | Source Index 源索引寄存器        |
| DI       | Destination Index 目标索引寄存器 |
| BP       | Base Pointer 基址指针寄存器      |
| SP       | Stack Pointer 栈顶指针寄存器     |

AX、BX、CX、DX可以分别拆开来用，16位分成高8位和低8位。

AX ---> AH、AL

BX ---> BH、BL

CX ---> CH、CL

DX ---> DH、DL

段寄存器有四个，分别是CS、DS、ES和IP

| 寄存器名 | 全称                               |
| -------- | ---------------------------------- |
| CS       | Code Segment 代码段寄存器          |
| DS       | Data Segment 数据段寄存器          |
| ES       | Extra Segment 附加段寄存器         |
| IP       | Instruction Pointer 指令指针寄存器 |

#### 分段机制

在编写程序时，我们习惯于把数据放在一个位置，然后通过指令去操作这些数据，但如果我们在操作这些数据时，使用的是真实的内存地址，这样写出来的程序就会是无法进行重定位的，也就是说如果程序在内存中的载入位置和预想的不同，那么程序就无法正常运行。

为了解决这个问题，8086引入了内存分段机制，这时候指令通过以段首地址为起始地址的偏移值来访问内存，这时候无论程序在内存的何处载入，指令访问的内存地址相对于段首的偏移量始终是相同的，因此只要确定段首地址，程序就可以正常运行。

在访问内存的时候，将**数据段的起始地址与偏移值相加**，就得到了真实的内存地址，此时就可以进行访问了。

8086有20根地址线，也就是说可以访问2^20=1MB的内存空间，但是段寄存器只有16位，也就是说段地址+偏移地址最多也只有16位，16位最多能访问2^16=64KB的内存，这样没法访问完全1MB的内存。

为了解决这个问题，8086在形成物理地址时，会先将段地址左移4位，此时16位的段地址变为了20位，这时候再加上16位的偏移地址，就形成了真正的物理地址。

由于偏移地址最大只有16位，因此每个段最大的空间只有64KB。

段的最小空间为16KB，因为段地址在左移4位之前是0000H、0001H、0002H...这样的，所以左移时候是00000H、00010H、00020H...这样的，也就是说，段地址是16字节对齐的，所以每个段最小位为16B。

#### 检测题

##### 检测点2.2

**如图2-6所示，加入处理器访问内存时是按低端字节序（低尾端）的，那么从地址0008H处取出一个字时，该字的值为？**

答：由于是按低尾端访问，即值的低字节位于低地址处，那么0008H开始的第一个字节为低字节，0009H的字节为高字节，因此取出来的字为A3 D8。

##### 检测点2.3

1. INTEL8086处理器有<u>8</u>个16位通用寄存器，分别是<u>AX, BX, CX, DX, SI, DI, BP, SP</u>。其中，有些还可以分开来作为两个独立的8位寄存器来用，这几个8位寄存器分别为<u>AX, BX, CX, DX</u>。

2. 选择题（可多选）：INTEL8086处理器取指令时，使用段寄存器<u>CS</u>和指令指针寄存器<u>IP</u>。方法是，将段寄存器的值<u>左移4位/乘以16</u>，加上指令指针寄存器的当前值，形成物理地址访问内存。

3. 物理地址132FEH对应的逻辑地址是（可多选）：A、B、C、D

   A.132FH:000EH	B.1300H:02FEH	C.1000H:32FEH	D.1320H:00FEH	E.102FH:03E0H	F.0FE0H:34FEH

#### 习题

1. 在段与段之间互不重叠的前提下，1MB内存可以完整划分为多少个16KB的段？

   1MB的内存地址范围为00000H~FFFFFH，即100000H($2^{20}$=1048576)个字节，16K=$2^{14}$=4000H=16384，那么100000H/40000H=1048576/16384=64个，所以1MB内存可以完整划分为64个16KB的段。

2. 数据段寄存器DS的值为25BCH时，计算INTEL8086可以访问的物理地址范围。

   由于偏移地址只有16位，因此偏移地址的范围为0000H ---> FFFFH，那么物理地址范围为25BC00H + 0000H = 25BC00H  ---->  25BC00H + FFFFH = 26BBFFH，即25BC00H->26BBFFH。

### 第3章 汇编语言和汇编软件

#### 基本指令

1. mov指令

   使用形式：mov 目的操作数 源操作数

   作用：将源操作数传送到目的操作数

   源操作数：立即数、内存或者寄存器

   目的操作数：寄存器或内存

2. add指令

   使用形式：add 目的操作数 源操作数

   作用： 将源操作数加到目的操作数上，并存储在目的操作数中

   源操作数：立即数、内存或者寄存器

   目的操作数：寄存器

#### 检测题

##### 检测点3.1

2. 选择填空：指令mov ax,0xf5fc中，“mov”指示这是一条<u>传送</u>指令，0xf5fc是<u>立即数</u>，指令执行后，寄存器AX中的内容是<u>0xf5fc</u>。

#### 习题

如图3-6所示，请问：

1. 源程序共有3行，每一行每一个字符在文件内的偏移量分别是多少？
2. 该源程序文件的大小是多少字节？

### 第5章 编写主引导扇区代码

#### 标号

标号是用于代表本行汇编地址的一个名称，在编译阶段，标号会被替换为标号所在行的汇编地址。

编译后指令的汇编地址是相对于段起始地址的偏移量，因此标号所代表的汇编地址是16位的。

#### 两种除法

1. 16位除以8位

   `ax / 8位寄存器 = al ... ah

   指令：`div bh/bl/ch/cl...`

2. 32位除以16位

   dx:ax / 16位寄存器 = ax ... dx

   指令：`div bx/cx...`

#### c05_mbr.asm代码解析

1. 主要功能

   在屏幕上显示`Label Offset: "标号number的地址"`

2. 实现该功能的主要过程

   ① 直接向显存区写入`Label Offset:`，并设置字符的属性。

   ② 使用寄存器存储标号number的地址。

   ③ 使用循环与运算指令将number的地址分解为各个数字，并存储到数据段中。

   ④ 最后将分解好的数位，使用循环写入显存区，并设置字符的属性。

#### 检测题

##### 检测点5.1

##### 检测点5.2

##### 检测点5.3

##### 检测点5.4

##### 检测点5.5

#### 习题

### 第8章 硬盘和显卡的访问与控制

#### 一个标准的用户程序加载器需要完成的工作

1. 定义好用户程序的结构，规定用户程序加载器与用户程序之间用于获取程序信息的头部。
2. 确定用户程序需要加载的位置（内存物理地址）。
3. 确定用户程序写入到硬盘的位置（逻辑扇区号）。
4. 使用I/O指令，从硬盘的指定位置读取用户程序，加载到内存中指定的位置。
5. 从用户程序获取入口点代码，并且将用户程序的各个段地址进行重定位，并写入重定位后的段地址。
6. 跳转到用户程序，继续执行。

#### 一个标准的用户程序需要完成的工作

1. 提供用户程序的大小参数。
2. 提供用户程序的入口点参数。
3. 提供程序所含段的数目。
4. 提供各个段的段首地址。
5. 执行完毕后跳转回用户加载器。

#### 编写过程的注意点

1. 过程中会修改到不应修改的寄存器时，注意先要使用栈来保护现场。
2. 保护现场的push和pop指令应成对使用，并且顺序要相反。
3. 定义好过程需要的参数、返回值以及返回方式，最好用注释标明。
4. 调用过程前先存储好参数。
5. 当过程有多个分支时，可能需要使用多次跳转，那么这时程序很可能会难以阅读，因此最好先画好流程图再编写。

#### 索引寄存器

索引寄存器的端口号是`0x3d4`，可以向它写入一个值用来指定内部的某个寄存器。指定索引寄存器后，如果要进行读写，则需要使用数据端口`0x3d5`。

#### 光标寄存器

光标寄存器是两个8位可读可写的寄存器，需要使用索引寄存器来访问，其索引值分别是`14(0x0e)`和`15(0x0f)`，分别用来提供光标寄存器的高8位和低8位。

#### 处理屏幕输出的注意点



### 第11章 进入保护模式

#### 保护模式

保护模式的意义在于，将用户程序彼此隔离开来，禁止随意访问、修改其他用户程序的数据，以保护程序的运行。

#### 全局描述符表

全局描述符表记录了每个段的段地址以及偏移地址等信息，每一个段都需要在描述符表中记录，尔后才能进行访问。

和全局描述符表相关的寄存器是全局描述符表寄存器（GDTR），这是一个48位的寄存器，由32位的线性地址和16位的边界组成。

32位线性地址部分保存的是全局描述符表在内存中的起始线性地址，16位边界保存的是全局描述符表的边界，在数值上等于描述符表的大小，所以描述符表最大为2^16字节。

