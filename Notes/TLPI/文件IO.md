## 通用I/O模型

- 所有执行IO操作的系统调用都以文件描述符来指代打开的文件

- 文件描述符是一个非负整数，通常是小整数

- 文件描述符用以表示所有类型的已打开文件，包括管道、FIFO、socket、终端、设备、普通文件

- 每个进程拥有独立的文件描述符命名空间

- 标准文件描述符

  - | 文件描述符 | 用途     | POSIX 名称    | stdio流 |
    | ---------- | -------- | ------------- | ------- |
    | 0          | 标准输入 | STDIN_FILENO  | stdin   |
    | 1          | 标准输出 | STDOUT_FILENO | stdout  |
    | 2          | 标准错误 | STDERR_FILENO | stderr  |

  - 在程序中指代这些文件描述符时，更应该用 POSIX 标准名称，因 freopen() 库函数可使这些变量指代其他任何文件对象，无法保证stdout变量值仍未1

- 文件IO操作4个主要系统调用

  - ```c
    fd = open(pathname, flags, mode);
        
    #include<sys/stat.h>`
    #include<fcntl.h>
    int open(const char *pathname, int flags, .../* mode_t mode*/)
    ```

    - 打开文件，返回文件描述符，失败返回 -1

    - SUSv3规定，若调用成功，必须保证其返回值为进程未用文件描述符中数值最小者，可利用该特性以特定文件描述符打开某一文件

      - ```c
        if (close(STDIN_FILENO) == -1)
            errExit("close");
        
        fd = open(pathname, O_RDONLY);
        if (fd==-1)
            errExit("open");
        ```

      - 除此方法外，`dup2()`、`fcntl()`也可实现类似功能

    - pathname

      - 会对符号链接解引用

    - flags 掩码

      - 若文件不存在，是否创建

      - | 访问模式    | 描述                                 | UNIX规范版本 |
        | ----------- | ------------------------------------ | ------------ |
        | O_RDONLY    | 只读                                 | v3           |
        | O_WRONLY    | 只写                                 | v3           |
        | O_RDWR      | 读写                                 | v3           |
        | O_CLOEXEC   | 设置close-on-exec 标志               | v4           |
        | O_CREAT     | 若文件不存在则创建                   | v3           |
        | O_DIRECT    | 无缓存的输入/输出                    |              |
        | O_DIRECTORY | 若pathname不是目录，则失败           | v4           |
        | O_EXCL      | 结合 O_CREAT 使用，专用于创建文件    | v3           |
        | O_LARGEFILE | 在32位系统使用此标志打开大文件       |              |
        | O_NOATIME   | 调用read()时，不修改文件最近访问时间 |              |
        | O_NOCTTY    | 不让pathname成为控制终端             | v3           |
        | O_NOFOLLOW  | 对符号链接不予解引用                 | v4           |
        | O_TRUNC     | 截断已有文件，使其长度为0            | v3           |
        | O_APPEND    | 总在文件尾部追加数据                 | v3           |
        | O_ASYNC     | 当IO操作可行时，产生信号通知进程     |              |
        | O_DSYNC     | 提供同步的IO数据完整性               | v3           |
        | O_NONBLOCK  | 以非阻塞方式打开                     | v3           |
        | O_SYNC      | 以同步方式写入文件                   | v3           |
        
      - 具体用法可查看TLPI 62页
  
    - mode 参数
    
      - 访问权限
      
    - 若open函数并未创建文件，则可省略 mode 参数
      
    - 新建文件的访问权限不仅仅依赖于mode，还有进程的umask值，更多要结合15.4.6查看
    
    - 函数返回错误
    
      - | 错误值  | 描述                                                         |
        | ------- | ------------------------------------------------------------ |
        | EACCES  | 无法访问文件，可能目录权限限制，或文件不存在且无法创建该文件 |
        | EISDIR  | 所指定的文件为目录，不能进行写操作                           |
        | EMFILE  | 进程打开的fd数量达到了进程资源设定的上限                     |
        | ENFILE  | 文件打开数量达到系统允许的上限                               |
        | ENOENT  | 文件不存在且未指定O_CREAT，或指定了O_CREAT，但所指定的路径不存在，或文件为符号链接且其指向文件不存在 |
        | EROFS   | 只读文件系统，不能以写方式打开                               |
        | ETXTBSY | 所指定为可执行文件，且正在运行，不允许修改正在运行的程序     |
    
      - 
    
  - ```c
    numread = read(fd, buffer, count);
    
    #include<unistd.h>
    ssize_t read(int fd, void *buffer, size_t count);
    ```
  
    - 读取至多 count 字节的数据，并存储到 buffer，返回值为实际读取到的字节数
  
    - 若再无字节可读，则返回 0
  
    - 错误返回 -1
  
    - 读取的字符可能会小于count，即使是管道、FIFO、socket、终端
  
      - 如终端，默认情况下从终端读取到`\n`, read() 调用就会结束
  
    - read() 读取的信息可能是文本数据、二进制整数、二进制数据结构等，read() 无法区分这些数据，故无法遵从C对字符串处理的约定，在尾部追加标识字符串结束的空字符。因而需要显式追加
  
      - ```c
        char buffer[MAX_READ+1];
        ssize_t numRead;
        numRead=read(STDIN_FILENO,buffer,MAXREAD);
        if(numRead==-1)
            errExit("read");
        buffer[numRead]='\0';
        printf("The input data was: %s\n",buffer);
        ```
    
  - ```c
    numwritten = write(fd, buffer, count);
    
    #include<unistd.h>
        ssize_t write(int fd, void *buffer, size_t count);
    ```
    
    
    - 从 buffer 中读取至多 count 字节的数据写入文件，返回实际写入的字符数
    - 错误返回 -1
    - 返回值小于count可能由于磁盘已满、进程资源对文件大小的限制(RLIMIT_FSIZE)
    - 对磁盘文件执行IO操作时，write() 调用成功不保证数据已经写入磁盘，因为内核会缓存磁盘的IO操作
  - 返回值为实际写入的字节数
  
  - ```c
  status = close(fd);
  ```
  
    - 释放文件描述符以及相关的内核资源
    
    - 成功返回0, 错误返回-1
    
    - 错误捕获
    
      - ```c
        if (close(fd) == -1)
            errExit("close");
  ```
  
      - 可捕获：关闭一个未打开的fd、两次关闭同一个fd、特定文件系统在关闭操作中诊断出的错误条件
  
- lseek() 改变文件偏移量

  - 每个打开的文件，系统内核会记录其文件偏移量，以相对于文件头部为起点来表示

  - 文件第一个字节的偏移量为0

  - ```c
    #include<unistd.h>
    off_t lseek(int fd, off_t offset, int whence)
    ```

    - 调整某个已打开文件的偏移量

    - 成功返回偏移量

    - 失败返回 -1

    - offset 指定以字节为单位的数值

    - whence 表明应参照哪个基点来解释 offset 参数

    - | whence 参数 | 描述                   | offset   |
      | ----------- | ---------------------- | -------- |
      | SEEK_SET    | 设置为从头部起始点开始 | 非负数   |
      | SEEK_CUR    | 从当前文件偏移量开始   | 可为负数 |
      | SEEK_END    | 从文件尾部为起始点     | 可为负数 |

  - ```c
    curr = lseek(fd,0,SEEK_CUR);
    //获取当前文件偏移量位置
    
    lseek(fd, 0 ,SEEK_SET);
    //文件开头
    lseek(fd, 0 ,SEEK_END);
    //文末
    lseek(fd, -1 ,SEEK_SET);
    //最后一个字符
    lseek(fd, -10 ,SEEK_CUR);
    //当前往前的10个字符
    lseek(fd, 10000 ,SEEK_SET);
    ```

  - lseek 不使用于管道、FIFO、socket、终端，出错，并将errno设置为errno

  - lseek 可以用于设备，如在磁盘上查找一处具体位

  - 文件空洞：

    - 文件偏移量跨越文件结尾后再执行IO操作，read() 返回0,表示文件结尾; write() 可在结尾后任一位置写入数据
    - 读取文件空洞返回空字节0
    - 文件空洞不占用磁盘空间
      - 若空洞边界落在块内，而非恰好落在边界上，则会分配一个完整的块来存储数据，块中与空洞相关部分以空字节填充
      - 14.4节，描述空洞在文件中的表示方式

## 通用I/O模型以外的操作：ioctl()

- ```c
  #include<sys/ioctl.h>
  int ioctl(int fd, int request, .../*argp*/);
  ```

- request 参数指定了将在fd上执行的操作，具体设备的头文件定义了可传递给request参数的常量

- `...` 可以是任意数据类型

- ioctl() 根据request的参数值来确定argp所期望的类型

- argp通常是指向整数或结构的指针

- 有时不需要argp

- 错误返回 -1

