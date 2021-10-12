# CMake

### 基本示例

- 目录结构

  - main.c

    - ```c
      #include<stdio.h>
      int main()
      {
      	printf("Hello World!!");
      	return 0;
      }
      
      ```

    - 

  - CMakeLists.txt

    - ```cmake
      PROJECT(HELLO)
      SET(SRC_LIST main.c)
      MESSAGE(STATUS "This is BINARY dir " ${HELLO_BINARY_DIR})
      MESSAGE(STATUS "This is SOURCE dir " ${HELLO_SOURCE_DIR})
      ADD_EXECUTABLE(hello ${SRC_LIST})
      
      ```

- `cmake .`

- 生成文件：

  - CMakeFiles //dir
  - CMakeCache.txt
  - cmake_install.cmake
  - MakeFile

- 生成了MakeFile文件后，即可在本目录使用`make`命令进行项目构建



### 基本命令

- 指令大小写无关，推荐使用大写

- 参数、变量大小写检查严格

- 参数之间用空格或分号分开

- ${} 变量取值，在 `IF` 语句中直接使用变量名

- 可忽略掉source列表中源文件后缀

  - ```cmake
    ADD_EXECUTABLE(t1 main)
    会在本目录查找main.c或main.cpp，推荐写全
    ```

- 对构建结果清理  `make clean` ，删除了可执行文件

#### PROJECT

```cmake
PROJECT(projectname [CXX] [C] [Java])
```

- PROJECT 定义工程名称，指定工程语言（默认支持所有）

- 隐式定义了

  - ```cmake
    <projectname>_BINARY_DIR
    <projectname>_SOURCE_DIR
    于上文项目中即为
    HELLO_BINARY_DIR //即当前工作目录
    HELLO_SOURCE_DIR //即当前工作目录
    
    若不指定projectname
    则默认
    <projectname>==PROJECT
    推荐使用默认
    ```

#### SET

```
SET(VAR [VALUE] [CACHE TYPE DOCSTRING [FORCE]])
```

- 显式定义变量

- ```
  SET(SRC_LIST main.c t1.c t2.c)
  ```

#### MESSAGE

```
MESSAGE([SEND_ERROR | STATUS | FATAL_ERROR] "message to display" ...)
```

- SEND_ERROR 产生错误，生成过程被跳过
- STATUS 输出信息
- FATAL_ERROR 立刻终止所有cmake过程

#### ADD_EXECUTABLE

```cmake
ADD_EXECUTABLE(hello ${SRC_LIST})
```



### 外部构建

#### 目录结构

- ```bash
  ./src/ 源代码
  ./doc/ 文档hello.txt
  ./COPYRIGHT
  ./README
  ./runhello.sh 调用hello二进制
  ./bin/ 构建后目标文件放置位置
  
  安装
  hello runhello.sh --> /usr/bin
  doc/* COPYRIGHT README --> /usr/share/doc/cmake/t2
  ```

#### 步骤

```bash
[t2]$
cp ../t1/main.c .
cp ../t1/CMakeLists.txt .
mkdir src
mv main.c src
cat > src/CMakeLists.txt <<EOF
ADD_EXECUTABLE(hello main.c)
EOF
cat > ./CMakeLists.txt <<EOF
PROJECT(HELLO)
ADD_SUBDIRECTORY(src bin)
mkdir build
cd build
cmake ..
make
```

- 目标文件位于 build/bin/ 中

#### ADD_SUBDIRECTORY

```cmake
ADD_SUBDIRECTORY(source_dir [binary_dir] [EXCLUDE_FROM_ALL])
```

- EXCLUDE_FROM_ALL 将这个目录从编译过程中排除

- 上述例子中，将src/ 加入工程，并指定编译输出路径为bin/

  - 若不指定bin/ 则编译结果（包括中间结果）都存放在 build/src/中
  - 指定 bin/ 后，则编译结果都存放在 build/bin 中

- 通过 SET 指令 可重定义 EXECUTABLE_OUTPUT_PATH LIBRARY_OUT_PATH 变量来指定最终目标二进制的位置（不包含编译生成的中间文件）

  - ```cmake
    SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
    SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)
    ```

  - `${PROJECT_BINARY_DIR}` 为编译发生时的当前目录

    - 若内部编译，则是工程代码所在目录
    - 若外部编译，则是当前所在目录，即本例中的 build/ 目录

  - SET 位置

    - 在 ADD_EXECUTABLE 或 ADD_LIBRARY 的CmakeLists.txt 中，若需要改变目标的存放路径，则在此CmakeLists.txt 中添加 SET



### 安装

#### INSTALL

```cmake
INSTALL(TARGETS targets...
[[ARCHIVE|LIBRARY|RUNTIME]
 [DESTINATION <dir>]
 [PERMISSIONS permissions...]
 [CONFIGURATIONS
  [Debug|Release|...]
  ]
 [COMPONENT <component>]
 [OPTIONAL]
 ]
 [...]
 )
```

- TARGETS 后为 ADD_EXECUTABLE 或 ADD_LIBRARY 定义的目标文件

  - 可执行二进制、动态库、静态库

- 目标类型

  - ARCHIVE 静态库
  - LIBRARY 动态库
  - RUNTIME 可执行二进制

- DESTINATION 定义安装路径

  - / 开头为绝对路径，CMAKE_INSTALL_PREFIX 失效

  - 若使用相对路径，则不以 / 开头，安装结果为

    - ```cmake
      ${CMAKE_INSTALL_PREFIX}/<DESTINATION >
      ```

- 举例

  - ```cmake
    INSTALL(TARGETS myrun mylib mystaticlib
    	RUNTIME DESTINATION bin
    	LIBRARY DESTINATION lib
    	ARCHIVE DESTINATION libstatic
    )
    ```

  - 可执行二进制 myrun --> `${CMAKE_INSTALL_PREFIX}/bin`

  - 动态库 libmylib -->``${CMAKE_INSTALL_PREFIX}/lib`

  - 静态库 libmystaticlib --> ``${CMAKE_INSTALL_PREFIX}/libstatic`

- 普通文件安装

  - ````cmake
    INSTALL(FILES files... DESTINATION <dir>
    	[PERMISSIONS permissions...]
    	[CONFIGURATIONS [Debug|Release|...]]
    	[COMPONENT <component>]
    	[RENAME <name>]
    	[OPTIONAL]
    )
    ````

  - 安装一般文件，指定权限

  - 默认权限 644

    - OWNER_WRITE, OWNER_READ, GROUP_READ, WORLD_READ

- 非目录可执行程序安装

  - ```
    INSTALL(PROGRAMS files... DESTINATION <dir>
    	[PERMISSIONS permissions...]
    	[CONFIGURATIONS [Debug|Release|...]]
    	[COMPONENT <component>]
    	[RENAME <name>]
    	[OPTIONAL]
    )
    ```

  - 默认权限 755

    - OWNER_EXECUTE, GROUP_EXECUTE, WORLD_EXECUTE

- 目录安装

  - ````cmake
    INSTALL(DIRECTORY dirs... DESTINATION <dir>
    	[FILE_PERMISSIONS permissions...]
    	[DIRECTORY_PERMISSIONS permissions...]
    	[USE_SOURCE_PERMISSIONS]
    	[CONFIGURATIONS [Debug|Release|...]]
    	[COMPONENT <component>]
    	[[PATTERN <pattern> | REGEX <regex>]
    	 [EXCLUDE]
    	 [PERMISSIONS permissions...]
    	 ]
    	[...]
    )
    ````

  - DIRECTORY 后为Source目录的相对路径

  - PATTERN 正则表达式过滤

  - PERMISSIONS 指定过滤后的文件权限

    - 例子

    - ```cmake
      INSTALL(DIRECTORY icons scripts/ DESTINATION share/myproj
      	PATTERN "CVS" EXCLUDE
      	PATTERN "scripts/*"
      	PERMISSIONS
      		OWNER_EXECUTE OWNER_WRITE OWNER_READ
      		GROUP_EXECUTE GROUP_READ
      )
      ```

    - icons 目录 --> <prefix>/share/myproj

    - scripts 目录内所有内容 --> <prefix>/share/myproj

    - 不包含名为 CVS 的目录

    - scripts/* 内文件指定权限为 OWNER_EXECUTE OWNER_WRITE OWNER_READ GROUP_EXECUTE GROUP_READ

  - 安装时CMAKE脚本执行

    - ````cmake
      INSTALL([[SCRIPT <file>] [CODE <code>]] [...])
      ````

    - SCRIPT 在安装时调用 cmake脚本文件（\<abc\>.cmake）

    - CODE 用于执行CMAKE指令，指令必须使用双引号

      - ```cmake
        INSTALL(CODE "MESSAGE(\"Sample install message.\")")
        ```

- t2 安装示例

  - ```bash
    [t2]$
    cp ../t1/main.c .
    cp ../t1/CMakeLists.txt .
    mkdir src
    mv main.c src
    cat > src/CMakeLists.txt <<EOF
    ADD_EXECUTABLE(hello main.c)
    EOF
    cat > ./CMakeLists.txt <<EOF
    PROJECT(HELLO)
    ADD_SUBDIRECTORY(src bin)
    mkdir build
    
    mkdir doc
    cat > doc/hello.txt <<EOF
    ...
    EOF
    cat >runhello.sh <<EOF
    ...
    EOF
    touch COPYRIGHT
    touch README
    ```

  - 安装COPYRIGHT、README

    - 修改主工程文件CmakeLists.txt，添加

    - ```cmake
      INSTALL(FILES COPYRIGHT README DESTINATION share/doc/cmake/t2)
      ```

  - 安装runhello.sh

    - 修改主工程文件CmakeLists.txt，添加

    - ```cmake
      INSTALL(PROGRAMS runhello.sh DESTINATION bin)
      ```

  - 安装doc中的hello.txt

    1. 在doc目录建立CmakeLists.txt ，并将doc目录通过ADD_SUBDIRECTORY加入工程

    2. 直接在工程目录通过INSTALL(DIRECTORY)

       - 修改工程文件CmakeLists.txt，添加唉

       - ```cmake
         INSTALL(DIRECTORY doc/ DESTINATION share/doc/cmake/t2)
         ```

  - 运行

    - ```bash
      [t2/build]$
      cmake -D CMAKE_INSTALL_PREFIX=/tmp/t2/usr ..
      make 
      make install
      tree /tmp/t2/
      
      [sinow@SiMaj build]$ tree /tmp/t2/
      /tmp/t2/
      └── usr
          ├── bin
          │   └── runhello.sh
          └── share
              └── doc
                  └── cmake
                      └── t2
                          ├── COPYRIGHT
                          ├── hello.txt
                          └── README
      ```

    - 若不指定 CMAKE_INSTALL_PREFIX，则默认值为 /usr/local/

    - 若要安装到系统，CMAKE_INSTALL_PREFIX == /usr/

### 共享库

#### 基本示例

````bash
[t3]$
cat >CMakeLists.txt << EOF
PROJECT(HELLOLIB)
ADD_SUBDIRECTORY(lib)
EOF
mkdir lib
cat >lib/hello.c <<EOF
#include "hello.h"
void HelloFunc()
{
printf("Hello World!\n");
}
EOF

cat >lib/hello.h <<EOF
#ifndef HELLO_H
#define HELLO_H
#include<stdio.h>
void HelloFunc();
#endif

cat >lib/hello.h <<EOF
SET(LIBHELLO_SRC ../hello.c) #不能直接使用hello.c ？？？？
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
EOF

mkdir build
cd build
cmake ..
make
````

- 在build/lib目录中得到 libhello.so 共享库
- 修改 libhello.so 生成位置
  - 主工程文件修改ADD_SUBDIRECTORY(lib)来指定编译输出位置
  - 或在lib/CmakeLists.txt 中添加 SET(LIBRARY_OUT_PATH \<path\>)来指定一个新位置

#### ADD_LIBRARY

```cmake
ADD_LIBRARY(libname [SHARED|STATIC|MODULE]
	[EXCLUDE_FROM_ALL]
	source1 source2 ... sourceN
)
```

- libname中不需要写全 libhello.so，只需要填写 hello，会自动生成 libhello.X

- SHARED 动态库
- STATIC 静态库
- MODULE 在使用dyld的系统有效，若系统不支持，则被当作SHARED
- EXCLUDE_FROM_ALL 表示这个库不会被默认构建，除非有其他的组件依赖或手工构建

#### 添加静态库

```bash
[t3/build]
cat >> ../lib/CMakeLists.txt <<EOF
ADD_LIBRARY(hello_static STATIC ${LIBHELLO_SRC})
EOF

cmake ..
make
```

- 在 `build/lib/` 中出现了libhello_static.a

- 使用hello_static 而不是 hello，是因为上一句已经出现了

  - ```cmake
    ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
    ```

  - hello 已被使用，hello作为一个target，不可以重名

  - 若要将 hello_static.a 也生成为 hello.a，需使用 `SET_TARGET_PROPERTIES()` 

#### SET_TARGET_PROPERTIES

```cmake
SET_TARGET_PROPERTIES( target1 target2 ...
	PROPERTIES prop1 value1
		prop2 value2 ...
)
```

- ```bash
  [t3/build]
  cat >> ../lib/CMakeLists.txt <<EOF
  SET_TARGET_PROPERTITIES(hello_static PROPERTIES OUTPUT_NAME "hello")
  EOF
  
  cmake ..
  make
  ```

- lib 中出现了hello.a ，但是hello.so 未生成，因为cmake在构建一个新target时，会尝试清理掉其他使用这个名字的库（实际测试中hello.so 仍然生成了hello.so，cmake version 3.20.1），需要使用`SET_TARGET_PROPERTIES`定义CLEAN_DIRECT_OUTPUT 属性

- ```bash
  [t3/build]$
  cat >> ../lib/CMakeLists.txt <<EOF
  SET_TARGET_PROPERTIES(hello PROPERTIES CLEAN_DIRECT_OUTPUT 1)
  SET_TARGET_PROPERTIES(hello_static PROPERTIES CLEAN_DIRECT_OUTPUT 1)
  ```

- 实现动态库版本号

- ```cmake
  SET_TARGET_PROPERTIES(hello PROPERTIES VERSION 1.2 SOVERSION 1)
  ```

  - VERSION 为动态库版本
  - SOVERSION 为API版本

- ```bash
  [t3/build]$
  cat >> ../lib/CMakeLists.txt <<EOF
  SET_TARGET_PROPERTIES(hello PROPERTIES VERSION 1.2 SOVERSION 1)
  EOF
  
  cmake ..
  make
  
  [sinow@SiMaj lib]$ ll
  total 36
  drwxr-xr-x 4 sinow sinow  4096  4月 26 15:15 CMakeFiles
  -rw-r--r-- 1 sinow sinow  1248  4月 26 15:15 cmake_install.cmake
  -rw-r--r-- 1 sinow sinow  1674  4月 26 15:15 libhello.a
  lrwxrwxrwx 1 sinow sinow    13  4月 26 15:15 libhello.so -> libhello.so.1
  lrwxrwxrwx 1 sinow sinow    15  4月 26 15:15 libhello.so.1 -> libhello.so.1.2
  -rwxr-xr-x 1 sinow sinow 15408  4月 26 15:15 libhello.so.1.2
  -rw-r--r-- 1 sinow sinow  6657  4月 26 15:15 Makefile
  ```

#### GET_TARGET_PROPERTY(VAR target property)

```cmake
GET_TARGET_PROPERTY(VALUE hello_static OUTPUT_NAME)
MESSAGE(STATUS "This is the hello_static OUTPUTNAME:"${VALUE})
```

- 若无这个属性定义，则返回 NOTFOUND

#### 安装共享库和头文件

```bash
[t3/build]$
cat >> ../lib/CMakeLists.txt <<EOF
INSTALL(TARGETS hello hello_static
	LIBRARY DESTINATION lib
	ARCHIVE DESTINATION lib
)
INSTALL(FILES hello.h
	DESTINATION include/hello
)
EOF

cmake -D CMAKE_INSTALL_PREFIX=/usr ..
make
make install
```

- 头文件安装到了 /usr/include/hello 中
- 共享库安装到了/usr/lib 中



### 使用外部共享库和头文件

#### INCLUDE_DIRECTORIES

```cmake
INCLUDE_DIRECTORIES([AFTER|BEFORE]
	[SYSTEM]
	dir1 dir2 ...
)
```

- 该指令可向工程添加多个特定的头文件搜索路径，路径之间用空格分割

- 若路径中出现空格，可用双引号

- 默认是append到头文件搜索路径的后面，可修改CMAKE_INCLUDE_DIRECTORY_BEFORE变量，用SET指令设其为 on，可将添加的头文件搜索路径放在已有路径的前面；也可通过AFTER、BEFORE参数控制追加还是置前

- ```bash
  [t4/build]$
  cat >> ../src/CMakeLists.txt <<EOF
  INCLUDE_DIRECTORIES(/usr/include/hello)
  EOF
  
  cmake ..
  make
  ```



#### LINKDIRECTORIES & TARGET_LINK_LIBRARIES

```cmake
LINK_DIRECTORIES(dir1 dir2 ...)
```

- 添加非标准的共享库搜索路径，若工程内部同时存在共享库和可执行二进制，在编译时需要指定这些共享库路径

```
TARGET_LINK_LIBRARIES(target library1
	<debug|optimized> library2
	...
)
```

- 为target添加需要链接的共享库

- 解决t4项目 `undefined reference to 'HelloFunc'`

  - ```bash
    [t4/build]$
    cat >> ../src/CMakeLists.txt <<EOF
    TARGET_LINK_LIBRARIES(main hello)
    或者
    TARGET_LINK_LIBRARIES(main libhello.so)
    默认链接到动态库
    若要链接到静态库，则指定libhello.a
    TARGET_LINK_LIBRARIES(main libhello.a)
    EOF
    
    cmake ..
    make
    
    [sinow@SiMaj build]$ src/main
    Hello World!
    [sinow@SiMaj build]$ ldd src/main
            linux-vdso.so.1 (0x00007ffea50c1000)
            libhello.so => /usr/lib/libhello.so (0x00007f08b80b7000)
            libc.so.6 => /usr/lib/libc.so.6 (0x00007f08b7eea000)
            /lib64/ld-linux-x86-64.so.2 => /usr/lib64/ld-linux-x86-64.so.2 (0x00007f08b80df000)
    ```



#### CMAKE_INCLUDE_PATH & CMAKE_LIBRARY_PATH

- 环境变量

- 若头文件没有存放在 /usr/include  /usr/local/include 等常规路径，则可通过这些变量进行弥补

- 若要实现上文`INCLUDE_DIRECTORIES(/usr/include/hello)` ，则可

  - ```bash
    [t4/build]$
    export CMAKE_INCLUDE_PATH=/usr/include/hello
    
    cat >> ../src/CMakeLists.txt <<EOF
    FIND_PATH(myHeader hello.h)
    IF(myHeader)
    INCLUDE_DIRECTORIES(${myHeader})
    ENDIF(myHeader)
    EOF
    
    ```

- `FIND_PATH(myHeader hello.h)`

- ```cmake
  FIND_PATH(myHeader NAMES hello.h PATHS /usr/include /usr/include/hello)
  ```

  - 上上个例子中，没有指定路径，但由于设置了环境变量CMAKE_INCLUDE_PATH，cmake仍可以找到hello.h路径

- 如果不使用`FIND_PATH`，设置`CMAKE_INCLUDE_PATH`没有用