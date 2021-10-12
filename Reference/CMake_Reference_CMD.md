#### ADD_DEFINITIONS(-D ENABLE_DEBUG -D ABC)

#### ADD_DEPENDENCIES(target-name depend-target1,depend-target2 ...)
```
#确保在编译本target之前，其他target已经被构建
```

#### ADD_EXECUTABLE
#### ADD_LIBRARY
#### ADD_SUBDIRECTORY

#### ADD_TEST(testname Exename arg1 arg2 ...)
```
# exp:
#     ADD_TEST(mytest ${PROJECT_BINARY_DIR}/bin/main)
#     ENABLE_TESTING()
```

#### AUX_SOURCE_DIRECTORY(dir VARIABLE)

```
# exp:
#     AUX_SOURCE_DIRECTORY(. SRC_LIST)
#     ADD_EXECUTABLE(main ${SRC_LIST})
```



#### CMAKE_MINIMUM_REQUIRED(VERSION versionNumber [FATAL_ERROR])



```
# exp:
#     CMAKE_MINIMUM_REQUIRED(VERSION 2.5 FATAL_ERROR)

# EXEC_PROGRAM
# EXEC_PROGRAM(Executable [directory in which to run]
#     [ARGS <arguments to executable>]
#     [OUTPUT_VARIABLE <var>]
#     [RETURN_VALUE <var>]
# )
# EXEC_PROGRAM(ls ARGS "*.c" OUTPUT_VARIABLE LS_OUTPUT RETURN_VALUE
# LS_RVALUE)
# IF(not LS_RVALUE)
# MESSAGE(STATUS "ls result: " ${LS_OUTPUT})
# ENDIF(not LS_RVALUE)
```



### FILE指令
FILE(WRITE filename "Message to write"...)
FILE(APPEND filename "Message to write"...)
FILE(READ filename variable)
FILE(GLOB variable [RELATIVE path] [globbing expression]...)
FILE(GLOB_RECURSE variable [RELATIVE path] [globbing expression]...)
FILE(REMOVE [directory]...)
FILE(REMOVE_RECURSE [directory]...)
FILE(MAKE_DIRECTORY [directory]...)
FILE(RELATIVE_PATH variable directory file)
FILE(TO_CMAKE_PATH path result)
FILE(TO_NATIVE_PATH path result)

### INCLUDE指令
INCLUDE(file [OPTIONAL])
INCLUDE(module [OPTIONAL])



```
# OPTIONAL 表示即使文件不存在，也不会产生错误
# 若定义的是一个模块，则将在CMAKE_MODULE_PATH中搜索并载入
```



### INSTALL指令

### FIND指令
FIND_FILE(<VAR> name1 path1 path2 ...)
找到文件的全路径，包含文件名

FIND_LIBRARY(<VAR> name1 path1 path2 ...)
找到库的全路径，包含库文件名



```
# exp:
# FIND_LIBRARY(libX X11 /usr/lib)
# IF(NOT libX)
#     MESSAGE(FATAL_ERROR "libX not found")
# ENDIF(NOT libX)
```



FIND_PATH(<VAR> name1 path1 path2 ...)
找到文件的路径

FIND_PROGRAM(<VAR> name1 path1 path2 ...)
找到程序的全路径

FIND_PACKAGE(<name> [major.minor][QUITE] [NO_MODULE]
    [[REQUIRED | COMPONENTS] [components ...]]
)


### IF指令
IF(var)，如果变量不是：空，0，N, NO, OFF, FALSE, NOTFOUND 
或<var>_NOTFOUND 时，表达式为真。

IF(expression)
COMMAND1(ARGS...)
COMMAND2(ARGS...)
...
ELSE(expression)
COMMAND1(ARGS...)
COMMAND2(ARGS...)
...
ENDIF(expression)

IF(NOT var)

IF(var1 AND var2)

IF(var1 OR varr2)

IF(COMMAND cmd)

IF(EXISTS dir/file)

IF(file1 IS_NEWER_THAN file2)

IF(IS_DIRECTORY dirname)

IF(variable MATCHES regex)

IF(string MATCHES regex)


IF("hello" MATCHES "ell")
MESSAGE("true")
ENDIF("hello" MATCHES "ell")


数字比较表达式
IF(variable LESS number)
IF(string LESS number)
IF(variable GREATER number)
IF(string GREATER number)
IF(variable EQUAL number)
IF(string EQUAL number)


按照字母序的排列进行比较
IF(variable STRLESS number)
IF(string STRLESS number)
IF(variable STRGREATER number)
IF(string STRGREATER number)
IF(variable STREQUAL number)
IF(string STREQUAL number)


IF(DEFINED variable)

IF(WIN32)
    MESSAGE(STATUS "This is Windows.")
ELSE
    MESSAGE(STATUS "This is not Windows.")
ENDIF



#### WHILE指令
WHILE(condition)
    COMMAND1(ARGS ...)
    COMMAND2(ARGS ...)
    ...
ENDWHILE(condition)

### 列表
FOREACH(loop_var arg1 arg2 ...)
    COMMAND1(ARGS ...)
    COMMAND2(ARGS ...)
    ...
ENDFOREACH(loop_var)

AUX_SOURCE_DIRECTORY(. SRC_LIST)
FOREACH(F ${SRC_LIST})
    MESSAGE(${F})
ENDFOREACH(F)

### 范围
FOREACH(loop_var RANGE total)
ENDFOREACH(loop_var)

FOREACH(VAR RANGE 10)
    MESSAGE(${VAR})
ENDFOREACH(VAR)

0
1
2
3
...

范围和步进
#### FOREACH(loop_var RANGE start stop [step])
#### ENDFOREACH(loop_var)



```
FOREACH(A RANGE 5 15 3)
    MESSAGE(${A})
ENDFOREACH(A)
5
8
11
14
```

