
- UNIX 标准头文件路径

  - ```c
    /usr/include
    /usr/local/include
    /usr/target/include
    /usr/lib/gcc/target/version/include
    ```

- `<>`包含头文件

  1. 首先搜索 -I 指定路径
  2. 标准头文件路径

- `""`包含头文件

  1. 首先搜索当前的工作目录
  2. 搜索 -I 指定路径 （ ? ）
  3. 标准头文件路径

- 头文件搜索路径双线


  - `-I` 后指定的路径
  - 系统头文件路径 和 以 `-prefix` 、`-withprefix` 、`-idirafter` 操作后的路径

- 若编译的是 c++ 程序，在搜索上述目录前，预处理器会首先搜索 `/usr/include/g++v3` 目录，`v3` 是gcc的版本 ( 存疑 )

- 不使用通配符

- -I 指定的目录，在使用尖括号引用时，是先于标准头文件目录被搜索的。例如使用了 -I./，且当前目录下有一个 stdio.h，那么在 #include <stdio.h>时，所引用的是当前目录下的stdio.h，而不是系统自带的标准头文件



### 利用gcc 命令 查找头文件路径


- ```bash
  `gcc -print-prog-name=cc1` -v
  # 查看 c 的头文件搜索目录命令
  [sinow@SiMaj ~]$ `gcc -print-prog-name=cc1` -v
  ignoring nonexistent directory "/usr/lib/gcc/x86_64-pc-linux-gnu/10.2.0/../../../../x86_64-pc-linux-gnu/include"
  #include "..." search starts here:
  #include <...> search starts here:
   /usr/lib/gcc/x86_64-pc-linux-gnu/10.2.0/include
   /usr/local/include
   /usr/lib/gcc/x86_64-pc-linux-gnu/10.2.0/include-fixed
   /usr/include
  End of search list.
  
  
  
  `gcc -print-prog-name=cc1plus` -v
  # 查看 c++ 的头文件搜索目录命令
  [sinow@SiMaj ~]$ `gcc -print-prog-name=cc1plus` -v
  ignoring nonexistent directory "/usr/lib/gcc/x86_64-pc-linux-gnu/10.2.0/../../../../x86_64-pc-linux-gnu/include"
  #include "..." search starts here:
  #include <...> search starts here:
   /usr/lib/gcc/x86_64-pc-linux-gnu/10.2.0/../../../../include/c++/10.2.0
   # 即 /usr/include/c++/10.2.0
   /usr/lib/gcc/x86_64-pc-linux-gnu/10.2.0/../../../../include/c++/10.2.0/x86_64-pc-linux-gnu
   # 即 /usr/include/c++/10.2.0/x86_64-pc-linux-gnu
   /usr/lib/gcc/x86_64-pc-linux-gnu/10.2.0/../../../../include/c++/10.2.0/backward
   # 即 /usr/include/c++/10.2.0/backward
   /usr/lib/gcc/x86_64-pc-linux-gnu/10.2.0/include
   /usr/local/include
   /usr/lib/gcc/x86_64-pc-linux-gnu/10.2.0/include-fixed
   /usr/include
  End of search list.
  
  
  根据上述内容可知
  c 调用的路径为
  /usr/lib/gcc/x86_64-pc-linux-gnu/10.2.0/include
  /usr/local/include
  /usr/lib/gcc/x86_64-pc-linux-gnu/10.2.0/include-fixed
  /usr/include
  
  c++ 调用的路径 比 c 调用的路径仅仅多出了
  /usr/include/c++/10.2.0
  /usr/include/c++/10.2.0/x86_64-pc-linux-gnu
  /usr/include/c++/10.2.0/backward
  ```



### gcc寻找头文件路径

1. 通过参数 -I 指定路径，按照顺序搜索头文件

2. 通过查找gcc环境变量

   - `C_INCLUDE_PATH` 、 `CPLUSH_INCLUDE_PATH` 、 `OBJC_INCLUDE_PATH`

   - ```bash
     # C
     export C_INCLUDE_PATH=XXXX:$C_INCLUDE_PATH
     # CPP
     export CPLUS_INCLUDE_PATH=XXX:$CPLUS_INCLUDE_PATH
     ```

   - 

3. 通过标准系统目录搜索

   - /usr/include
   - /usr/local/include
   - /usr/lib/gcc-lib/i386-linux/2.95.2/include
     - gcc程序 库文件地址

- gcc 还有一个参数 `-nostdinc`
  - 使编译器不在系统缺省的头文件目录中查找头文件
  - 一般与 -I 参数一起使用
    - 强制gcc 不搜索系统默认路径 /usr/include 等
    - 仅仅使用指定的 头文件路径
- 

