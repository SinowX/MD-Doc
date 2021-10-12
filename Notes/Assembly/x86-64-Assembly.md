## 控制

- | 指令      | 效果           | 描述         |
  | --------- | -------------- | ------------ |
  | leaq S, D | $D \gets \&S $ | 加载有效地址 |
  | INC D     | $D \gets D+1 $ |              |
  | DEC D     | $D \gets D-1 $ |              |
  | NEG D     | $D \gets -D  $ |              |
  | NOT D     | $D \gets ~D  $ |              |
  | ADD S, D  | $D \gets D+S $ |              |
  | SUB S, D  | $D \gets D-S $ |              |
  | IMUL S, D | $D \gets D*S $ |              |
  | XOR S, D  | $D \gets D\^S $ |              |
  | OR S, D   | $D \gets D|S $ |              |
  | AND S, D  | $D \gets D\&S $ |              |
  | SAL k, D  | $D \gets D<<k $ |              |
  | SHL k, D  | $D \gets D<<k $ |              |
  | SAR       | $D \gets D>>_A k $​ |              |
  | SHR k, D  | $D \gets D>>_L k $ |              |

  
  

### 条件码

- CPU维护着一组单个位的 condition code 寄存器，其描述了最近的算数或逻辑操作的属性

- 最常用的条件码有
  - CF：进位标志
    - 最近的操作使最高位产生了进位
    - 可用于检查无符号操作的溢出
  - ZF：零标志
    - 最近的操作的出的结果为0
  - SF：符号标志
    - 最近的操作得到的结果为负数
  - OF：溢出标志
    - 最近的操作导致一个补码溢出，正溢出或负溢出
  
- 上表中，除了 `leaq` 以外的所有指令，都会设置条件码

- 还有两类指令

  - | 指令              | 基于        | 描述 |
    | ----------------- | ----------- | ---- |
    | CMP $S_1$, $S_2$  | $S_2 - S_1$ | 比较 |
    | TEST $S_1$, $S_2$ | $S_1\&S_2$  | 测试 |

  - 对应不同大小的操作数，cmpb、cmpw ...



### 访问条件码

- 使用方法
  - 可以根据条件码的某种组合，将一个字节设置为0或1
  - 可以条件跳转到程序的某个部分
  - 可以有条件地传送数据

| 指令    | 同义名 | 效果 | 设置条件   |
| ------- | ------ | ---- | ---------- |
| sete D  | setz   |      | 相等/零    |
| setne D | setnz  |      | 不等/非零  |
| sets D  |        |      | 负数       |
| setns D |        |      | 非负数     |
| 有符号  |        |      |            |
| setg D  | setnle |      | 大于       |
| setge D | setnl  |      | 大于等于   |
| setl D  | setnge |      | 小鱼       |
| setle D | setng  |      | 小于等于   |
| 无符号  |        |      |            |
| seta D  | setnbe |      | 超过       |
| setae D | setnb  |      | 超过或相等 |
| setb D  | setnae |      | 低于       |
| setbe D | setna  |      | 低于或相等 |



