---
title: 【笔记】CPP 随笔
category: Notes
date: 2024-01-01
---

> 瞎几把写写。

### Copy ctor & Overload of equal sign

1. 等于号重载也有返回值，为了支持连等号操作。

    ```cpp
    Fraction& Fraction::operator = (const Fraction &b) {
        this -> numerator   = b.numerator;
        this -> denominator = b.denominator;
        this -> quotient    = b.quotient;
        return *this;
    }
    ```
    虽然理论上 `void` 也可行，但不支持连等。

2. 调用谁的问题

    ```cpp
    Fraction f0(2, 3);
    Fraction f1(1, 3);
    Fraction f2 = f1; // Call the COPY CTOR!
    Fraction f3; f3 = f1; // Call the OVERLOAD PART!
    ```

3. 编译器会优化类似于 `f4 = f0 + f1` 的语句。你会发现等号那里对应的拷贝构造函数并没有被「显式」地调用。