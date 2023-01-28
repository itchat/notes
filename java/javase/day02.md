# 第二章 Java 入门

## 1.0 运算符

其他还有 lambda 表达式 `->` 最后学习

### 1.0.1 逻辑

```java
& // logical AND
| // logical OR
！ // logical NOT
^ // logical XOR
&& // Short circuit AND
|| // Short circuit OR
```

#### a. logical XOR

 只有两边不同一个 true 一个 false 的时候才是 true

#### b. logical vs Short circuit AND/OR

&& 与 || 的速度与效率大于 & 和 |, 一旦左边是 true/false 就不再看右边的表达式

```java
public class Main {
    public static void main(String[] args) {
        int x = 1;
        int y = 1;
        // 不短路所以两边都对比，false and true -> false
        if (x++ == 2 & ++y==2){
            x = 7;
        }
        System.out.println(x + y);
    }
}
```

- 面试题

```java
public class Main {
    public static void main(String[] args) {
        boolean x = true;
        boolean y = false;
        short z = 42;
        if ((z++ == 42) && y == true)
            z++;
            // 这里虽然没有做计算但是 z 这个时候已经是 43
        if ((x = false) || (++z == 45))
            // 这里虽然没有验证通过但是 z 又被加了 1
            z++;
        System.out.println("z = " + z);
    }
}
```

### 1.0.2 条件

又称三元运算符

```java
// 条件表达式 ? 结果表达式一 : 结果表达式二
```

当某个操作是二选一的时候，可以免写 if else 语句

```java
public class Main {
    public static void main(String[] args) {
        int a = 1;
        int b = 2;
        int max = a >= b ? a : b;
        // max >= b 是否，是赋值 1, 不是赋值 2
        System.out.println(max);
        boolean marry = false;
        System.out.println(marry ? "true" : "false");
    }
}
```

### 1.0.3 位

```java
<< // 左移
>> // 右移
>>> // 无符号右移
& // 按位与
| // 按位或
~ // 按位取反
^ // 按位异或
```

位运算效率极高，一些软件源码可能会使用位运算，但是可读性不高

#### a. << 左移

```java
public class Main {
    public static void main(String[] args) {
        System.out.println(3 << 4); // 48
        /*
        三的二进制补码
        00000000 00000000 00000000 00000011 -> 左移四位后面补零
        0000 00000000 00000000 000000110000 -> 48
         */
    }
}
```

```java
/*  -3 << 48
    -3 的二进制原码：10000000 00000000 00000000 00000011
    -3 的二进制反码：11111111 11111111 11111111 11111100
    -3 的二进制补码：11111111 11111111 11111111 11111101
    左移四位后重新变回原码就构成了 -48
 */
```

- 到这里你可能发现了一个技巧：-3 << 4 = $-3 \times 2^4$ = -48

但是当左移的位数超过当前的数据类型的总位数：int 占 4 字节也就是 32 bit 的时候：

$3<<34=3<<2=3\times2^2=12$ **需要减去总位数，再进行计算**

#### b. >> 右移

右移几位，相当于除以 2 的几次方，同样位数一旦超过总位数也要进行减去再计算

- 但是负数需要注意

```java
/*
    -83 的二进制原码：10000000 00000000 00000000 01010011
    -83 的二进制反码：11111111 11111111 11111111 10101100
    -83 的二进制补码：11111111 11111111 11111111 10101101
    -83 >> 4 右边减去四位，左边根据最左边的 sign 位加上对应的值 ->
    111111111111 11111111 11111111 1010 补码
    111111111111 11111111 11111111 1001 反码
    100000000000 00000000 00000000 0110 原码
 */
```

按照公式来计算的时候是 $-83/2^4 = -5.x$ 按照 int 向下取整的原则，负数变成了 -6

#### c. >>> 无符号右移

正数和 >> 一样但是负数移动补位的时候是不管你最左边是什么数字的，统一补上 0

#### d. & 和 | 和 ^

按位与和按位或，在二进制阶段 0 与 1 互相比较进行 or 与 and 计算

```java
/* 
	3 & 4
	24 个0 00000011
	24 个0 00000100
	最后得出00000000
*/
```

按位或也一样，^ 就是按位的 xor，只有不一样的时候才是 true

```java
/* 
	3 ^ 4
	24 个0 00000011
	24 个0 00000100
	最后得出00000111
*/
```

#### e. ~ 按位取反

~ 在二进制中进行 not 操作

```java
System.out.println(~4);
/* 
	24 个0 00000100 补码状态 -> 取反
	24 个1 11111011 ~4 补码转原码
	1 23 个0 00000101 变成了 -5
*/
```

### 1.0.4 赋值

```java
+= // 左边加右边最后赋值给左边
%= // 左模右最后赋值给左边
<<= // 左边左移右边最后赋值左边
```

以此类推

### 1.0.5 Operator

1. `/` 对于除法，整数相除，结果只取整数部分；`1/0` 结果会发生 Arithmetic 异常，但是对于小数 `1.1/0` 相除，会得到无穷大
2. `%` 对于取模，`5.6%1.2` 最终是无限循环小数，最终进行截断；`5.6%0.0` 最终得到的是 `NaN`

#### a. 自增与自减

如果是自增与自减表达式是独立的，`i--` 与 `--i` 无区别

但是如果自增与自减表达式是其他计算的一部分，那么有区别

```java
// ++ 在前，自增变量先自增 1 然后去除自增变量的值后再进行其他运算
// ++ 在后，先去除自增变量的值，放在操作数 Stack[临时储存的地方]，然后自增变量自增 1，取栈中的值运算

int i = 1;
i++; // 这样叫单独独立出现
System.out.println(i);
    
int j = 1;
System.out.println(j++);
    
// 1. 由于参加了其他算数的运算，是先取 j 的 value 也就是 1 到操作数栈中
// a. 然后自增变成了 2, 但是最后取的是操作栈中的值赋给 j 这个变量变成了 1
    
int j = 1;
System.out.println(++j);

// 2. 先自增变 2 再放入 Stack 中最后取出赋值，也就是 2
```

```java
// Prac a
int m = 1;
m = ++m; 
// 由于 + 参与了其他运算，有变化；由于在前先自增成 2 赋值给 m
System.out.println("m = " + m)
```

对于 Prac a 这段的疑惑可以直接使用 `javap` 工具对 `class` 代码进行解析，查看编译后的具体

```java
javap -c TestAdd.class
// 0. iconst_1 加载常量 1 到内存
// 1. istore_1 给 m 赋值
// 2. iinc 自增
// 3. iload_1 取出 m 的值
// 4. istore_1 给 m 赋值
// 5. return 返回

// 如果 m = ++m; 变成了 m++, 2 和 3 的顺序则会颠倒
```

#### b. Practices

- 取个十百千万

```java
// 核心算法即 num / 1 - 1000 % 10
int i = 1234;
System.out.println(i / 1 % 10);
System.out.println(i / 10 % 10);
System.out.println(i / 100 % 10);
System.out.println(i / 1000 % 10);
```

- 自增减混合运算

```java
public class Main {
    public static void main(String[] args) {
        int i = 1;
        int j = i++;
        // i++ 没有独立，启动赋值算法：j = 1, i =2
        System.out.println(i++ * ++j + ++i * j++);
        /* (i++[i = 3 ] = 2) + (++j[j = 2] = 2) +
           (++i[i = 4] = 4) * (j++[j = 3] = 2) = 2*2 + 4*2 = 12
         */
    }
}
```

- 动态赋值

```java
// == 用于判断是否相等，= 是赋值
public class Main {
    public static void main(String[] args) {
        boolean flag = false;
        System.out.println(flag = true);
    }
}
```

Java 会先赋值再打印

video 22







