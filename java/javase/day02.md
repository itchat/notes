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

### 1.0.2 条件

又称三元运算符

```java
// 条件表达式 ? 结果表达式一 : 结果表达式二
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

#### b. Practice

```java
// 取个十百千万，核心算法即 num / 1 - 1000 % 10

int i = 1234;
System.out.println(i / 1 % 10);
System.out.println(i / 10 % 10);
System.out.println(i / 100 % 10);
System.out.println(i / 1000 % 10);
```

```java
// ++ 与 -- 混合运算

int i = 1;
int j = i++; 
// i++ 没有独立，启动赋值算法：j = 1, i =2
System.out.println(i++ * ++j + ++i * j++);
// (i++[i = 3 ] = 2) + (++j[j = 2] = 2) + (++i[i = 4] = 4) * (j++[j = 3] = 2) = 2*2 + 4*2 = 12
```

video 15









