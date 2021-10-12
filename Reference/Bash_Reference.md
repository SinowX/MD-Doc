# Bash Reference

## Run a Shell Script

1. As an Executable File

   ```bash	
   chmod +x ./test.sh
   ./test.sh
   ```

2. With Interpreter

   ```bash
   /bin/sh test.sh
   ```

   

## Variable

* Type:
  * 局部变量
  * 环境变量
  * shell变量
    * 由shell程序设置的变量，一部分为环境变量，一部分为局部变量，保证shell正常运行

```bash
//Define a variable
name=value

//Apply a variable
echo $name
echo "I am ${name} in the flesh"

//Read Only Variable
myUrl="https://sinow.top"
readonly myUrl	//myUrl can not be change now

//Delete a Variable
uset name
```

### String

#### Single Quotation

```bash
str='this is a string'
```

- 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
- 单引号字串中不能出现单独一个的单引号（对单引号使用转义符后也不行），但可成对出现，作为字符串拼接使用。

#### Double Quotation

```bash
str2="Let's print str: \"$str\" ! \n"
echo str2
```

```shell
Let's print str: "this is a string" !
```

- 双引号里可以有变量
- 双引号里可以出现转义字符

#### Concat

```bash
name='Sinow'
tmp1="Hello, "$name" !"
tmp2="Hello, ${name} !"
tmp3='Hello, '$name' !'
tmp4='Hello, ${name} !'
echo $tmp1
echo $tmp2
echo $tmp3
echo $tmp4
```

```shell
Hello, Sinow !
Hello, Sinow !
Hello, Sinow !
Hello, ${name} !
```

#### Length

```bash
str="abcd"
echo ${#string}
```

```shell
4
```

#### Substring

```bash
str='abcdefg'
echo ${str:1:4}
```

```shell
bcde
```

#### Search

```bash
str='abcdefg'
echo `expr index "$string" c'//查找'c'的位置，返回第一个匹配位置
```

```shell
3
```

### Array

#### Create an Array

```bash
array_name=(value0 value1 value2 value3)

array_name=(
value0
value1
value2
value3
)

array_name[0]=value0
array_name[1]=value1
array_name[n]=valuen
```

#### Access

```bash
${array_name[index]}

${array_name[@]}返回数组中所有元素
```

#### Length

```bash
${#array_name[@]}
${#array_name[*]}
//均返回数组长度

${#array_name[index]}
//返回array_name[index]的长度
```

### Annotation

```bash
#单行注释

:<<EOF
多行注释
EOF

```



## Transfer Parameters

| 参数处理 | 说明                                                         |
| :------- | :----------------------------------------------------------- |
| $n       | 返回第n个参数                                                |
| $#       | 传递到脚本的参数个数                                         |
| $*       | 以一个单字符串显示所有向脚本传递的参数。 如`$*`用「"」括起来的情况、以`$1` `$2` … `$n`的形式输出所有参数。 |
| $$       | 脚本运行的当前进程ID号                                       |
| $!       | 后台运行的最后一个进程的ID号                                 |
| $@       | 与`$*`相同，但是使用时加引号，并在引号中返回每个参数。 如`$@`用「"」括起来的情况、以`$1` `$2`… `$n` 形式输出所有参数。 |
| $-       | 显示Shell使用的当前选项，与 set 命令功能相同。               |
| $?       | 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。 |

* `$*`与`$@`的区别
  * 只有在双引号中体现出来。假设在脚本运行时写了三个参数 1、2、3，，则 " * " 等价于 "1 2 3"（传递了一个参数），而 "@" 等价于 "1" "2" "3"（传递了三个参数）

```bash
#test.sh
echo "Shell 传递参数实例！";
echo "第一个参数为：$1";

echo "参数个数为：$#";
echo "传递的参数作为一个字符串显示：$*";
```

```shell
chmod +x test.sh 
./test.sh 1 2 3
Shell 传递参数实例！
第一个参数为：1
参数个数为：3
传递的参数作为一个字符串显示：1 2 3
```



## Operator

### Arithmetic

| 运算符 | 说明                                          | 举例                          |
| :----- | :-------------------------------------------- | :---------------------------- |
| +      | 加法                                          | `expr $a + $b` 结果为 30。    |
| -      | 减法                                          | `expr $a - $b` 结果为 -10。   |
| *      | 乘法                                          | `expr $a \* $b` 结果为  200。 |
| /      | 除法                                          | `expr $b / $a` 结果为 2。     |
| %      | 取余                                          | `expr $b % $a` 结果为 0。     |
| =      | 赋值                                          | a=$b 将把变量 b 的值赋给 a。  |
| ==     | 相等。用于比较两个数字，相同则返回 true。     | [ $a == $b ] 返回 false。     |
| !=     | 不相等。用于比较两个数字，不相同则返回 true。 | [ $a != $b ] 返回 true。      |

* 条件表达式要放在方括号之间，并且要有空格，例如: **[$a==$b]** 是错误的，必须写成 **[ `$a` == `$b `]**
* 乘号`*`前边必须加反斜杠`\`才能实现乘法运算

### Relation

| 运算符 | 说明                                                  | 举例                           |
| :----- | :---------------------------------------------------- | :----------------------------- |
| -eq    | 检测两个数是否相等，相等返回 true。                   | [ `$a` -eq `$b`] 返回 false。  |
| -ne    | 检测两个数是否不相等，不相等返回 true。               | [ `$a` -ne `$b` ] 返回 true。  |
| -gt    | 检测左边的数是否大于右边的，如果是，则返回 true。     | [ `$a` -gt `$b` ] 返回 false。 |
| -lt    | 检测左边的数是否小于右边的，如果是，则返回 true。     | [ `$a` -lt `$b` ] 返回 true。  |
| -ge    | 检测左边的数是否大于等于右边的，如果是，则返回 true。 | [ `$a` -ge `$b` ] 返回 false。 |
| -le    | 检测左边的数是否小于等于右边的，如果是，则返回 true。 | [ `$a` -le `$b` ] 返回 true。  |

* `$a`=10, `$n`=20
* 仅支持数字，不支持字符串，除非字符串的值为数字

### Boolean

| 运算符 | 说明                                                | 举例                                         |
| :----- | :-------------------------------------------------- | :------------------------------------------- |
| !      | 非运算，表达式为 true 则返回 false，否则返回 true。 | [ ! false ] 返回 true。                      |
| -o     | 或运算，有一个表达式为 true 则返回 true。           | [ `$a` -lt 20 -o `$b` -gt 100 ] 返回 true。  |
| -a     | 与运算，两个表达式都为 true 才返回 true。           | [ `$a` -lt 20 -a `$b` -gt 100 ] 返回 false。 |

* `$a`=10,  `$n`=20

### Logic

| 运算符 | 说明       | 举例                                          |
| :----- | :--------- | :-------------------------------------------- |
| &&     | 逻辑的 AND | [[ `$a` -lt 100 && `$b` -gt 100 ]] 返回 false |
| \|\|   | 逻辑的 OR  | [[ `$a` -lt 100 || `$b` -gt 100 ]] 返回 true  |

### String

| 运算符 | 说明                                         | 举例                     |
| :----- | :------------------------------------------- | :----------------------- |
| =      | 检测两个字符串是否相等，相等返回 true。      | [ $a = $b ] 返回 false。 |
| !=     | 检测两个字符串是否相等，不相等返回 true。    | [ $a != $b ] 返回 true。 |
| -z     | 检测字符串长度是否为0，为0返回 true。        | [ -z $a ] 返回 false。   |
| -n     | 检测字符串长度是否不为 0，不为 0 返回 true。 | [ -n "$a" ] 返回 true。  |
| $      | 检测字符串是否为空，不为空返回 true。        | [ $a ] 返回 true。       |

* `$a`="abc",  `$n`="efg"

### File Test

* 用于检测 Unix 文件的各种属性

| 操作符  | 说明                                                         | 举例                      |
| :------ | :----------------------------------------------------------- | :------------------------ |
| -b file | 检测文件是否是块设备文件，如果是，则返回 true。              | [ -b $file ] 返回 false。 |
| -c file | 检测文件是否是字符设备文件，如果是，则返回 true。            | [ -c $file ] 返回 false。 |
| -d file | 检测文件是否是目录，如果是，则返回 true。                    | [ -d $file ] 返回 false。 |
| -f file | 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。 | [ -f $file ] 返回 true。  |
| -g file | 检测文件是否设置了 SGID 位，如果是，则返回 true。            | [ -g $file ] 返回 false。 |
| -k file | 检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。  | [ -k $file ] 返回 false。 |
| -p file | 检测文件是否是有名管道，如果是，则返回 true。                | [ -p $file ] 返回 false。 |
| -u file | 检测文件是否设置了 SUID 位，如果是，则返回 true。            | [ -u $file ] 返回 false。 |
| -r file | 检测文件是否可读，如果是，则返回 true。                      | [ -r $file ] 返回 true。  |
| -w file | 检测文件是否可写，如果是，则返回 true。                      | [ -w $file ] 返回 true。  |
| -x file | 检测文件是否可执行，如果是，则返回 true。                    | [ -x $file ] 返回 true。  |
| -s file | 检测文件是否为空（文件大小是否大于0），不为空返回 true。     | [ -s $file ] 返回 true。  |
| -e file | 检测文件（包括目录）是否存在，如果是，则返回 true。          | [ -e $file ] 返回 true。  |

- **-S**: 判断某文件是否 socket。
- **-L**: 检测文件是否存在并且是一个符号链接。



## echo

```bash
echo 'This is a String'
echo This is a String

echo "\" This is a String \""
echo \" This is a String \"

echo "$name This is a String"

echo -e "OK! \n" #-e 开启转义

echo -e "OK! \c"	#\c不换行，即下一个输出语句在本行输出
echo "This is a String"

echo "This is a String" > file	#定向输出到file

echo '$name \"'	#单引号输出原字符串，无变量与转移功能

echo `date`	#显示命令执行结果
```



## printf

```bash
printf  format-string  [arguments...]
```

* format-string ：为格式控制字符串
* arguments ： 为参数列表

```bash
$ echo "Hello, Shell"
Hello, Shell
$ printf "Hello, Shell\n"
Hello, Shell

printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg  
printf "%-10s %-8s %-4.2f\n" 郭靖 男 66.1234 
printf "%-10s %-8s %-4.2f\n" 杨过 男 48.6543 
printf "%-10s %-8s %-4.2f\n" 郭芙 女 47.9876 
```

### 转移序列

| 序列  | 说明                                                         |
| :---- | :----------------------------------------------------------- |
| \a    | 警告字符，通常为ASCII的BEL字符                               |
| \b    | 后退                                                         |
| \c    | 抑制（不显示）输出结果中任何结尾的换行字符（只在%b格式指示符控制下的参数字符串中有效），而且，任何留在参数里的字符、任何接下来的参数以及任何留在格式字符串中的字符，都被忽略 |
| \f    | 换页（formfeed）                                             |
| \n    | 换行                                                         |
| \r    | 回车（Carriage return）                                      |
| \t    | 水平制表符                                                   |
| \v    | 垂直制表符                                                   |
| \\    | 一个字面上的反斜杠字符                                       |
| \ddd  | 表示1到3位数八进制值的字符。仅在格式字符串中有效             |
| \0ddd | 表示1到3位的八进制值字符                                     |







## test

### Number Test

| 参数 | 说明           |
| :--- | :------------- |
| -eq  | 等于则为真     |
| -ne  | 不等于则为真   |
| -gt  | 大于则为真     |
| -ge  | 大于等于则为真 |
| -lt  | 小于则为真     |
| -le  | 小于等于则为真 |

```bash
num1=100
num2=100
if test $[num1] -eq $[num2]
then
    echo '两个数相等！'
else
    echo '两个数不相等！'
fi
```

* `[]` 内可执行基本算术运算

```bash
a=5
b=6

result=$[a+b] # 注意等号两边不能有空格
echo "result 为： $result"
```

### String Test

| 参数      | 说明                     |
| :-------- | :----------------------- |
| =         | 等于则为真               |
| !=        | 不相等则为真             |
| -z 字符串 | 字符串的长度为零则为真   |
| -n 字符串 | 字符串的长度不为零则为真 |

```bash
num1="ru1noob"
num2="runoob"
if test $num1 = $num2
then
    echo '两个字符串相等!'
else
    echo '两个字符串不相等!'
fi
```









## Function

```bash
[ function ] funname [()]

{

    action;

    [return int;]

}
```

* 函数返回值用 `$?` 获取

```bash
funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73
```

| 参数处理 | 说明                                                         |
| :------- | :----------------------------------------------------------- |
| $#       | 传递到脚本或函数的参数个数                                   |
| $*       | 以一个单字符串显示所有向脚本传递的参数                       |
| $$       | 脚本运行的当前进程ID号                                       |
| $!       | 后台运行的最后一个进程的ID号                                 |
| $@       | 与$*相同，但是使用时加引号，并在引号中返回每个参数。         |
| $-       | 显示Shell使用的当前选项，与set命令功能相同。                 |
| $?       | 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。 |



## Process Control

### if

```bash
if condition
then
    command1 
    command2
    ...
    commandN 
fi
```



### if else

```bash
if condition
then
    command1 
    command2
    ...
    commandN
else
    command
fi
```

```shell
if [ $(ps -ef | grep -c "ssh") -gt 1 ]; then echo "true"; fi
```



### if else-if else

```bash
if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi
```

### for

```bash
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done
```

```shell
for var in item1 item2 ... itemN; do command1; command2… done;
```



### while

```bash
while condition
do
    command
done
```

### until

```bash
until condition
do
    command
done
```

### case

```bash
case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2）
    command1
    command2
    ...
    commandN
    ;;
esac
```

```bash
echo '输入 1 到 4 之间的数字:'
echo '你输入的数字为:'
read aNum
case $aNum in
    1)  echo '你选择了 1'
    ;;
    2)  echo '你选择了 2'
    ;;
    3)  echo '你选择了 3'
    ;;
    4)  echo '你选择了 4'
    ;;
    *)  echo '你没有输入 1 到 4 之间的数字'	#`*`最后捕获
    ;;
esac
```

### break continue



## Pip

| 命令            | 说明                                               |
| :-------------- | :------------------------------------------------- |
| command > file  | 将输出重定向到 file。                              |
| command < file  | 将输入重定向到 file。                              |
| command >> file | 将输出以追加的方式重定向到 file。                  |
| n > file        | 将文件描述符为 n 的文件重定向到 file。             |
| n >> file       | 将文件描述符为 n 的文件以追加的方式重定向到 file。 |
| n >& m          | 将输出文件 m 和 n 合并。                           |
| n <& m          | 将输入文件 m 和 n 合并。                           |
| << tag          | 将开始标记 tag 和结束标记 tag 之间的内容作为输入。 |

* 默认情况下

  * command >file 将 stdout 重定向到file

  * command <file 将 stdin 重定向到file

  * ```bash
    command 2>file	#stderr重定向到file
    
    command 2>>file	#stderr追加到file文件末尾
    
    command >file 2>&1	#stdout, stderr 合并后重定向到file
    command >>file 2>&1
    
    command < file1 >file2 将 stdin 重定向到 file1，将 stdout 重定向到 file2
    ```

* Here Document

  * ```bash
    command << delimiter
        document
    delimiter
    #结尾的delimiter 一定要顶格写，前面不能有任何字符，后面也不能有任何字符，包括空格和 tab 缩进
    #开始的delimiter前后的空格会被忽略掉
    ```

  * 将两个 delimiter 之间的内容(document) 作为输入传递给 command

* /dev/null

  * ```bash
    command > /dev/null
    ```

  * 禁止输出作用

  * ```bash
    command > /dev/null 2>&1
    #屏蔽stdout, stderr
    ```

## Include File

```bash
. $dir/$filename
source $dir/$filename
均可导入脚本文件
```

