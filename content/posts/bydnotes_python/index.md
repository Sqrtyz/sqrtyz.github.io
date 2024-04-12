---
title: 【杂记】Python 3 Getting Started
category: Notes
tag: Unfinished
date: 2024-01-01
---
> 强推菜鸟教程。本文由其整理而来。

### § 1 最基础语法

#### § 1.1 注释与输入输出

+ 基本样例

    ```python
    a = input("Input an interger, fucking kids!\n") # input

    print("The integer you just inputed is", a, "!") # output

    '''
    在这里写多行注释。
    '''

    """
    在这里写多行注释。
    """
    ```

+ 输出相关

    如果想在输出中加入变量，或者「一块一块」地输出，我们可以使用逗号进行分隔。例如：
    ```python
    a = "apples"
    b = 23
    print("There are", b, a, "on the table.")
    ```

    这个程序段将输出 `There are 23 apples on the table.`。

    此外，在 python 3.x 中，我们可以在 `print()` 函数中添加 `end = "???"` 参数，使得输出的末尾为 ???。
    
    Python 3 中，print 函数的 end 默认值为 `\n`，这也是为什么一般的 print 函数最后会默认换行。故而，我们可使用 `print("Content", end = "")` 语句达到不换行效果。

    「end 参数」可以与「逗号分隔」搭配使用，不过 end 参数只能出现在最后。


#### § 1.2 语句的换行、压行和缩进

和 C/C++ 的使用分号表示语句结尾不同，一般而言，Python 一行自成一条语句。

+ 如果需要在一行里写多条语句（压行），可以使用分号 `;` 进行分隔。在这种情况下，一行里面的最后一条语句最后依然不能有分号。例如：

    ```python
    f = b + c; g = d + e; h = f + g
    ```

    等价于

    ```python
    f = b + c
    g = d + e
    h = f + g
    ```

+ 如果需要让一条语句拓展到多行（反压行），可以使用 `+\` 连接。例如：
  
    ```python
    e = b +\
        c +\
        d
    ```

    等价于

    ```python
    e = b + c + d
    ```

+ 与在 C/C++ 中的用大括号表示层次不同，Python 中用缩进表示层次。例如：

    ```python
    a = 1
    if a = 1:
        do thing A
    if a = 2:
        do thing B
    ```

### § 2 基本数据类型

#### § 2.1 Python 变量简介

+ Python 中的变量不需要声明。每个变量在使用前都必须赋值，变量赋值以后该变量才会被创建。

  在 Python 中，变量就是变量，它没有类型，我们所说的"类型"是变量所指的内存中对象的类型。

  ```python
  counter = 100          # 整型变量
  miles   = 1000.0       # 浮点型变量
  name    = "runoob"     # 字符串
  
  print (counter)
  print (miles)
  print (name)
  ```
  
+ 在 Python 中，允许以下两种特殊的同时赋值：
  

  + 连续等号赋值
    
    ```python
    a = b = c = 1
    ```
    
    运行上述语句后，三个变量 a, b, c 的值均为 1。
  
  + 逗号分隔赋值

    ```python
    a, b, c = 1, 2, "runoob"
    ```
    
    运行上述语句后，a = 1, b = 2, c = `runoob`。

+ Python3 中有六个标准的数据类型：

  |Number| String| List| Tuple|Set|Dictionary|
  |---|---|---|---|---|---|
  |数字|字符串|列表|元组|集合|字典|

  其中：Number / String / Tuple 为 **不可变数据**。List /  Set / Dictionary 为 **可变数据**。

  > 当该数据类型对应的变量的值发生了变化时，如果它对应的内存地址不发生改变，那么这个数据类型就是 **可变数据类型**。当该数据类型对应的变量的值发生了变化时，如果它对应的内存地址发生了改变，那么这个数据类型就是 **不可变数据类型**。*（事实上，这一说法也并不完全准确。参见 2.10 节：Python 变量的本质）*

  接下来依次介绍这六个数据类型。

#### § 2.2 Number（数字）

Python 数字数据类型用于存储数值。Number 类型是 **不允许改变** 的：这就意味着如果改变数字数据类型的值，将重新分配内存空间。

+ Number 类型的创建和删除

    你只需要对一个 Number 进行初始化来进行创建。例如：

    ```python
    num1 = 1
    num2 = 3.0
    ```

    对一个 Number 进行删除可以使用 `del` 语句。例如：

    ```python
    del num1, num2
    ```
+ Number 的三种类型

  Number 具有三种类型，分别是 int（整型）、float（浮点型）、complex（复数）。以下是合法的三种类型的具体数值。


  | Number 类型 | 示例 |
  | --- | --- |
  | **int** | 10, 100, 0x69, 080 |
  | **float** | -3.2, 2.3e100, 70.2E-12 | 
  | **complex** | 3.14j, 2 + 0.6j |
  
  在 complex 中，用 `j` 来表示虚根 $i$。

+ Number 的强制类型转换

    使用 `int(x)` 将 $x$ 转换为一个整数。

    使用 `float(x)` 将 $x$ 转换到一个浮点数。

    使用 `complex(x)` 将 $x$ 转换到一个复数，实数部分为 $x$，虚数部分为 $0$。

    使用 `complex(x, y)` 将 $x$ 和 $y$ 转换到一个复数，实数部分为 $x$，虚数部分为 $y$。$x$ 和 $y$ 是数字表达式。

+ Number 执行运算的方式

    使用 `+`, `-`, `*`, `/` 对 Number 进行简单的四则运算。

    需要特别注意的是，`/` 的结果 **永远会返回一个 float**。如果你希望返回整除的结果，可以使用整除运算符 `//`。另外，`//` 返回的不一定是 int，当分子和分母有一个为 float 的时候，`//` 将返回 float。

    ```python
    a = 6 // 4
    print(a) # output 1
    b = 6.0 // 4
    print(b) # output 1.0
    ```

    此外，Python 可使用 `**` 进行幂运算。例如：

    ```python
    a = 3 ** 3
    print(a) # output 27
    b = 2 ** 0.5
    print(b) # output 1.414...
    ```

    不同类型的数混合运算时会将整数转换为浮点数。

+ 交互模式下的下划线
  
    在交互模式中，最后被输出的表达式结果被赋值给变量 _ 。例如：

    ```python
    >>> tax = 12.5 / 100
    >>> price = 100.50
    >>> price * tax
    12.5625
    >>> price + _
    113.0625
    >>> round(_, 2)
    113.06
    ```
    此处， _ 变量应被用户视为只读变量。

#### § 2.3 String（字符串）

+ 你需要知道的

    + Python 中 **单引号和双引号** 都可以用以定义字符串，这两者没有区别。
    + Python 中没有 **单字符类型（类别 C/C++ 的 char）**，取而代之的是长度为 $1$ 的字符串。

+ 字符串的基本用法

  + **创建**：直接为变量分配一个初始值即可。
  
      ```python
      str1 = "avs"
      str2 = 'dsafd'
      ```

  + **访问**：一种是通过 `str[i]` 访问下标为 $i$ 的字符（字符串的下标从 $0$ 起始），另一种是通过 `str[l:r]` 访问下标在 $l \sim (r - 1)$ 内的子字符串（左闭右开）。
      
    特别地，下标还可以进行负索引。例如 `str[-2]` 表示的是 str 的倒数第二个字符。例如：

    |str[]|e|a|r|t|h|
    |---|---|---|---|---|---|
    |顺索引|0|1|2|3|4|
    |负索引|-5|-4|-3|-2|-1|

    在这个实例中，`str[1] = str[-4] = 'a'`，`str[0:3] = str[-5:3] = "ear"` 。

  + **拼接**：使用字面意思的 `+` 和 `*` 符号对字符串进行拼接。例如：

      ```python
      str = "earth"
      print(str[-5:3] * 2 + 'e' + "a") # output: earearea
        ```

+ 字符串转义
    
    下面仅列举出一些常用的字符串转义符。


    | 转义字符 | 含义 |
    |---|---|
    | `\`（在行尾）| 续行符 |
    | `\\` | 反斜杠符号|
    | `\'` | 单引号|
    | `\"` | 双引号|
    | `\n` | 换行符|
    
    **[Attention]** 如果希望字符串中的转义全部失效，只需要在字符串的前引号前加上 `r` 或者 `R` 即可。例如：

    ```python
    str = r"\n\n\n"
    print(str) # output: \n\n\n
    ```

+ 字符串格式化

    类似于 C/C++ 中的 `%d` 和 `%s` 等参数，我们可以用这些小玩意儿作为其他变量的代替。
    
    与 C/C++ 中不同的是，分割用的逗号变成了百分号；此外，我们甚至可以把结果赋到一个字符串上。例如：

    ```python
    str = "abc %d %lf def%s" % (114, 514.0, 'www')
    print(str)
    ```

    这个程序段将输出 `abc 114 514.000000 defwww`。

+ 字符串内建函数

    以下是 String 类型常用的一些函数。


    |函数|意义|
    |---|---| 
    |`count(sub, beg = L, end = R)`|返回 sub 在 String 里面出现的次数，如果 beg 或者 end 指定则返回指定范围内 sub 出现的次数|
    |`find(sub, beg = L, end = R)`|检测 sub 是否包含在字符串中，如果指定范围 beg 和 end，则检查是否包含在指定范围内，如果包含返回开始的索引值，否则返回 `-1`|
    |`isalnum(S)`|如果字符串至少有一个字符并且所有字符都是【字母】或【数字】则返回 True，否则返回 False|
    |`isalpha(S)`|如果字符串至少有一个字符并且所有字符都是【字母】或【中文字符】则返回 True，否则返回 False|
    |`isdigit(S)`|如果字符串只包含【数字】则返回 True，否则返回 False|
    |`len(S)`|返回字符串长度|
    |`replace(old, new[, max])`|将字符串中的 old 替换成 new。如果 max 指定，则替换不超过 max 次。|

#### § 2.4 List（列表）

列表是最常用的 Python 数据类型。你可以将其与 C/C++ 中的数组类比。但有所不同的是，Python 中的列表是一个链式存储结构——在后面的操作中，你将会有更深的体会。

Python 中的列表的下标从 $0$ 开始。**列表的数据项不一定具有相同的类型。**

+ 基本操作
  
  + 初始化
    
    ```python
    list1 = [1926, 817]
    list2 = ["2132", 'rre']
    list3 = ["342", 2434.0]
    ```

    如果你希望建立一个长度为 $n$ 的全为数字 $0$ 的列表，你可以这样初始化（这本质上是 **列表推导式**，本节最后提及）。

    ```python
    a = [0 for i in range(100)]
    ```

  + 访问
    
    与 String 的索引方式一致。不管是访问特定下标，还是某个子列表。例如：

    ```python
    list = [1, 2, 3, 4, 5]
    a = list[3]
    b = list[4]
    c = list[-2]
    d = list[1: -2]
    print(a) # output 4
    print(b) # output 5
    print(c) # output 4
    print(d) # output [2, 3]
    ```

  + 更新
    
    通过 `list[x] = val` 将列表中第 $x$ 项的值改为 $val$。**请注意：$x$ 必须小于数组的长度！**

    通过 `list.append(val)` 向列表的末尾增加 $val$ 这个元素。

    通过 `del list[x]` 删除列表中第 $x$ 项的元素。

    例如：

    ```python
    a = [1, 2, 3, 4, 5, 6, 7]
    a[3] = 10
    a.append(20)
    del a[4]
    print(a) # output [1, 2, 3, 10, 6, 7, 20]
    ``` 
  
  + 拼接
    
    和 String 类似地，你可以使用 `+` 和 `*` 符号对列表进行拼接。


    |表达式|结果|
    |---|---|
    |`[1, 2, 3] + [4, 5]`|`[1, 2, 3, 4, 5]`|
    |`[3, "22"] * 2`|`[3, "22", 3, "22"]`|

+ 列表的嵌套
  
  和 C/C++ 不同的是，在 Python 中没有「二维数组」这一说了——取而代之的是通过列表嵌套实现类似的功能。例如：

  ```python
  array = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
  print(array[1][2]) # output 6
  ```

+ 列表的函数与方法
  
  + 函数：
    

    |函数|作用|
    |---|---|
    |`len(list)`|返回列表长度|
    |`max(list)`|返回列表元素最大值|
    |`min(list)`|返回列表元素最小值|

  + 方法：


    |方法|作用|
    |---|---|
    |`list.append(v)`|在列表末尾添加元素 $v$|
    |`list.count(v)`|统计 $v$ 在列表中出现的次数|
    |`list.index(v)`|找到 $v$ 在列表中的最小索引|
    |`list.insert(x, v)`|在列表下标 $x$ 处插入元素 $v$，插入后 $list_x=v$|
    |`list.remove(v)`|删除列表中第一个匹配到的 $v$|
    |`list.pop([index = x])`|删除列表中下表为 $x$ 的元素，若为空则删除最后一个元素|
    |`list.reverse()`|反转列表|
    |`list.sort(cmp=None, key=None, reverse=False)`|`reverse` 为 False 表示升序（默认），为 True 表示降序；`cmp` 表示自定义排序方法；`key` 主要用于表示进行比较的元素|
    |`list.clear()`|清空列表|

+ 列表的比较
  
  引入 `operator` 模块的 `eq` 方法可以实现列表的比较。如果两个列表相等，这个方法返回 `True`，否则返回 `False`。

  ```python
  import operator

  a = [1, 2]
  b = [2, 3]
  c = [2, 3]
  print(operator.eq(a,b)) # False
  print(operator.eq(c,b)) # True
  ```

#### § 2.5 Tuple（元组）

Python 的元组与列表类似，不同之处在于 **元组的元素不能修改**。

元组使用小括号 `( )`，列表使用方括号 `[ ]`。

元组创建很简单，只需要在括号中添加元素，并使用逗号隔开即可。

#### § 2.6 Set（集合）

Set 是一个无序的不重复元素序列。

+ 基本操作

  + 创建

    可以使用大括号 `{ }` 或者 `set()` 函数创建集合，注意：创建一个空集合必须用 `set()` 而不是 `{ }`，因为 `{ }` 用来创建一个空字典。例如：

    ```python
    setA = {114, 514, "apple"}
    setB = set({0, 1, 2, 3})
    setC = set(range(4)) # setB == setC
    setEmpty = set()
    ```
  + 添加元素
    
    使用 `S.add(x)` 将元素 $x$ 添加到集合 $S$ 中。

    此外可以使用 `S.update(x)` （$x$ 是列表 / 元组等）将多个元素添加到集合 $S$ 中。例如：

    ```python
    S = {1, 2, 3}
    S.add(4)
    print(S) # output {1, 2, 3, 4}
    S.update([1, 5], [10, 20])
    print(S) # output {1, 2, 3, 4, 5, 10, 20}
    ```

  + 删除元素

    第一种方式是使用 `S.remove(x)` 将元素 $x$ 从 $S$ 中移除。**如果元素不存在，则会发生错误。**

    第二种方式是使用 `S.discard(x)` 将元素 $x$ 从 $S$ 中移除。且如果元素不存在，不会发生错误。

  + 获取集合元素数目
    
    使用 `len(S)` 获取集合 $S$ 的元素数目。

    ```python
    S = {1, 10, 10, 100}
    print(len(S)) # output 3
    ```

+ 其它常用方法


|方法|含义|
|---|---|
|`S.clear()`|移除集合中的所有元素|
|`S1.intersection(S2,S3...)`|求 $S_1, S_2, S_3...$ 的交集|
|`S1.intersection(S2,S3...)`|求 $S_1, S_2, S_3...$ 的并集|
|`x.difference(y)`|求在集合 $x$，但不在集合 $y$ 的元素集合 **（差集）**|

+ 列表不可作为集合中的元素。
  
#### § 2.7 Dictionary（字典）

从数学的角度看，Dictionary 建立了若干映射关系。从 C/C++ 的角度看，Dictionary 的功能和 STL 中的 Map 非常类似。

请注意：

1）**键必须是唯一的，但值则不必。** 可以类比数学中，映射中不可以同时出现两个对应关系，它们的自变量相同。

2）**值可以取任何数据类型，但键必须是不可变的。** 不可变的数据类型包括 **Number / Tuple / String**。

+ 基本用法
  
  + 声明

    ```python
    d = {key1 : value1, key2 : value2, key3 : value3}
    d_empty1 = {} # Use {} to create an empty dictionary
    d_empty2 = dict() # You can also create an empty dict in this way
    ```
  + 访问

    使用 `dic[key]` 来获取 kay 对应的 value。
  
  + 更新

    使用 `dic[key] = new_value` 来修改 key 对应的值。当在该字典中 key 没有对应的值，字典将自动创建一个新的键值对 `key -> new_value`。  

    删除字典中的某个键值对可以使用 `del dic[key]`；清空字典中的所有键值对可以使用 `dic.clear()`；删除字典的话使用 `del dic`。

+ 其它字典的常用内置函数 / 方法
  

  |函数或方法|含义|
  |---|---|
  |`len(d)`|返回字典长度|
  |`d.clear()`|删除字典内所有函数|
  |`d.pop(key[,default])`|返回 key 对应的值并删除 key 对应的键值对；如果 key 不在字典中则不删除任何键值对，并且返回 default（如果没有设置 default，当 key 不在字典内将会触发 error）|
  |`key in d`| 如果 key 在字典 d 内返回 True，否则返回 False|

#### § 2.8 数据类型转换
   
Python 中的数据类型转换可以分为两种：

+ 隐式类型转换：自动完成类型转换。
+ 显式类型转换：需要使用特定函数帮助完成转换。

**1）隐式类型转换**

例如，当把一个 int 和一个 float 相加时，Python 会自动给将 int 转化为更高的 Number 类型 float。最终得数的返回亦是 float 类型。

```python
a = 1
b = 1.4
c = a + b
print(c) # output 2.4
print(type(c)) # output <class 'float'>
```

**2）显式类型转换**

从规律上看，Python 一般只能在较为直观的情形进行「隐式类型转换」。像比如 `42 + "10"` 这种表达式，Python 是肯定无法把 `"10"` 转化为 `10` 的。不过这个时候，我们可以考虑「显式类型转换」。

隐式类型转换借助 **函数** 完成。一般可能用到以下函数：

|函数|作用|
|---|---|
|`int(x [,base])`|将 $x$ 转换为一个整数，$base$ 表示进制数，默认为十进制|
|`float(x)`|将 $x$ 转换到一个浮点数|
|`complex(real [,imag])`|创建一个复数|
|`str(x)`|将对象 $x$ 转换为字符串|
|`tuple(s)`|将序列 $s$ 转换为一个元组|
|`list(s)`|将序列 $s$ 转换为一个列表|
|`set(s)`|转换为可变集合|
|`dict(d)`|创建一个字典，$d$ 必须是一个 (key, value) 元组序列|

#### § 2.9 Python 推导式

常用的推导式有如下几种：

+ 列表 (list) 推导式
+ 字典 (dictionary) 推导式
+ 集合 (set) 推导式
+ 元组 (tuple) 推导式

下依次介绍。本部分建议结合实例理解。

**1） List 推导式**
  
  ```
  [out_exp_res for value in collection if condition]
  ```

  + `out_exp_res`：列表中元素的表达式，可以是有返回值的函数。
  + `for value in collection`：迭代 `collection` 将 `value` 传入到 `out_exp_res` 表达式中。
  + `if condition`：条件语句，可以过滤列表中不符合条件的值。**此部分为可选，下同。**

  【实例 1】

  ```python
  a = [i * 2 for i in [1, 2, 3] if i != 2]
  print(a) # output [2,6]
  ```

  【实例 2】

  ```python
  def fun(x):
    if x < 2:
        return x
    else:
        return x * 10

  a = [fun(i) for i in range(5)]
  print(a) # output [0, 1, 20, 30, 40]
  ```

**2） Dictionary 推导式**

```
{ key_expr: value_expr for value in collection if condition }
```

类比 List 推导式即可。其中 `key_expr` 和 `value_expr` 一般是关于 `value` 的表达式。

**3）Set 推导式**

```
{ expr for value in collection if condition }
```

注意区分 Set 推导式和 Dictionary 推导式的格式差别。

**4）Tuple 推导式**

```
( expr for value in collection if condition )
```

Tuple 推导式又名生成器表达式。

#### § 2.10 Python 变量的本质

当你运行以下的代码，你可能会发现结果出乎你的意料。

```python
y = [1, 2, 3, 4]
x = y
y.append(5)
print(x)
print(y)
```

输出了两行 `[1, 2, 3, 4, 5]` 。

这是因为在 Python 中，**六种基本变量的本质都是指针**。

在 C/C++ 中，一般的赋值语句 `x = 3` 再进行修改 `x = 4` 的内在逻辑如下：

+ 开辟一个内存空间，代号为 `x`；
+ 将数字 $3$ 放入这个内存空间；
+ 修改时，将同一个内存空间的值改为 $4$。

但在 Python 中不一样。

+ 赋值时，将 `x` 指向 $3$ 这个 Number 类型的实例；
+ 修改时，将 `x` 指向 $4$ 这个 Number 类型的实例。

因此，我们便可以解释上述代码——

+ 首先，将 `y` 指向列表 $[1, 2, 3, 4]$；
+ 将 `x` 和 `y` 指向同一个列表；
+ 将 `y` 所指的列表 $[1, 2, 3, 4]$ 末尾加上一个 $5$；
+ 输出 `x` 和 `y`，它们均为 $[1, 2, 3, 4, 5]$。

---

Python 中的变量分为 **可变** 和 **不可变** 的。细心的话可以发现，Python 中所有的不可变变量（Number/Tuple/String）都有不可拓展的性质；反之，所有可变变量（List/Set/Dict）都是可以拓展的。

+ 对于一切的 **不可变数据类型** `var`，其发生的更新可以抽象为一个赋值语句 `var = new_val`。 假设 `var` 初始时指向的是 `old_val`，进行这一赋值后它重新将指向 `new_val`。这便是为什么我们说，当不可变数据类型对应的值发生了变化时，它对应的内存地址也会改变。因为 `old_val` 和 `new_val` 显然不在同一内存区域。

+ 对于一切的 **可变数据类型** `var`，若其发生的更新是针对其「子内容」的（例如列表的 `a[x] = val` ，集合的 `s.add(x)` 和字典的 `d[x] = val`），则 `var` 指向的内存地址不会发生改变。这是因为从大的整体来看，列表/集合/字典 它们自身并没有什么变化，变的仅仅是其内部内容。

  但是请注意，如果对可变数据进行整体性修改（如对于一个已创建的列表 `a` 执行整体赋值 `a = [1, 2, 3]`），则 `a` 指向的地址仍会发生改变。

  需要厘清的是，如果将 **可变数据类型** `var` 的「子内容」也看成数据类型的话，这些「子内容」的本质亦为指针。若某个「子内容」是不可变数据，那么修改「子内容」后，「子内容」的指向也将发生变化。

  例如，列表元素具有指针本质。

  ```python
  a = [1, 2, 3, 4]
  b = [1, 2, 3, 4]
  b[0] = 2
  b[2] = 4
  ```

  ![](images/p1.png)

  元组元素也具有指针本质。

  ```python
  a = [1, 2, 3]
  b = (233, a, a)
  a[0] = 10
  print(b)
  ```

  上述代码输出 `(233, [10, 2, 3], [10, 2, 3])` 。

  有意思的是，（你可以尝试）在执行 `a[0] = 10` 之后， `b` 的内容发生了改变，但 `id(b)`（b 的地址）并未发生改变，这似乎和「Tuple 是不可变数据」有所相悖。

  我个人的理解是，当我们把元组的元素看作指针之后，我们发现指针实际上并未发生改变，因而 `id(b)` 不变也是可以理解的。

---

Quiz：解释下述几个程序的输出。

+ 代码块 1：
  ```python
  a = [1, 2, 3, 4, 5]
  b = a
  b[3] = 10
  print(a)
  ```
  输出 `[1, 2, 3, 10, 5]` 。

+ 代码块 2：
  ```python
  a = 3
  b = a
  a = 7
  print(b)
  ```
  输出 `3` 。

+ 代码块 3：
  ```python
  a = 1919
  b = [1, 1, 4, 5, 1, 4]
  c = (a, b)
  del b[5]
  a = 1919810
  print(c, a)
  ```
  输出 `(1919, [1, 1, 4, 5, 1]) 1919810` 。

+ 代码块 4：
  ```python
  x = 4
  a = [1, 2, 3]
  b = [x, 5, 6]
  a, b = b, a
  a[0], b[0] = b[0], a[0]
  print(a, b)
  ```
  输出 `[1, 5, 6] [4, 2, 3]` 。

### § 3 流程控制

#### § 3.1 条件

Python 中选择结构一般如下：

```python
if condition_1:
    statement_block_1
elif condition_2:
    statement_block_2
else:
    statement_block_3 
```
值得关注的是，Python 中用 `elif` 代替了 `else if` 。

注意每个条件后的 **冒号**，以及代码的 **缩进**。

如果 `statement_block` 只包含一个语句，可以将其和 `if/elif/else` 放在同一行（压行）。

+ `match... case` 语句
  
  类比 C/C++ 中的 `switch case` 语句。

  `match` 后的对象会依次与 `case` 后的内容进行匹配，如果匹配成功，则执行匹配到的表达式，否则直接跳过，`_` 可以匹配一切。

  例如，下述程序输出 `wtf` 。

  ```python
  a = 3

  match a:
      case 1:
          print("a = 1")
      case 2:
          print("a = 2")
      case _:
          print("wtf")
  ```

  `match case` **语句只在 Python 3.10 版本及以后可以使用。**

#### § 3.2 循环

+ 常用循环体
  
  Python 中的循环语句有 `for` 和 `while`。接下来将进行介绍。

  + while 循环

    ```python
    while condition:
      statement
    ```

    例如，以下代码块输出 `55` 。

    ```Python
    a = 1
    sum = 0

    while a <= 10:
        sum += a
        a += 1

    print(sum)
    ```

    特别地，我们可以在 `while` 语句后连接一个 `else` 语句。其作用是在跳出循环体后执行一次 `else` 子句内的内容。例如：

    ```Python
    a = 1
    sum = 0

    while a <= 10:
        sum += a
        a += 1
    else:
        print(sum + 1) # output 56
    ```

    如果 `while` 循环体中的 `statement` 仅包含一条语句，可以将其和 `while` 放在同一行（压行）。

  + for 循环

    ```Python
    for variable in sequence:
        statements
    [else:
        statements]
    ```

    此处的 `variable` 和 `sequence` 比较抽象。`sequence` 可以是多种类型，例如：

    ```Python
    arr = ["fu", 1.0, [1, 2, 3]]
    for i in arr:
        print(i, end = " ")
    ```

    **sequence 可以是一个列表**。输出 `fu 1.0 [1, 2, 3] ` 。

    ```Python
    seq = "bilibili"
    for i in seq:
        print(i, end = " ")
    ```

    **sequence 可以是一个字符串**。输出 `b i l i b i l i ` 。

    ```Python
    for i in range(1, 4):
        print(i, end = "@")
    ```

    **sequence 可以是一个 range 函数**。输出 `1@2@3@` 。


+ 循环结构常用辅助语句
  
  + `break` 和 `continue` 语句

    `break` 语句可以跳出循环体。**如果使用 `break` 从循环中终止，任何对应的循环 `else` 块将不执行**。

    `continue` 语句被用来告诉程序跳过当前循环块中的剩余语句，然后继续进行下一轮循环。

  + `pass` 语句

    这是一个空语句，不执行任何操作。
    
  + `range()` 函数

    第一种是 `range(l, r)`，内含 $[l,r)$ 内的所有整数。例如：

    ```Python
    for i in range(1, 10):
      print(i + 1)
    ```

    输出 $2 \sim 10$（每个数字都换行）。

    第二种是 `range(r)`，这个等同于 `range(0, r)`。

    第三种是 `range(l, r, step)`。step 表示步长，指生成的数的公差。例如：

    ```Python
    for i in range(1, 10, 3):
      print(i, end = " ")
    ```

    输出 `1 4 7 ` 。

    **除了常用于 for 循环的范围限制之外，range() 函数还可以用于生成 List/Tuple/Set。** 例如：

    ```Python
    a = list(range(5))
    b = set(range(4))
    c = tuple(range(3))

    print(a) # output [0, 1, 2, 3, 4]
    print(b) # output {0, 1, 2, 3}
    print(c) # output (0, 1, 2)
    ```

#### § 3.3 函数

基本操作：

```python
def func_name(argument table):
    statements
    statements
    statements
```

+ 参数的传递问题

  在继续阅读之前，请回顾 2.10 节 - Python 变量的本质。

  参数传递遵循这样一个规则：当函数开始执行时，形参被设定为和实参指向同一个地方。或者说，从地址上看，形参是实参的拷贝。

  例如：

  ```python
  def fun(x):
      print(x, id(x)) # output 5 140720516166568
      x = 10
      print(x, id(x)) # output 10 140720516166728

  a = 5
  print(a, id(a)) # output 5 140720516166568
  fun(a)
  print(a, id(a)) # output 5 140720516166568
  ```

  我们不难从这个代码块发现，`a`（实参）与 `x`（形参）指向的地址是一致的。因而，一些教程说「对于不可变变量，函数传参时是值传递」，个人感觉有失妥当。

  在进行 `x = 10` 的赋值后，`x` 所指的 Number 从 5 变成了 10，相应地，其地址也必定发生变化。然而，`a` 指向的地址并不会发生改变。

  再如（这个例子比较重要！）：

  ```python
  def fun1(x):
    print(x, id(x)) # output [1, 2, 3] 2339008125888
    x.append(10)
    print(x, id(x)) # output [1, 2, 3, 10] 2339008125888

  def fun2(x):
      print(x, id(x)) # output [1, 2, 3, 10] 2339008125888
      x = [1, 1, 4, 5, 1, 4]
      print(x, id(x)) # output [1, 1, 4, 5, 1, 4] 2339008997632

  a = [1, 2, 3]
  print(a, id(a)) # output [1, 2, 3] 2339008125888
  fun1(a)
  print(a, id(a)) # output [1, 2, 3, 10] 2339008125888
  fun2(a)
  print(a, id(a)) # output [1, 2, 3, 10] 2339008125888
  ```

  我们首先声明了一个列表 `[1, 2, 3]`，并让 `a` 指向这个列表。执行第一个函数时，`x` 作为形参也指向同一个 `[1, 2, 3]`。然后执行 append 方法，致使 `x` 指向的列表最后增加了一个 `10`。回到主干上，由于 `a` 和 `x` 指向同一个列表，故而它们都变成了 `[1, 2, 3, 10]` 。

  接着执行第二个函数。第二次 `x` 作为形参依然指向同一个 `[1, 2, 3，10]`。当执行赋值操作时，`x` 整体发生了改变，故而 `x` 被指向了一个新的列表 `[1, 1, 4, 5, 1, 4]`，而注意，`x` 原来指向的列表（也是 `a` 指向的列表）并未发生改变。故而回到主干上时，`a` 指向的依旧是 `[1, 2, 3, 10]` 。

+ 参数的设定
  
  调用函数时可使用的正式参数类型主要包含四种：必需参数，关键字参数，默认参数，不定长参数。

  + 必需参数

    必需参数须以正确的顺序传入函数。调用时的数量和顺序必须和声明时的一样。就是你一般写函数时，所使用的参数设定方式。例如：
    
    ```python
    def fun(a, b, c):
        d = a + b + c * 2
        return d

    a, b, c = 1, 2, 3
    print(fun(a, b, c)) # output 9
    ```

  + 关键字参数

    使用关键字参数允许函数调用时参数的顺序与声明时不一致。例如：
    
    ```python
    def fun(x, y, z):
        res = x + y + z * 2
        return res

    a, b, c = 1, 2, 3
    print(fun(x = c, y = b, z = a)) # output 7
    ```

  + 默认参数

    在「关键字参数」的基础上，如果某一形参具有默认值，当调用时实参没有指定值时，将采取默认值。例如：

    ```python
    def fun(x = 11, y = 45, z = 14):
        res = x + y + z * 2
        return res

    a, b, c = 1, 2, 3
    print(fun(x = c, y = b)) # output 33
    ```

  + 不定长参数

    当你不确定函数的参数数量时，你可能需要不定长参数。

    ```python
    def fun(x, y, *t):
        print(x, y, t)

    fun(1, 2, 3, 4, 5) # output 1 2 (3, 4, 5)
    ```

    加了 `*` 的形参会以 **元组** 的形式导入，这便实现了参数的不定长需求。

+ lambda 函数

  Python 使用 `lambda` 来创建匿名函数。

  所谓匿名，意即不再使用 `def` 语句这样标准的形式定义一个函数。

  Attention:

  + lambda 的主体是一个表达式，而不是一个代码块。**仅仅能在 `lambda` 表达式中封装有限的逻辑进去。**

  + lambda 函数拥有自己的命名空间，且 **不能访问自己参数列表之外或 global 里的参数**。

  lambda 函数的语法只包含一个语句：

  ```python
  lambda [arg1 [,arg2,.....argn]]: expression
  ```

  例如：
  ```python
  sum = lambda arg1, arg2: arg1 + arg2
  print(sum(10, 20)) # output 30
  ```

  之所以说函数是匿名的，是因为更多时候我们并不一定要给予其名字。例如：

  ```python
  print((lambda a, b: a ** b)(2, 3)) # output 8
  ```

  你甚至可以把 lambda 函数封装在一个函数内。例如：

  ```python
  def func(n):
    return lambda x: x ** n

  print(func(2)(3)) # output 9
  print(func(3)(3)) # output 27
  F = func(4)
  print(F(3)) # output 81
  ```

### § 4 第一阶段：杂项
  
#### § 4.1 作用域

初学 Python 时，一个令人恼火的问题是——我该怎么知道这个变量是全局的还是局部的？本节我们将探讨这个问题。

Python 的作用域分为四种：

#### § 4.2 文件操作

#### § 4.3 再探输入与输出

#### § 4.4 迭代器与生成器

#### § 4.5 Python 内置数据结构

#### § 4.6 Python 标准库

### § 5 Import

### § 6 面向对象编程