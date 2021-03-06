## 80386 CPU

- 4GB $2^{32}$ 物理地址空间
- 64TB $2^{46}$ 虚拟地址空间
- 不仅有分段存储管理方式，还增加了分页存储管理方式
- 具有实地址模式、保护模式和虚拟8086模式三种工作方式

## Pentium CPU

- 与 80X86系列CPU兼容
- 64位数据总线



## IA-32 CPU的主要寄存器

### 通用寄存器——8个

- 8 个 32位通用寄存器

![image-20210914195928280](https://i.loli.net/2021/10/12/Zu5sq1tzXEfSOAm.png)

- EAX,EBX,ECX和EDX可以作为32位、16位或8位寄存器使用。作为16位和8位使用时，与8086中的命名（AX,BX,CX,DX，AH,AL,BH,BL等）一致。
- ESP、EBP、EDI和ESI被扩展为32位，SP、BP、DI和SI仍然与8086一致，为16位。



### 指令寄存器——1个

![image-20210914200236898](https://i.loli.net/2021/10/12/hnEzaPIOrMXK5QZ.png)

- 指令指针EIP为32位，其低16位与8086同名为IP

### 标志寄存器——1个

![image-20210914200324331](https://i.loli.net/2021/10/12/AUghudXKSYEReIZ.png)

- 标志寄存器EFLAGS为32位，其中第0⁓11位共有9个标志位，与8086一致。

### 段寄存器——6个

- 共有6个16位的段寄存器
- CS、SS、DS和ES与8086相同
- 增加了两个数据段寄存器FS和GS
- IA-32中段基址和偏移量都是32位,段寄存器只有16位不能直接产生段基址，其内容称为段选择器，需间接访问才能得到段基地址、段长度以及段的属性



### 系统地址寄存器——4个

- 两个48位的寄存器
  - 全局描述符表寄存器GDTR
  - 中断描述符表寄存器IDT
- 两个16位的寄存器
  - 局部描述符表寄存器LDTR
  - 任务状态段寄存器TR
- 四个寄存器都是用来实现对存储器的访问

### 控制寄存器——4个

- 有4个32位的控制寄存器CR0、CR1、CR2和CR3，其中CR1保留未使用
- 用来设置和保存机器的各种全局性状态，比如是否有浮点部件，是否处于保护模式，是否采用页式存储管理等等



## 工作方式

- 实地址方式——实模式
- 保护虚拟地址工作方式——保护模式
  - 虚拟8086方式

### 实模式

- 与8086兼容
- 32条地址线中只有低20条地址线起作用，可寻址1MB的物理地址空间
- 相当于一个快速的8086，虽然具有32位的数据线和数据寄存器，但无法发挥其全部功能
- 实模式下微处理器不能实现多任务的处理



### 保护模式

- 32条地址可寻址4GB的物理存储器空间
- 支持虚拟存储器功能。每个任务运行可以有16K个段，每个段最大为4GB，一个任务最大可使用64TB虚拟地址空间
- 程序运行分为4个特权等级，操作系统核心运行在最高特权级0，用户程序运行在最低特权级
- <img src="https://i.loli.net/2021/10/12/nBN8DAkmFjsHQt4.png" alt="image-20210914200939136" style="zoom: 33%;" />



### 虚拟86模式

- 寻址空间只有1MB
- 不能实现多任务、多用户处理； 
- 没有特权机制； 
- 不能发挥IA-32微处理器的分页式存储管理机制 



- 把8086/8088的工作模式和IA-32处理器的实模式简称为16位模式
- 将IA-32处理器的保护模式简称为32位模式。



## 保护模式下存储器访问

### 段描述符

- 一个段描述符指示了这个段在内存中的位置、大小以及使用限制。
- 段描述符有64位，包括段基址32位、段限长20位、特权级等属性12位。
- ![image-20210914201206494](https://i.loli.net/2021/10/12/ke4O9wgoCF1RsVA.png)
- 段基址：32 位，它指示该段在 4GB 线性地址空间中的起始地址。
- 属性：12位，包括特权级、段类型、段存在标志、粒度标志等。
  - 特权级DPL：每个段都有一个特权级，取值范围 0 ~ 3，0 级为最高级，一般用于操作系统核心代码。如果特权级数值高的程序试图访问特权级数值低的段，则发生处理器故障。
  - 段类型：指定段的访问类型（只读、可读可写、只执行等）以及段生长的方向（向上或向下）。
  - 段存在标志：指示该段当前是否在物理内存中
  - 粒度标志G：确定对段限长字段的解释。G=0:段限长以字节为单位。G=1:则段限长的解释单位为 4096 字节。。
- 段限长： 20 位，指定段的大小。按照粒度标志，有两种解释：
    - G=0: 允许段大小的范围为 1〜1MB。
    - G=1:允许段大小的范围为 4KB〜4GB。



### 描述符表

- 多个段的段描述符顺序地存放在一起就构成描述符表
- 全局描述符表GDT
  - 系统中所有任务都可能会用到的段描述符所构成的描述符表
  - 系统中只有一个GDT
  - 全局描述符表寄存器GDTR来指示GDT在内存中的存放位置
- 局部描述符表LDT
  - 每个任务的代码段、数据段和堆栈段的段描述符，以及该任务所使用的门描述符组成该任务的局部描述符表LDT
  - 每个任务的LDT在内存中的存放空间形成一个段，这个段的描述符叫LDT描述符
  - LDT描述符作为系统描述符也存放在GDT中。
  - LDTR只有16位，虽然它无法直接指向LDT的地址，但可以间接地访问到局部描述符表LDT
- IA-32有CS、DS、ES、SS、 FS和GS 6个段寄存器
- 段寄存器的内容称为段选择器（或段选择子），用来从GDT或LDT中选择一个段描述符，即用来选择一个段
  - 段选择器
    - <img src="https://i.loli.net/2021/10/12/m2tS3nyPL78EFoC.png" alt="image-20210914202019263" style="zoom:50%;" />
    - 索引值：13位，指示在描述符表中的相对位置。
    - TI：1位，为0则描述符在GDT中，为1则在LDT中。
    - CPL/RPL：2位，表示当前的代码或数据的优先级。CS中就是CPL，其他段寄存器中为RPL。

- 保护模式下的逻辑地址表示
  - 段寄存器：段内地址
  - 段寄存器--为CS、DS、ES、SS、GS、FS之一
  - 段内地址--为32位，段的长度可达$2^{32}$=4GB

### 逻辑地址转换为线性地址的过程

<img src="https://i.loli.net/2021/10/12/rusmBRqQ12o9zdb.png" alt="image-20210914202429952" style="zoom:50%;" />

- 在指令执行时要将逻辑地址转换成一个32位的线性地址才能访问到物理存储器。
- 根据段寄存器中的TI位是0还是1，有两种不同的转换过程。

#### TI=0 —— 段描述符在全局描述符表GDT中

1. 从GDTR中获取GDT的基地址
2. 以段选择器的高13位做索引值在GDT中找到对应的段描述符
3. 从段描述符中获得段基址，即找到段的起始地址
4. 从段基址偏移段内地址即是最终的目的地址-线性地址

<img src="https://i.loli.net/2021/10/12/CRSmEGPWOtTNMzV.png" alt="image-20210914203308091" style="zoom:50%;" />

#### TI=1:段描述符在局部描述符表LDT中

1. 从GDTR中获取GDT的基地址
2. 以LDTR的高13位做索引值在GDT中找到对应的LDT段描述符
3. 从LDT段描述符中获得该LDT的起始地址
4. 以段寄存器的高13位做索引值在LDT中找到对应的段描述符
5. 从段描述符中获得段基址，即找到段起始地址
6. 从段基址偏移段内地址即是最终的线性地址

![image-20210914203123455](https://i.loli.net/2021/10/12/XTsOHF4D7VthvjJ.png)

