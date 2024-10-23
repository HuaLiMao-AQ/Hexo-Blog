---
title: C++学习笔记-Lambda函数用法探究
date: 2024-09-20 18:37:40
tags: C++学习笔记
categories: 学习笔记
thumbnail: "https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/20240920/OIP-C.4g4gg0g798.webp"
mathjax: ture
---

# 1.Lambda函数介绍
Lambda 表达式因数学中的 $\lambda$ 演算得名，直接对应于其中的 lambda 抽象。Lambda 表达式能够捕获作用域中的变量的无名函数对象。我们可以将其理解为一个匿名的内联函数，可以用来替换独立函数或者函数对象，从而使代码更可读。但是从本质上来讲，Lambda 表达式只是一种语法糖，因为它能完成的工作也可以用其他复杂的 C++ 语法来实现。

# 2. Lambda基本语法
下面是Lambda语句的基本语法:
```c++
[capture] (parameters) mutable -> return-type {statement}
```
由此,我们可以写一个Lambda表达式:
```c++
int foo = [](int a, int b) -> int {
    return a+b;
};
```

### 1. 对lambda表达式各部分解释

  - **capture** 用于指定捕获哪些变量，以及捕获是通过值还是引用：
    - **[]** 不捕获任何变量
    - **[var]** 以值传递方式捕获变量**var**(**lambda**中不能修改**var**)
    - **[&var]** 以引用传递方式捕获变量**var**
    - **[&]** 以引用传递方式捕获父作用域的所有变量(包括**this**)
    - **[=]** 以值传递方式捕获父作用域的所有变量(包括**this**)
    - **[&, =var]** 以值传递方式捕获变量**var**，其他变量以引用传递方式捕获
    - **[=， &var]** 以引用传递方式捕获变量**var**，其他变量以值传递方式捕获
    - **[this]** 以值传递方式捕获当前**this**指针
  - **parameters** 参数列表，与普通函数一致，如若无需传递参数**括号内可为空**或**可直接连同括号一起省略**
  - **mutable** 可选，如若指定**mutable**，则会在函数体内拷贝以**值传递方式**捕获的变量，并允许在函数体内更改变量的值且**不会更改**父作用域里该变量的值
  - **-> return-type** 返回值类型，若无返回值可直接省略，若有返回值，也可省略由**编译器推导**
  - **statement** 函数体，可直接使用**参数**和**捕获的变量**

{% notel red 注意 %}
**lambda**中除捕获列表和函数体是必需的，其它都是可选的，最简的**lambda**是```[] {}```，但是该**lambda**无法处理任何事
{% endnotel %}

# 3. Lambda的使用
### 1. 无效lambda
```c++
    int a = 10;
    int b = 10;

    // 无效lambda
    [=] { std::cout << a+b << std::endl; };
```

### 2. 将lambda赋值给一个变量直接调用
```c++
    // 将lambda赋值给foo变量(带参数使用)
    auto foo = [](int a, int b) -> int {
        return a+b;
    };
    std::cout << foo(10, 10) << std::endl;
```

### 3. 以值传递方式捕获父作用域变量
```c++
    int a = 10;
    int b = 10;

    // 以值传递方式捕获变量a
    auto foo = [a] (int x) -> int {
        x += 10; // x可修改
        return a+x; // a不可修改
    };

    std::cout << foo(b) << std::endl;
```
使用**mutable**：
```c++
    int a = 10;
    int b = 10;

    // 以值传递方式捕获变量
    auto foo = [=] mutable -> int {
        a += 10; // a可修改
        b += 10; // b可修改
        return a+b;
    };

    std::cout << foo() << std::endl;
    std::cout << a << " " << b << std::endl; // 函数体外a和b的值并未改变
```

### 4. 以引用传方式捕获变量
```c++
    int a = 10;
    int b = 10;

    // 以引用传递方式捕获变量
    auto foo = [&] -> int {
        a += 10; // a可修改
        b += 10; // b可修改
        return a+b;
    };

    std::cout << foo << std::endl;
    std::cout << a << " " << b << std::endl; // 函数体外a和b的值改变
```
### 5. 自定义排序
```c++
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
    std::vector<int> arr;

    // 生成10个随机数
    for (int i = 0; i < 10; i++)
    {
        arr.push_back(rand());
    }

    // 降序排序
    std::sort(arr.begin(), arr.end(), [](int a, int b) {return a > b;});

    // 输出
    for (auto i = arr.begin(); i != arr.end(); i++)
    {
        std::cout << *i << std::endl;
    }

    return 0;
}
```
