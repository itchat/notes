# Tkinter GUI 

## Creating windows and labels

tkinter 和 turtle 一样都是 python 中内置的模块，开箱即用

```python
import tkinter

# 创造一个窗口对象
window = tkinter.Tk()
window.title("GUI Program")
window.minsize(width=500, height=300)
my_label = tkinter.Label(text="I am a label.", font=("Times New Roman", 24, "bold"))
# pack 是一个几何管理系统，只有打包最终才能展现出内容，决定内容储存的位置
my_label.pack(expand=True)
# 开启窗口监听，while loop 监控用户操作
window.mainloop()
```

## Optional Arguments

### Many Positional Arguments

default arguments 限定了输入 arguments 的数量，但是 `*args` 可以做到无限 arguments 传参

```python
def add(n1, n2):
    return n1 + n2
```

以上这样简单的一个求和 function 只能提供两数相加，如果我需要多个参数传参求和，就需要借助 `*args`, 在 python 中 `args` 的数据结构为元组

```python
def add(*args):
    num_sum = 0
    for i in args:
        num_sum = num_sum + i
    return num_sum

print(add(3, 4, 5, 6, 7, 8, 88))
```

由于是元组，也可以直接使用元组的一些方法去调用

```python
print(args[0])
```

### Many Keyworded Arguments

`**kwargs` 用于解析字典类型的传参，用于用户指定的传参内容，如果你输入 `type(**kwargs)` 就会发现返回的是 `dict` 类型，由于是字典类型，所以依然可以调用字典的 function 进行操作

```python
def calculate(**kwargs):
    for k, v in kwargs.items():
        print(k, v)
    print(kwargs["add"])
    n += kwargs["add"]
    n *= kwargs["multiply"]
    print(n)
    

calculate(add=123)
```

创造一个多关键词参数 class

```python
class Car:

    def __init__(self, **kw):
        self.make = kw.get("make")
        self.model = kw.get("model")


my_car = Car(make="Nissan")
print(my_car.model, my_car.make)
```

python 中的字典的 get function 和 `dict["make"]` 这种方式不同，前者当不存在这种属性的时候只会返回 none 不会报错

在早期的 tkinter 的模块中会出现这种情况是因为 tk 原本是 UCB 一位学生创造的另外一种语言，python 对其进行了移植

## Label Changer

```python
import tkinter

window = tkinter.Tk()
window.title("My First GUI Program.")
window.minsize(width=500, height=300)

# Label
label = tkinter.Label(text="I am a Label", font=("Arial", 24, "bold"))
label.pack()

# 重覆盖更新标签与设置
label["text"] = "New Text"
label.config(text="New Text")

# Button

def button_click():
    # label.config(text="I have been changed")
    label.config(text=input.get())

button = tkinter.Button(text="click me", command=button_click)
button.pack()

# Entry
input = tkinter.Entry(width=10)
input.pack()

window.mainloop()
```

- [tkinter 模块参考文档](http://tcl.tk/man/tcl8.6/TkCmd/contents.htm)
- [tkinter 官方文档指南](https://docs.python.org/3.6/library/tkinter.html)

在此基础上写了一个 counter 工具，细化了排版与风格

```python
import tkinter

window = tkinter.Tk()
window.title("A Simple Counter")
window.minsize(width=500, height=300)

# 添加标签
label = tkinter.Label(text=0, font=("Times", 24, "bold italic underline"))
label.pack()


# Button
def button_click():
    label["text"] += 1


button = tkinter.Button(text="count", command=button_click, font=("Times", 8, "italic"))
button.pack(pady=10)

# Entry
user_input = tkinter.Entry(width=50, border=1)
user_input.pack()

window.mainloop()
```

以上就是 Label Button Entry

## Other Widgets

```python
from tkinter import *

# Creating a new window and configurations
window = Tk()
window.title("Widget Examples")
window.minsize(width=500, height=500)

# Entries, 条目，不可改变宽度只能改变长度
entry = Entry(width=30, font=("Times", 16, "bold italic underline"), justify="center", border=1)
# Add some text to begin with, END 代表 index 参数，光标从什么时候开始插入
entry.insert(END, string="Some text to begin with.")
# entry.get 可以打印出默认设置的 string
print(entry.get())
entry.pack(ipady=10, pady=10)

# Text, 真正意义上的文本框
text = Text(height=5, width=60, font="Times")
# 加入 cursor 光标
text.focus()
# Adds some text to begin with.
text.insert(END, "To Shuan, or not to Shuan, that is a question.")
# 从文本框中的第一行的第一个字符开始 print 出 text
print(text.get("1.1", END))
text.pack()


# Spinbox, 中文叫微调框


def spinbox_used():
    # gets the current value in spinbox.
    print(spinbox.get())


# 从 0 ~ 10 框体长度为 5
spinbox = Spinbox(from_=0, to=10, width=20, command=spinbox_used)
spinbox.pack(pady=10)


# Scale
# Called with current scale value.
def scale_used(value):
    print(value)


scale = Scale(from_=0, to=100, command=scale_used)
scale.pack()


# Checkbutton
def checkbutton_used():
    # Prints 1 if On button checked, otherwise 0.
    print(checked_state.get())


# variable to hold on to checked state, 0 is off, 1 is on.
checked_state = IntVar()
checkbutton = Checkbutton(text="Is On?", variable=checked_state, command=checkbutton_used)
checked_state.get()
checkbutton.pack()


# Radiobutton
def radio_used():
    print(radio_state.get())


# Variable to hold on to which radio button value is checked.
radio_state = IntVar()
radiobutton1 = Radiobutton(text="Option1", value=1, variable=radio_state, command=radio_used)
radiobutton2 = Radiobutton(text="Option2", value=2, variable=radio_state, command=radio_used)
radiobutton1.pack()
radiobutton2.pack()


# Listbox
def listbox_used(event):
    # Gets current selection from listbox
    print(listbox.get(listbox.curselection()))


listbox = Listbox(height=4)
fruits = ["Apple", "Pear", "Orange", "Banana"]
for item in fruits:
    listbox.insert(fruits.index(item), item)
listbox.bind("<<ListboxSelect>>", listbox_used)
listbox.pack()
window.mainloop()
```

## Layout Managers

有三个布局组件，`pack, place, grid` 如果一个组件没有用任何上面三个之一的布局设置，那么这个组件就不会被显示

- pack 总是从顶部开始，然后把所有的其他小组件打包

```python
label.place(x=0, y=0)
label.pack(side="left")
```

相较于 place 这种像素级管理方式，pack 的排版总是相较于模糊







