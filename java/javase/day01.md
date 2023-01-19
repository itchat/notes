# 第一章 Java 概述

JavaSE 全称 Java Platform, Standard Edition, 主要适用于桌面系统平台的标准版.

## 1.0 安装

在 Oracle 官网登录账号下载 [JDK8](https://www.oracle.com/java/technologies/downloads/) 后安装，设置环境表变量

在 Windows 的环境变量配置中上半部分是用户变量，下方是全局变量；为了避免造成后期用户权限问题在下方进行更改

1. 配置全局变量中的 Path 新增 bin 目录用于执行配置

```bash
D:\JDK8\bin
```

2. 新增全局变量 JAVA_HOME 用于标注 Java 主目录路径

```bash
D:\JDK8
```

## 1.1 跨平台

特定指令集编译器将 .java 文件编译为字节码 .class 文件 -> 特定指令集的 JVM 作为解释器运行 .class 文件

由此，有了较为容易的字节码的辅助，原本高级语言 -> 汇编语言 -> 机器语言的工作需要跨平台 mxn 种设计，现在只需要 m+n 种设计即可实现多种语言多台不同架构的机器上运行

### 1.1.1 JVM

1. Java Virtual Machine 是运行所有 Java 程序的虚拟机，是 Java 程序的运行环境之一，所有编写的代码都运行于 JVM 之上
2. Java Runtime Enviroment 也就是 JRE 是 Java 程序的运行环境，包含 JVM 和运行时所需要的核心类库
3. Java Development's Kit 时 Java 程序开发工具包，包含 JRE 和其他开发工具

## 1.2 文件名风格

1. Windows 编译过程中目录下的文件名大小写不区分，但 Linux 严格区分
2. 当 class 为 public 的时候，文件名要和 class 名一致；如果非 public, 文件名与 class 名可以不一致但不方便管理

当一个文件种有多个 class 的时候运行会生成多个 .class 文件，文件与类名一般采用大驼峰命名法

## 1.3 Annotations

```java
// 单行注释
/* 多行注释 */
```

接口风格注释

```java
/**
 * Java Doc
 * 
 * @author Galahad Sir
 * @version 1.0
 * @since 2019-10-10
 */

public class Intro {
    /**
     * This is Main function
     * 
     * @param args
     */
    public static void main(String[] args) {

        System.out.println("Hello World");

    }
}
```

具体可在日后学习写接口的时候完善

### 1.3.1. JavaDoc

```bash
javadoc -help
javadoc [options] [packagenames] [sourcefiles] [@files]
javadoc -author -d doc Comments.java
```

## 1.4 Java Identifier

Java 一共有五十个关键字，包含 C 语言兼容关键字，全部小写

标识符是自己命名的部分，命名规则如下：

### 1.4.1 硬性规则

1. Java的 标识符 只能使用 26 个英文字母大小写，0-9 的数字，下划线 _，美元符号 $
2. 不能使用 Java 的关键字（包含保留字）和特殊值
3. 数字不能开头
4. 不能包含空格
5. 严格区分大小写

### 1.4.2 命名规范

1. 见名知意

2. 类名、接口名等：每个单词的首字母都大写，形式：XxxYyyZzz，

例如：HelloWorld，String，System 等

3. 变量、方法名等：从第二个单词开始首字母大写，其余字母小写，形式：xxxYyyZzz，

例如：age,name,bookName,main

4. 包名等：每一个单词都小写，单词之间使用点.分割，形式：xxx.yyy.zzz，

例如：java.lang

5. 常量名等：每一个单词都大写，单词之间使用下划线_分割，形式：XXX_YYY_ZZZ，

例如：MAX_VALUE,PI

**更多细节详见《代码整洁之道.pdf》《Java开发手册（泰山版）》**

## 1.5 Data Type

Java 的数据类型分为两大类

1. **基本数据类型**

整数：byte, short, int, long

小数：float, double

单字符：char

布尔型：boolean 

2. **引用数据类型**

类 class, 接口 interface, 枚举 enum, 注解 @interface, 数组 []

## 1.6 Constant

常量值，程序运行期间无法改变：比如 18 是一个常量

```java
public class ConstantValue{
    public static void main(String[] args){
        System.out.println(1) // 识别为 int
        System.out.println(1L) // 识别为 long, 数字后一般跟 l 或 L
        System.out.println(1.5) // 识别为 double
        System.out.println(1.5F) // 识别为 float, 数字后跟 f 或 F
        System.out.println('a') // 识别为 char
        System.out.println("helloworld") // 识别为 String 因为是双引号
    }
}
```

## 1.7 Variables

变量，也就是程序运行期间能够改变的量：比如 age 

1. 变量必须先申明后使用
2. 变量在使用前必须初始化
3. 变量申明类型与值必须一致或兼容

```java
public class Variable{
    public static void main(String[] args){
        int age = 18.5;
        System.out.println(age);
    }
}
```

### 1.7.1 Final

最终变量又被称为常量，无论一开始赋值多少最后都无法再被修改。所有字母大写，每一个单词中间使用下划线分割

```java
class Main {
	public static void main(String[] args){
        final int FULL_MARK = 100;
        System.out.println("满分：" + FULL_MARK);
        int wang = FULL_MARK - 1;
        int shang = FULL_MARK;
        int liu = FULL_MARK/2;
        System.out.println("小王成绩：" + wang);
        System.out.println("小尚成绩：" + shang);
        System.out.println("小刘成绩：" + liu);
	}
}
```

### 1.7.2 转义字符

```java
\n：换行
\r：回车
\t：Tab键
\\：\
\"："
\'：'
\b：删除键Backspace
```

## 1.8 Numerical System

十进制 0~9, 二进制 0~1, 八进制 0~7, 十六进制 0~9, a~f；具体的数字转换机制直接看离散数学前两章

### 1.8.1 计算机中表示四种进制

1. 十进制：正常表示

`System.out.println(10);`

2. 二进制：0b 或 0B 开头

`System.out.println(0B10);`

3. 八进制：0 开头

`System.out.println(010);`

4. 十六进制：0x或0X开头

`System.out.println(0X10);`

### 1.8.2 Java 数据类型储存范围

![image-20221227161310875](images\day01data_type_range.png)

### 1.8.3 True Form, One's, Two's

原码补码反码与符号位 signed 与 unsigned 概念

计算机数据用二进制补码也就是 signed two's complement 储存，最高位为符号位，1 代表负数 0 代表正数

正数的补码与反码原码均为一致，但负数之间的转换：

```txt
负数：-25  10000000 00000000 000000000 00011001（原码）
负数：-25  11111111 11111111 111111111 11100110（反码）
负数：-25  11111111 11111111 111111111 11100111（补码）
```

反码在源码的基础上除了符号位其他全部取反，补码在反码的基础上加一

## 1.9 基本数据类型转换

### 1.9.1 自动类型/隐式转换

Implicit Conversion 多种数据类型数据混合运算的时候，小类型自动转换为取值范围大的类型

```java
int i = 1;
byte b = 1;
double d = 1.0;
double sum = i + b + d;
// 混合运算会直接升级为最大的那个，所以要注意 sum 要用 double 声明
```

byte -> short/char -> int -> long -> float -> double

boolean 类型不参与自动转换，**short 与 char 之间由于字节相同也无法互相进行转换**

自动类型转换根据顺序提升，double + float 会报错，只能由大到小人工排序好再相加

#### a. 特殊情况

byte 与 short 与 char 混合运算时，**会自动提升为 int 类型**，因为底层没有设计 byte 与 short 类型指令

```java
byte b1 = 1;
short b2 = 2;
int b3 = b1 + b2;
```

### 1.9.2 显示/强制类型转换

强行把大数据类型转成小数据类型，byte 加上 short 后实际上会被自动提升为 int 类型，现在给他强转成 byte

```java
byte b1 = 127;
short b2 = 2;
int b3 = b1 + b2;
byte b4 = (byte)(b1 + b2)
// b4 最后结果是 -127
```

```txt
00000000 00000000 00000111 00001111 [127]
00000000 00000000 00000000 00000010 [2]
00000000 00000000 00001000 10000001 按照 int 类型是 129

但是这实际上是 byte 类型，进行了截断，失去了 1000 只得到了补码 1000 0001 
最高位是负数 sign 位是 1 转换成反码到原码后变成了 -127 最终造成了溢出
```

```txt
数据类型 变量名 = （数据类型）被强转数据值;
```

因此强制类型转换风险较高，不仅会造成溢出，还可能造成精度损失

```java
double i = 58.23245546;
// double 小数点后 15 ~ 16 位
float small = (float)i;
// float 是小数点后 7 ~ 8 位
System.out.println(small);
// 结果变成了 58.232456 系统自动会截断，进行约等于计算
```

#### a. 小范围类型主动提升

```java
int x = 1;
int y = 2;
System.out.println(x/y);
// 结果是 0, int 会自动向下兼容
System.out.println((double)x/y);
// 这个结果是 0.5, 把 x 转成 double 后再除以
System.out.println((double)(x/y));
// 这个结果是 0.0, 有了结果后再转 double
```

这种强制类型提升没有风险

**最终注意，无论是自动还是强制，布尔类型都不参与转换**

## 2.0 String 类型与基本数据类型转换

字符串在 Java 中不是基本数据类型

```java
char c1 = 'a';
char c2 = 'b';
System.out.println(c1 + c2);
// 195 这个结果在自动类型转换中讲过，char/short/byte 混合运算自动提示 int
System.out.println("c1" + c1 + c2);
// c1ab
System.out.println(c1 + c2 + "c1");
// 195c1
System.out.println(c1 + "" + c2);
// ab 任何与 String 进行 + 的拼接，最终结果都会是 String
```

