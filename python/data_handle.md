# Python Data Handling

## Debug

- 学会阅读允许错误注释，方法描述
- 当异常发生时，及时解决异常再继续编码
- 问题复现、描述问题提问在 Stack Overflow
- 用 `print` 截断，使用编译器 debugger 功能 line by line 执行
- `pythontutor.com` 以及 `sentry.io` 都可以做到分析，后者能监控运行中的异常

最后当问题长时间无法解决的时候，记得及时休息；或者求助朋友和 `discord` 社区等求助；在每编写一个模块的代码后都要进行单元测试，不要积累到最后时刻才统一提交

## Comprehension

### List

当我们使用 Python 给一个列表做循环操作的时候，往往需要写一大段代码实现，比如给一个列表的元素全部加 1

```python
num = [1,2,3]
n_list = []
for n in num:
    n += 1
    n_list.append(n)
```

直接写列表解析

```python
num = [1, 2, 3]
n_list = [n + 1 for n in num]
```

```python
name = "Angela"
n_list = [letter for letter in name]
```

在 Python 中 list, range, string, tuple 这些关键词全部被称为 Python Sequences 序列，并且能够被列表解析，因为他们有特定的顺序

- 用 range 写一个范围内的偶数

```python
letter_list = [letter for letter in range(0, 100, 2)]
```

- 加入 if 判断

```python
name = ['Jerry', 'Andy']
new_name = [name.upper() for name in name if len(name) > 4]
```

注：PyCharm 的 Python Console 可以调试 Special Variables, Ctrl + Shift + Alt + L 一件 reformat

- 列表偶数判断

```python
even_num = [i for i in num if i != 1 and i % 2 == 0]
```

```python
"""
新建两个 txt 文件
一个逐行写入 1 2 3 一个逐行写入 1 3 5，利用列表解析判断后者存在于前者的数
"""

with open('../1.txt', 'r+', encoding='utf-8') as f:
    file1_data = f.readlines()
file1_data = [i.replace("\n", "") for i in file1_data]

with open('../2.txt', 'r+', encoding='utf-8') as f:
    file2_data = f.readlines()
file2_data = [i.replace("\n", "") for i in file2_data]
result = [int(i) for i in file1_data if i in file2_data]
print(result)
```

### Dictionary

使用字典解析给下面的字典添加随机 value 生成一个字典

```python
names = ['Alex', 'Beth', 'Dave']
```

```python
import random

names = ['Marry', 'Jerry', 'James']
student_scores = {student: random.randint(1, 100) for student in names}
```

接下来给随机生成的字典做一个判断，哪些学生及格哪些没有

```python
passed_score = {student: score for (student, score) in student_scores.items() if score >= 60}
```

- 一句话判断整个字符串中所有的单词的长度

```python
sentence = "Hello Google what is the matter Google"
cal_word = {i: len(i) for i in sentence.split()}
```

由此可见无论是列表还是字典解析，针对列表的操作是全部写在前部的变量中

- Python 字典解析一句话完成华氏度转摄氏度

```python
weather_f = {day:(temp_c*9/5) + 32 for (day, temp_c) in weather_c.items()}
```

## Pandas

使用字典解析最大的问题是很难给出一个标准好看的格式，使用 `pandas` 库可以

```python
student_dict = {
    "student": ["Angela", "Tom", "Jerry"],
    "score": [56, 76, 98]
}

for (key, value) in student_dict.items()
    print(value)
```

原本只能 print 单个的 value 或者 key

```python
import pandas
student_dict = {
    "student": ["Angela", "Tom", "Jerry"],
    "score": [56, 76, 98]
}
student_data_frame = pandas.DataFrame(student_dict)
print(student_data_frame)
```

你会得到一个非常漂亮的表格式输出，这是因为 pandas 内部内置了一个方法 iterrows 逐行读取数据

```python
student_data_frame = pandas.DataFrame(student_dict)
for index, row in student_data_frame.iterrows():
    print(index, row.student, row.score)
```

- 使用 pandas 逐行读取 csv 文件然后转换成 `dict` 数据类型

```python
import pandas

data = pandas.read_csv(r"C:\Users\itcha\Desktop\nato_phonetic_alphabet.csv")
phonetic_dict = {row.letter: row.code for (index, row) in data.iterrows()}
print(phonetic_dict)
```

```txt
letter,code
A,Alfa
B,Bravo
```

csv 的格式大致如上，第一行的列表排位作为 variables 可以被 function 调用，然后使用字典解析即可，iterrows 方法必须要同时使用 index 和 row 两个变量，不然会报错
