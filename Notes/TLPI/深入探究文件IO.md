## 原子操作与竞争条件
###以独占方式创建一个文件 

- 同时指定`O_EXCL`与`O_CREAT`作为open标志位时
  - 若文件已經存在，则open返回一个错误
  - 这个保证了进程是要打开文件的创建者
  - 文件是否存在的检查与创建文件属于同一个操作

### 向尾部追加数据

- 若多个进程同时向同一个文件尾部添加数据

  - ```c
    if (lseek(fd, 0,SEEK_END)==-1)
        errExit("lseek");
    if (write(fd, buf, len) != len)
        fatal("Partial/failed write");
    ```

  - 两个进程均运行上述代码，会产生竞争条件

  - 用O_APPEND 标志可保证均在文件尾部添加数据

## fcntl() 文件控制操作

- ```c
  #include<fcntl.h>
  int fcntl(int fd, int cmd, ...);
  ```

### F_GETFL

- 获取或修改一个打开的文件的访问模式和状态标志

- ```c
  int flags, accessMode;
  
  flags = fcmtl(fd, F_GETFL);
  if(flags == -1)
      errExit("fcntl");
  if(flags & O_SYNC)
      printf("writes are synchronized\n");
  ```

- 针对一个打开文件，只有通过open或fcntl的F_SETFL操作，才能对其状态标志进行设置

- 对于打开大文件技术，F_GETFL操作，标志中总是包含 O_LARGEFILE 标志

- 判断文件的访问模式（只读写）较复杂，因O_RDONLY、O_WRONLY、O_RDWR三个常量不与打开文件状态标志中的单个比特位对应，需要使用掩码 O_ACCMODE 与 flag 相与，结果与三常量比对

  - ```c
    accessMode = flags & O_ACCMODE;
    if (accessMode == O_WRONLY || accessMode == O_RDWR)
        printf("file is writable\n");
    ```

### F_SETFL

- 允许更改的状态标志有O_APPEND、O_NONBLOCK、O_NOATIME、O_ASYNC、O_DIRECT，系统将忽略对其他标志的修改操作

- 适用场景

  - 文件不是由调度程序打开的，无法使用open来控制文件的状态标志。如，3个标准输入输出描述符
  - 文件描述符的获取是通过open之外的系统调用，如，pipe()、socket() 调用

- 修改流程

  - F_GETFL-->副本-->修改-->F_SETFL-->更新

  - ```c
    int flags;
    
    flags = fcntl(fd, F_GETFL);
    if(flags == -1)
        errExit("fcntl");
    flags |= O_APPEND;
    if(fcntl(fd,F_SETFL,flags) == -1)
        errExit("fcntl");
    ```

## 文件描述符与打开文件之间的关系

- 内核维护的3个数据结构

  - | 数据结构   | 类型       |
    | ---------- | ---------- |
    | 文件描述表 | 进程级     |
    | 打开文件表 | 系统级     |
    | i-node 表  | 文件系统级 |

  - 文件描述表

    - 针对每个进程，内核为其维护打开文件的描述符表
      - 控制文件描述符操作的一组标志
      - 打开文件句柄的引用

  - 打开文件表

    - 内核对所有打开的文件维护了一个系统级描述表格
    - 表中各条目成为打开文件句柄
    - 一个打开文件句柄存储了一个打开文件相关的全部信息
      - 当前文件偏移量
      - 状态标志
      - 文件访问模式
      - 与信号驱动I/O相关的设置
      - 对该文件i-node 对象的引用

  - i-node 表

    - 每个文件系统都会为驻留其上的所有文件建立一个i-node表
      - 文件类型（如，常规文件、套接字、FIFO）和访问权限
      - 一个指向该文件所持有的锁的列表
      - 文件各种属性，包括文件大小、不同类型操作相关的时间戳
    - 14章详细讨论i-node结构与文件系统总体结构
    - 此处忽略了i-node在磁盘和内存中的差异
    - 磁盘上的i-node表
      - 记录了文件的固有属性
        - 文件类型、访问权限、时间戳
    - 内存中的i-node表
      - 访问一个文件时，内存为i-node创建一个副本
      - 记录了：
        - 引用该i-node打开文件句柄的数量
        - 该i-node所在设备的主、从设备号
        - 打开文件时与文件相关的临时属性，如，文件锁
    
  - 文件描述符、打开的文件句柄、i-node之间的关系

    - ![alt ](https://i.loli.net/2021/10/12/OyvWUDxJF6Lm5w8.jpg)
    - 进程A中，fd 1、20指向同一个打开的文件句柄
      - 可能是通过调用dup() dup2() fcntl() 形成的
    - 进程A fd 2 与进程B fd 2 都指向同一个打开的文件句柄 73
      - 可能在调用fork后出现（父子关系）
      - 可能某进程通过UNIX域套接字，将一个打开的文件描述符传递给另一个进程
    - 进程A fd 0 与 进程B fd 3 分别指向不同打开文件句柄，但两句柄指向i-node表的相同条目 1976，即指向同一文件
      - 每个进程各自对同一文件发起了open()调用
      - 同一个进程两次打开同一文件也可产生类似现象
    - 两个不同fd，若指向同一个打开文件句柄，则共享同一文件偏移量，共享同一组打开的文件标志（如：O_APPEND、O_NONBLOCK、O_ASYNC）
    - 文件描述符标志（close-on-exec标志）为进程和文件描述符私有
      - 对这一标志的修改不会影响同一进程或不同进程中其他的其他文件描述符


## 复制文件描述符

### dup()

- dup 调用复制一个打开的文件描述符 oldfd，并返回一个新的描述符，而这都指向同一打开的文件句柄

- 系统会保证新描述符一定是编号值最低的未用文件描述符

- ```c
  #include<unistd.h>
  int dup(int oldfd);
  ```

- 错误返回 -1

- ```c
  newfd = dup(1);
  ```

  - 正常情况下，shell已经代表程序打开了文件描述符0、1、2，假设没有其他描述符在用，dup() 会创建文件描述符1的副本，返回文件描述符3

### dup2()

- ```c
  #include<unistd.h>
  int dup2(int oldfd, int newfd);
  ```

- 成功返回新文件描述符

- 错误返回 -1

- dup2() 为oldfd参数所指定的文件描述符创建副本，其编号由newfd参数指定

- 如果由newfd参数指定编号的文件描述符已经打开，则dup2会先将其关闭

- dup2 会忽略newfd 关闭期间出现的任何错误，因而更安全的做法是，若dup2之前，若newfd已打开，先显式调用close关闭关闭

- 若dup2成功，则返回副本的文件描述符编号（newfd）

- 若oldfd并非有效文件描述符，则dup2 调用将失败并返回错误 EBADF，且不关闭newfd

- 若oldfd有效，且与newfd相等，则dup2将什么也不做，不关闭newfd，并将其作为调用结果返回

### fcntl() F_DUPFD 复制文件描述符

- ```c
  newfd = fcntl(oldfd, F_DUPFD, startfd);
  ```

- 创建副本，且将使用大于等于startfd的最小未用值作为描述符编号

- 文件描述符的正、副本之间共享同一打开文件句柄所含的文件偏移量和状态标志。

- 但是新文件描述符有其一套文件描述符标志，且其 close-on-exec 标志（FD_CLOEXEC） 总是处于关闭状态

### dup3()

- ```c
  #define _GNU_SOURCE
  #include<unistd.h>
  
  int dup3(int oldfd, int newfd, int flags);
  ```

- 与dup2相同，只是新增了一个附加参数flag，这是一个可以修改系统调用行为的位掩码

- 错误返回 -1

- dup3 只支持一个标志 O_CLOEXEC，促使内核为新文件描述符设置close-on-exec标志（FD_CLOEXEC）



## pread() & pwrite()

- ```c
  #include<unistd.h>
  
  ssize_t pread(int fd, void *buf, size_t count, off_t offset);
  
  ssize_t pwrite(int fd, const void *buf, size_t count, off_t offset);
  ```

- 错误返回 -1

- pread 与 pwrite 均为原子操作

- pread()相当于将如下调用纳入同一原子操作

  - ```c
    off_t orig;
    
    orig = lseek(fd, 0, SEEK_CUR);
    lseek(fd, offset, SEEK_SET);
    s = read(fd, buf, len);
    lseek(fd, orig, SEEK_SET);
    ```

  - 对 pread() 和 pwrite() 而言，fd所指代的文件必须是可定位的，即允许对fd执行lseek() 调用



## readv() writev()

- ```c
  #include<sys/uio.h>
  
  ssize_t readv(int fd, const struct iovec *iov, int iovcnt);
  
  ssize_t writev(int fd,const struct iovec *iov, int iovcnt);
  ```

- 错误返回 -1

- 一次即可传输多个缓冲区的数据

- 数组 iov 定义了一组用于传输数据的缓冲区

- 整型 iovcnt 指定了iov的成员个数

- iov 中的每个数据结构均为

  - ```c
    struct iovec {
        void *iov_base;
        size_t iov_len;
    }
    ```

- SUSv3 允许系统对 iov 中成员个数加以限制

  - <limits.h> 中的IOV_MAX
  - sysconf(_SC_IOV_MAX)
  - SUSv3要求限额不少于16
  - Linux 价格IOV_MAX定义为1024，这是内核对该响亮大小的限制（内核常量 UIO_MAXIOV定义）
  - glibc 对 readv() writev() 封装函数做了额外工作
    - 若系统调用因iovcnt参数值过大而失败，外壳函数将临时分配一块缓冲区，其大小足以容纳iov所有成员，随后再执行 read() write() 调用

- 分散输入 readv()

  - 原子操作
  - 从 fd 所指文件中读取一片连续字节，分散放置于iov指定的缓冲区中
  - 从iov[0]开始，依次填满每个缓冲区
  - 成功返回读取的字节数，若文件结束返回0
  - 必须对返回值进行检查，验证读取的字节数是否满足要求
  
- 分散输出 writev()

  - 原子操作
  - 将iov所指定的所有缓冲区中的数据拼接起来，以连续的字节序列写入fd指代的文件中
  - 从iov[0]开始，依次读取每个缓冲区
  - 必须检查返回值，以确定写入的字节数是否与要求相符

### preadv() pwritev()

- ```c
  #define _BSD_SOURCE
  #include<sys/uio.h>
  
  ssize_t preadv(int fd, const struct iovec *iov, int iovcnt, off_t offset);
  
  ssize_t pwritev(int fd, const struct iovec *iov, int iovcnt, off_t offset);
  ```
  
- 错误返回 -1

### truncate() ftruncate()

- ```c
  #include<unistd.h>
  
  int truncate(const char *pathname, off_t length);
  
  int ftruncate(int fd, off_t length);
  ```

- 成功返回 0

- 错误返回 -1

- 若当前文件长度大于length，则丢弃超出部分，若小于length，则在文件尾部添加一系列空字节或文件空洞

- truncate() 以路径名字符串来指定文件，要求可访问该文件，写权限，对符号链接解引用

- ftruncate()，调用之前用可写方式操作文件，获取fd后指代该文件，该调用不会修改文件偏移量

- SUSv3规定

  - 若ftruncate() 的 length 值超出文件的当前大小，则：
    - 扩展该文件（Linux）
    - 返回错误（符合XSI标准的系统）
  - 若truncate() 的 length 值超出文件的当前大小，则要求总是能扩展文件



### 非阻塞I/O

- 打开文件时，指定O_NONBLOCK标志
  - 若open()调用未能立即打开文件，则返回错误，而非陷入阻塞
  - 例外是 open() 对 FIFO 操作， 可能会陷入阻塞，见44.7节
  - 调用open成功后，后续的IO操作也是非阻塞的
    - 传输部分数据或调用失败，返回 EAGAIN 或 EWOULDBLOCK 错误  
  - 管道、FIFO、套接字、设备（如，终端、伪终端）都支持非阻塞模式
  - 管道、套接字的文件描述符无法通过open() 获取，所以要启用非阻塞标志，必须使用fcntl() 的F_SETFL 命令
  - 内核缓冲区保证了普通文件IO不会陷入阻塞，故打开普通文件一般会忽略O_NONBLOCK标志；但是使用强制文件锁时，O_NONBLOCK 对普通文件也是起作用

### 打开大文件

- 待续

### /dev/fd 目录

- 对于每个进程，内核都提供一个特殊的虚拟目录/dev/fd

- 包含"/dev/fd/n" 形式的文件名，其中n是与进程打开文件描述符相对应的编号

- 打开/fev/fd 目录中的一个文件等同于复制相应的文件描述符

- ```c
  fd = open("/dev/fd/1",O_WRONLY);
  fd = dup(1);
  //两者等价
  ```

- open() 设置flag参数时，注意与原描述符相同，flag标志引入其他标志，系统会将其忽略

- /dev/fd --> /proc/self/fd

- /dev/stdin --> /dev/fd/0

- /dev/stdout --> /dev/fd/1

- /dev/stderr --> /dev/fd/2



### mkstemp() tmpfile()

- 进程退出时自动删除临时文件

- ```c
  #include<stdlib.h>
  int mkstemp(char *template);
  ```

- 成功返回fd

- 错误返回 -1

- 模板参数为路径名，最后六个字符为 "XXXXXX"，这六个字符将会被替换，以保证文件名唯一性，且修改后的字符串通过template参数传回

- 因会对传入的template参数进行修改，所以必须是字符数组，不能是字符串常量

- 文件owner 对mkstemp() 创建的文件拥有读写权限，其他用户无任何权限，且打开时使用了O_EXCL 标志，保证调用者独占方式访问文件

- 通常打开临时文件不久就会用unlink() 系统调用将其删除

- ```c
  int fd;
  char template[] = "/tmp/somestringXXXXXX";
  fd = mkstemp(template);
  if (fd == -1)
      errExit("mkstemp");
  printf("Generated filename was: %s\n",template);
  unlink(template);
  // 名字立即删除，但文件在close之后删除
  
  if (close(fd) == -1)
      errExit("close");
  ```

- ```c
  #include<stdio.h>
  FILE *tmpfile(void);
  ```

- 成功返回指针

- 返回一个文件流供stdio库函数使用，文件流关闭后自动删除临时文件

- 未达到上一目的，在tmpfile() 打开文件后，从内部立即调用unlink() 来删除该文件名

- 错误返回NULL

- 创建一个名称唯一的临时文件

- 读写方式打开，使用O_EXCL 标志，以防出现一个可能性极小的冲突，即另一个进程创建了一个同名文件