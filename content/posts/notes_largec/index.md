---
title: 【笔记】C 大程杂记
category: Notes
date: 2023-09-01
---

学习 C 大程过程中的一些零散笔记。

### 多文件编程注意事项

+ 头文件
  
  自己编写头文件时，使用 `#ifndef` 和 `#endif` 来避免头文件的重复包含。

  编译器自带的头文件（例如 `stdio.h`）是自带这种系统的。也就是说，可以放心地在多个 c 文件中重复包含同一自带的头文件。

+ 变量的关联
  
  我们将变量和函数分为 **定义 (define)** 和 **声明 (declare)** 两种情况。连接后的程序 **允许多次声明，但只能有一次定义**。

  + 对于一个变量，定义的方法是 `type var;`。而声明则是在其之前加上一个 `extern`。

  + 对于一个函数，定义的方法是填充其内容（即有一对大括号）；而声明则是只写出其名字和参数表，例如 `int foo(int x);` 。
  

### sscanf() 函数

+ C 库函数 `int sscanf(const char *str, const char *format, ...)` 从字符串读取格式化输入。

+ 和 `scanf` 基本一致。唯一的区别是从输入流读入数据变为了从 `str` 读入数据。所以这个函数常常用于格式化一些字符串。
  
+ 特别地两个用法：`%n` 用于接收当前读入和开头相比的偏移量，`%*` 用于忽略某个数据，即不存储于对应的参数中。

+ 例子 1：
  
    ```cpp
    #include <stdio.h>

    int main() {
        char s[] = "wtfwtf 21", a[100];
        int b, p1, p2;
        sscanf(s, "%s%n %d%n", &a, &p1, &b, &p2);
        printf("%s\n%d\n", a, b);
        printf("%d %d\n", p1, p2);
        return 0;
    }
    ```
    这个程序输出：

    ```
    wtfwtf
    21
    6 9
    ```

    可以看到 sscanf 的基本工作原理。并且，`%n` 不会读入 `str` 中的任何数据，只是会获得一个偏移量而已。

+ 例子 2：
    
    ```cpp
    #include <stdio.h>

    int main() {
        char s[] = "wtf 233 466", a[100];
        int b;
        sscanf(s, "%s %*d %d", &a, &b);
        printf("%s\n%d\n", a, b);
        return 0;
    }
    ```

    这个程序输出：

    ```
    wtf
    466
    ```

    可以看到中间的 `233` 被忽略了。

### 函数指针与回调函数

+ 函数指针

  ```c
  void (*pFunc)(int, int);
  pFunc = &add; // Interestingly, & is optional
  (*pFunc)(3, 5); // Interestingly, * is also optional
  ```

  第一行：定义了一个类型为 `void(int, int)` 的函数指针 `pFunc` 。

  第二行：`pFunc` 指向 `add` 的地址。

  第三行：调用 `pFunc` 指向的函数，亦即执行 `add(3, 5)` 。

  Example:
  ```c
  #include <stdio.h>
  #include <string.h>

  int add(int a, int b) {return a + b;}
  void mul(int a, int b) {printf("%d\n", a * b);}

  int main() {
      int (*fuck)(int, int);
      void (*shit)(int, int);
      fuck = &add;
      printf("%d\n", (*fuck)(3, 5));
      shit = &mul;
      (*shit)(3, 5);
      return 0;
  }
  ```
  输出 `8` 和 `15` 。

+ 函数回调
  
  简单理解就是把函数作为传递的参数。当然了，我们传的是函数指针。例如：

  ```c
  void populate_array(int *array, size_t arraySize, int (*GenerateMethod)()){
      array[0] = 1;
      for (size_t i = 1; i < arraySize; ++i)
          array[i] = (*GenerateMethod)(array[i - 1]);
  }

  int Double(int x) {return x * 2;}
  int Triple(int x) {return x * 3;}
  
  int main(void)
  {
      int myarray[10];
      populate_array(myarray, 10, &Double);
      for(int i = 0; i < 10; i++) printf("%d ", myarray[i]);
      printf("\n");
      populate_array(myarray, 10, &Triple);
      for(int i = 0; i < 10; i++) printf("%d ", myarray[i]);
      printf("\n");
      return 0;
  }
  ```

  这个程序生成了两个等比数列。分别是：

  ```
  1 2 4 8 16 32 64 128 256 512 
  1 3 9 27 81 243 729 2187 6561 19683
  ```


### typedef

作用和宏定义 `#define` 类似。

+ 最简单的 typedef 用法
  
  ```c
  typedef int INTEGER; // typedef oldname newname
  INTEGER a, b;
  ```

+ 数组的 typedef 用法

  ```c
  typedef char Line[233];
  Line text;
  ```
  第二行声明了一个长度为 233 的 text 数组。

+ 指针的 typedef 用法
  
  ```c
  typedef char * pstr; 
  pstr str = "abc";
  ```

+ 函数指针 typedef 用法

  对于我们之前给出的例子

  ```c
  void (*pFunc)(int, int);
  pFunc = &add;
  (*pFunc)(3, 5);
  ```

  它又可以写作

  ```c
  typedef void (*FUNCPOINTER)(int, int);
  FUNCPOINTER pFunc; // 声明函数指针 pFunc
  pFunc = &add;
  ```
+ 归纳总结
  
  看起来 typedef 在进行不同类型的代替时写法也有很大区别。但是我们有一个比较简单的方法来进行记忆：

  + 注意到调用时采用的都是 `Type Var;` 的方式。
  + 用 `Var` 替代 typedef 语句中的 `Type`，即可得到原语句。

### 文件操作

+ fopen() 函数

+ feof() 函数
  
  当读取到 EOF 时，该函数返回一个非零值，否则返回零。

+ 读入
  
  `fgetc(FILE* fo)` 从 fo 中读入一个字符。如果到达文件末尾或发生读错误，则返回 EOF。

### 大小端

+ **大端模式（Big-endian）**：数据的高字节，保存在内存的低地址中；而数据的低字节，保存在内存的高地址中。这样的存储模式有点儿类似于把数据当作字符串顺序处理。

  e6 84 6c 4e
  
  在大端模式下，这 32 位应该读作  `e6 84 6c 4e`。

+ **小端模式（Little-endian）**：数据的高字节，保存在内存的高地址中；而数据的低字节，保存在内存的低地址中。这种存储模式将地址的高低和数据位权结合起来——高地址部分权值高，低地址部分权值低。

  e6 84 6c 4e
  
  在小端模式下，这 32 位应该读作 `4e 6c 84 e6`。

+ 注意在一个字节内部，大小端是 **完全一致的**。