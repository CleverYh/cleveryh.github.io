---
layout:     post
title:      Object-Oriented Programming!
subtitle:   Learning C++
date:       2020-04-09
author:     蒟蒻炸毛
timecost:   5 minutes
# header-style: black
# header-mask: 0.01
# header-img-credit:      Yuhan Chen
# header-img-year:        2019 
header-img-outchain:    true
header-img: https://s1.ax1x.com/2020/03/16/88O1SO.jpg
catalog: true
mathjax: true
live2d:  false
tags:
    - C++
    - Matrix
---

# Week1 Basic iostream

使用`<iostream>`以及`<iomanip>`库操纵输入输出流。

输入一行，包含一个整数$n$，一个字符$c$，一个整数$w$。

输出这个整数$n$，要求控制输出宽度为$w$，左对齐，空白部分用字符$c$填充，且$n$为非负整数时显示$+$号。

保证$n$的宽度小于$w$。

#### Input

```
4 c 6
```

```
-45 u 8
```

#### Output

```
+4cccc
```

```
-45uuuuu
```

#### C++ code

注释掉的是我一开始写的，也能过，但是不美观。
```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    int n, w;
    char c;

    cin >> n >> c >> w;
    cout << left << setfill(c) << setw(w) << showpos << n << endl;
    // if (n >= 0)
    //     cout << '+' << setfill(c) << setw(w - 1) << left << n << endl;
    // else
    //     cout << setfill(c) << setw(w) << left << n << endl;

    return 0;
}
```


# Week1 Create Array

使用C++中的关键字`new`和`delete`创建和回收数组。

实现以下四个函数：

```c++
int* Array(int len);
int** Reshape(int* vec, int row, int col);
void FreeArray(int* vec);
void FreeMatrix(int** mat, int row);
```

其中，`Array`函数创建一个长度为`len`的一维数组；`Reshape`函数将上述数组变为一个`row`$\times$`col`的矩阵，题目数据保证`row`$\times$`col`$=$`len`。

已禁用`stdlib.h`，请勿使用`malloc`及`free`。

#### Input

```
6
1 2 3 4 5 6
2 3
```

### Output

```
1 2 3
4 5 6
```

将长度为$6$的向量reshape为$2 \times 3$的矩阵。

#### Provided: main.cpp

```cpp
// #include "check.h"
// #include <iostream>
#include "functions.h"
#include <stdio.h>

int main() {
    int *vec = nullptr;
    int len, row, col;
    scanf("%d", &len);
    vec = Array(len);
    for (int i = 0; i < len; ++i) {
        scanf("%d", vec + i);
    }
    int **mat = nullptr;
    scanf("%d%d", &row, &col);
    mat = Reshape(vec, row, col);
    for (int i = 0; i < row; ++i) {
        for (int j = 0; j < col; ++j) {
            printf("%d%c", mat[i][j], j == col - 1 ? '\n' : ' ');
        }
    }
    FreeArray(vec);
    FreeMatrix(mat, row);
    return 0;
}
```


#### Provided: functions.h

```cpp
#ifndef FUNCTION_H
#define FUNCTION_H

int* Array(int len);							
int** Reshape(int* vec, int row, int col);	
void FreeArray(int* vec);
void FreeMatrix(int** mat, int row);

#endif
```


#### Answer: functions.cpp
以下是我原来写的。

```cpp
int *Array(int len) {
    int *array = new int[len];
    return array;
}
int **Reshape(int *vec, int row, int col) {
    int k = 0;
    int **matrix = new int *[row];
    for (int i = 0; i < row; i++) {
        matrix[i] = new int[col];
        for (int j = 0; j < col; j++) {
            matrix[i][j] = vec[k++];
        }
    }
    return matrix;
}
void FreeArray(int *vec) {
    delete[] vec;
}
void FreeMatrix(int **mat, int row) {
    for (int i = 0; i < row; i++) {
        delete[] mat[i];
    }
    delete[] mat;
}
```
其中的 `Reshape`函数也可以如下实现。

```cpp
int **Reshape(int *vec, int row, int col) {
    int **matrix = new int *[row];
    for (int i = 0; i < row; i++) {
        matrix[i] = new int[col];
        for (int j = 0; j < col; j++) {
            matrix[i][j] = vec[i*col + j];
        }
    }
    return matrix;
}
```

# Week1 Call by Reference

引用传递函数参数是C++区别于C的一大特性，可看作是指针的语法糖。通过引用传递参数主要有以下两个好处：

- 减少内存开销

设想有一个庞大的自定义结构体，当你想要将其作为函数参数时，若直接传入函数：

```c++
void f(struct HugeStruct hugestruct);
```

实际上是进行了将实参赋值给形参这样的内存拷贝，对于`int`，`bool`这样的类型来说可以忽略，但对于某些庞大的结构来说，这样的内存开销是不可接受的；

- 多个返回值

C++中的函数不支持多个返回值，假设你的函数想返回多个值，一种可行的解决方法是返回一个结构体，但某些情况下这样做可能不太优雅，因此可以通过使用引用作为函数参数，来支持多值返回。

```c++
void g(int x, int& y1, int& y2); // y1, y2是返回值
```



请同学们自行查阅相关资料，完成以下两个函数：

```c++
void addsub(int x, int y, int& sum, int& diff); // 将x, y相加结果储存在sum中，相减结果储存在diff中
void swap(int& x, int& y); // 交换x, y的值
```
#### Provided: main.cpp

```cpp
#include <iostream>
#include "functions.h"
using std::cin;
using std::cout;
using std::endl;

int main() {
    int x, y, sum, diff;
    cin >> x >> y;
    addsub(x, y, sum, diff);
    cout << sum << '\t' << diff << endl;
    swap(x, y);
    cout << x << '\t' << y << endl;
    swap(sum, diff);
    cout << sum << '\t' << diff << endl;
    return 0;
}
```

#### Provided: functions.h

```cpp
#ifndef FUNCTION_H
#define FUNCTION_H

void swap(int&, int&);
void addsub(int, int, int&, int&);

#endif
```

#### Answer: functions.cpp

```cpp
// 将x, y相加结果储存在sum中，相减结果储存在diff中
void addsub(int x, int y, int &sum, int &diff) {
    sum = x + y;
    diff = x - y;
}

// 交换x, y的值
void swap(int &x, int &y) {
    int tmp;
    tmp = x;
    x = y;
    y = tmp;
}
```


# Week1 Complex

在C++中实现一个复数结构体，并实现相应的构造函数和析构函数。

C++中结构体与C的区别：

- 拥有继承关系，可以定义`public`，`private`，`protected`等类型的数据成员，可以有虚函数等；
- 可以直接定义一些简单的函数，如构造函数，运算符重载等；
- 无需`struct`关键字即可直接使用结构体名定义新的对象。

本题要求实现一个复数结构体，其成员如下：

```c++
struct Complex {
	int r;
	int i;
	Complex();
	Complex(int r_, int i_);
    ~Complex();
};
```

以及函数：

```c++
double Distanceof(const Complex& c1, const Complex& c2);
```

返回两个复数对应的复平面上的点$p_1, p_2$的距离。

要求在调用`Complex()`时，将实部和虚部都初始化为$0$，并输出以下内容：

```
Create an empty complex.
```

在调用`Complex(int, int)`时，将实部和虚部分别初始化$r, i$，并输出以下内容：

```
Create an complex whose real is r, image is i.
```

在调用析构函数时，输出：

```
Destroy an complex whose real is r, image is i.
```

#### Hint

在结构体或类外定义(实现)相关函数时，需要指出该函数是属于哪个结构体(类)的，请查阅构造函数，析构函数，域作用符的相关知识。

# Week1 Complex Class

实现一个复数类，其结构如下：

```c++
class Complex {
	int r;
	int i;
	Complex();
	Complex(int r_, int i_);
    Complex(const Complex& other);
    ~Complex();
    
    double Distanceof(const Complex& other) const;
	void Print() const;
    
	static int getCounter();
    
private:
    static int counter;
};
```

其中，调用`Complex()`时需将复数的实部和虚部初始化为$0$，并输出：

```
Create an empty complex.
```

调用`Complex(int, int)`时，将实部和虚部分别初始化为$r, i$，并输出以下内容：

```
Create an complex whose real is r, image is i.
```

调用`Complex(const Complex)`时，用传入的复数初始化当前复数，输出：

```
Copy from an complex whose real is r, image is i.
```

在调用析构函数时，输出：

```
Destroy an complex whose real is r, image is i.
```

函数`Distanceof`返回两个复数对应的复平面上的点的距离。

`Print()`函数输出当前复数，格式为：
$$
a + b\mathrm{i}
$$
(注意某些特殊情况下的输出)。


除了上面这些内容，懒惰的计数君想要统计当前已经定义了多少复数，函数`getCounter()`返回当前复数的个数。

#### Hint

请查阅函数重载，类的成员属性，类中的静态成员等内容。

#### Provided: complex.h

```cpp
#ifndef COMPLEX_H
#define COMPLEX_H

class Complex {
public:
    int r;
    int i;
    Complex();
    Complex(int r_, int i_);
    Complex(const Complex& other);
    ~Complex();
    
    double Distanceof(const Complex& other) const;
    void Print() const;
    
    static int getCounter();
    
private:
    static int counter;
};

#endif
```
此处省略`main.cpp`

#### Answer: complex.cpp

```cpp
#include <cmath>
#include <iomanip>
#include <iostream>
using std::cout;
using std::endl;
using std::noshowpos;
using std::showpos;

int Complex::counter = 0;

Complex::Complex(void) {
    r = 0;
    i = 0;
    cout << "Create an empty complex." << endl;
    counter++;
}

Complex::Complex(int r_, int i_) {
    r = r_;
    i = i_;
    cout << "Create an complex whose real is " << r << ", image is " << i << "." << endl;
    counter++;
}

Complex::Complex(const Complex &other) {
    r = other.r;
    i = other.i;
    cout << "Copy from an complex whose real is " << r << ", image is " << i << "." << endl;
    counter++;
}

Complex::~Complex(void) {
    cout << "Destroy an complex whose real is " << r << ", image is " << i << "." << endl;
    counter--;
}

double Complex::Distanceof(const Complex &other) const {
    double dis = sqrt((double)((other.r - this->r) * (other.r - this->r) + (other.i - this->i) * (other.i - this->i)));
    return dis;
}

void Complex::Print(void) const {
    if (r != 0 && i != 0) {
        cout << r << showpos << i << "i" << noshowpos << endl;
    } else if (i == 0) {
        cout << r << endl;
    } else if (r == 0) {
        cout << noshowpos << i << "i" << endl;
    }
}

int Complex::getCounter(void) {
    return counter;
}
```

#### Key: complex.cpp

```cpp
#include <cmath>
#include <iomanip>
#include <iostream>
using std::cout;
using std::endl;
using std::setiosflags;
using std::resetiosflags;

int Complex::counter = 0;

Complex::Complex(): r(0),i(0) {
    cout << "Create an empty complex." << endl;
    this->counter++;
}

Complex::Complex(int r_, int i_): r(r_), i(i_) {
    cout << "Create an complex whose real is " << this->r << ", image is " << this->i << "." << endl;
    this->counter++;
}
// 拷贝构造函数
Complex::Complex(const Complex& other): r(other.r), i(other.i) {
    cout << "Copy from an complex whose real is" <<this->r << ", image is " << this->i << "." << endl;
    this->counter++;
}

Complex::~Complex() {
    cout << "Destory an complex whose real is" << this->r << ", image is " << this->i << "." << endl;
    this->counter--;
}

double Complex::Distanceof(const Complex& other) const {
    return sqrt(pow(this->r - other.r,2 ) + pow(this->i - other.i, 2 ));
}

void Complex::Print() const {
    if (r == 0) {
        if (i == 0) {
            cout << 0 << endl;
            return;
        } else {
            cout << i << 'i' << endl;
            return;
        }
    } else {
        cout << r;
        if (i == 0) {
            cout << endl;
            return;
        } else {
            cout << setiosflags(std::ios::showpos) << i << 'i' << resetiosflags(std::ios::showpos) << endl;
            return;
        }
    }
}
```

# Week1 ADT in C (Date Class)

#### Task
学习如何用C模仿C++的类：  
[How do you implement a class in C?](http://stackoverflow.com/questions/1403890/how-do-you-implement-a-class-in-c)  
根据给出的文件实现C中的“Date class”。
#### Hints
先认真阅读上面的链接。  
`void DestroyDate(Date** p);` 中，传入Date**的作用是避免“空悬指针”问题。
#### One more step
- 试试在client代码中直接访问或修改Date的year等变量，跟C++版本有何不同？这会与“封装”原则冲突吗？

- 仔细辨别`void SetDate(Date* date, int year, int month, int day)`和`void Date::setDate(int year, int month, int day);`两者的异同，
包括参数数量，参数类型，命名风格等（关于命名风格可参照前面提到的《Google C++ Style Guide》）。

- 大胆猜想早期C++编译器是如何把C++程序转换为C程序的。另外，本题只是一种思路，有兴趣可以进一步了解

- 这种做法有必要吗？请查阅资料了解其应用场景。

- 如果`void DestroyDate(Date** p);` 传入Date*，可行吗？可能会导致什么问题？

- 为什么要把free()封装到Destroy()里？直接让client代码调用free()合适吗？

- 内存资源释放要求调用者调用`Destroy*`，可能会有什么问题？有兴趣的同学可以了解C++的解决方案（需要超前学习，选做）。

- 如果调用者写出`date2 = date1`这种代码，即两个指针指向同个“对象”，在内存资源释放上会有问题吗？有兴趣的同学可以了解C++的解决方案（需要超前学习，选做）。


#### Provided: date_client.c

```c
#include "date.h"
#include <assert.h>
#include <stdio.h>

void TestDate(Date *date, int after, int before) {
    printf("Year: %d\n", GetYear(date));
    printf("Month: %d\n", GetMonth(date));
    printf("Day: %d\n", GetDay(date));

    char *date_string = GetDateString(date);
    printf("Formatted: %s\n", date_string);

    DestroyDateString(&date_string);
    assert(date_string == NULL);
    DestroyDateString(&date_string);

    IncreaseDate(date);
    date_string = GetDateString(date);
    printf("1 day later: %s\n", date_string);
    DestroyDateString(&date_string);

    int i;
    for (i = 0; i < after; ++i) {
        IncreaseDate(date);
    }
    date_string = GetDateString(date);
    printf("Another %d day(s) later: %s\n", after, date_string);
    DestroyDateString(&date_string);

    for (i = 0; i < before; ++i) {
        DecreaseDate(date);
    }
    date_string = GetDateString(date);
    printf("Another %d day(s) earlier: %s\n", before, date_string);
    DestroyDateString(&date_string);
}

int main() {
    int year, month, day;
    int after, before;
    scanf("%d%d%d%d%d", &year, &month, &day, &after, &before);

    Date *date1 = CreateDate(year, month, day);
    assert(sizeof(*date1) == sizeof(Date));

    Date *date2 = CopyDate(date1);
    assert(date1 != date2);
    assert(date1->month == date2->month);

    SetDate(date1, year + 1, month, day);
    SetYear(date1, year);
    SetMonth(date1, month);
    SetDay(date1, day);

    TestDate(date1, after, before);

    DestroyDate(&date1);
    DestroyDate(&date2);

    assert(date1 == NULL);
    DestroyDate(&date2);

    return 0;
}

/*
One probable test case:

1900 2 28 365 366
Year: 1900
Month: 2
Day: 28
Formatted: 1900-02-28
1 day later: 1900-03-01
Another 365 day(s) later: 1901-03-01
Another 366 day(s) earlier: 1900-02-28

*/
```

#### Provided: date.h

```c
#ifndef DATE_H_
#define DATE_H_

typedef struct {
    int year, month, day;
} Date;

Date *CreateDate(int year, int month, int day);
void DestroyDate(Date **p);
Date *CopyDate(const Date *date);

int GetYear(const Date *date);
int GetMonth(const Date *date);
int GetDay(const Date *date);
void SetYear(Date *date, int year);
void SetMonth(Date *date, int month);
void SetDay(Date *date, int day);
void SetDate(Date *date, int year, int month, int day);

char *GetDateString(const Date *date);
void DestroyDateString(char **p);

void IncreaseDate(Date *date);
void DecreaseDate(Date *date);

#endif
```

#### My Answer: date.c

```c
#include <stdlib.h>
#include <string.h>
#include "date.h"

Date *CreateDate(int year, int month, int day) {
    Date *date = (Date *)malloc(sizeof(Date));
    date->year = year;
    date->month = month;
    date->day = day;
    return date;
}
void DestroyDate(Date **p) {
    free(*p);
    *p = NULL;
}
Date *CopyDate(const Date *date) {
    Date *ret = (Date *)malloc(sizeof(Date));
    memcpy(ret, date, sizeof(Date));
    return ret;
}

int GetYear(const Date *date) {
    return date->year;
}
int GetMonth(const Date *date) {
    return date->month;
}
int GetDay(const Date *date) {
    return date->day;
}
void SetYear(Date *date, int year) {
    date->year = year;
}
void SetMonth(Date *date, int month) {
    date->month = month;
}
void SetDay(Date *date, int day) {
    date->day = day;
}
void SetDate(Date *date, int year, int month, int day) {
    date->year = year;
    date->month = month;
    date->day = day;
}


char *GetDateString(const Date *date) {
    char *dateStr = (char *)malloc(sizeof(char) * 11);
    int year = date->year;
    for (int i = 3; i >= 0; i--) {
        dateStr[i] = year % 10 + 48;
        year /= 10;
    }
    dateStr[4] = dateStr[7] = '-';
    dateStr[5] = date->month / 10 + 48;
    dateStr[6] = date->month % 10 + 48;
    dateStr[8] = date->day / 10 + 48;
    dateStr[9] = date->day % 10 + 48;
    dateStr[10] = '\000';

    return dateStr;
}
void DestroyDateString(char **p) {
    free(*p);
    *p = NULL;
}

void IncreaseDate(Date *date) {
    if (date->month != 2) {

        if (date->day < 30) {
            date->day++;
            return;
        } else if (date->day == 31) {
            if (date->month != 12) {
                date->day = 1;
                date->month++;
                return;
            } else {
                date->day = 1;
                date->month = 1;
                date->year++;
                return;
            }
        } else if (date->day == 30) {
            if (date->month == 1 || date->month == 3 || date->month == 5 || date->month == 7 || date->month == 8 || date->month == 10 || date->month == 12) {
                date->day++;
                return;
            } else {
                date->day = 1;
                date->month++;
                return;
            }
        }
    } else if (date->month == 2) {
        if (date->day < 28) {
            date->day++;
            return;
        }
        if (date->day == 29) {
            date->day = 1;
            date->month++;
            return;
        }
        // date->day == 28
        int isleap = 0;
        if (date->year % 400 == 0 || (date->year % 100 != 0 && date->year % 4 == 0)) isleap = 1;

        if (isleap) {
            date->day++;
            return;
        } else {
            date->day = 1;
            date->month++;
            return;
        }
    }
}
void DecreaseDate(Date *date) {
    if (date->day > 1) {
        date->day--;
        return;
    }
    // date->day == 1;
    if (date->month == 1) {
        date->day = 31;
        date->month = 12;
        date->year--;
        return;
    }
    if (date->month == 2 || date->month == 4 || date->month == 6 || date->month == 9 || date->month == 11 || date->month == 8) {
        date->day = 31;
        date->month--;
        return;
    }
    if (date->month == 3) {
        date->month--;

        int isleap = 0;
        if (date->year % 400 == 0 || (date->year % 100 != 0 && date->year % 4 == 0)) isleap = 1;

        if (isleap) {
            date->day = 29;
            return;
        } else {
            date->day = 28;
            return;
        }
    }
    date->month--;
    date->day = 30;
    return;
}
```

#### Key: some improvement in date.c

```c
char *GetDateString(const Date *date) {
    const int length_of_date_string = 10;
    char *date_string = (char *)malloc(sizeof(char) * (length_of_date_string + 1)); 
    // + 1 to stroge '\000' 
    // in <stdio.h> or <cstdio> int snprintf(char *__restrict__ __stream, size_t __n, const char *__restrict__ __format, ...)
    snprintf(date_string,
             length_of_date_string + 1,
             "%04d-%02d-%02d",
             GetYear(date), GetMonth(date), GetDay(date));
    return date_string;
}

int GetDaysOfMonth(int year, int month) {
    if (month == 2) {
        return ((year % 4 == 0 && year % 100 != 0) || year % 400 == 0) ? 29 : 28;
    } else {
        int daysOfMonth[13] = {-1, 31, -1, 31, 30,
                               31, 30, 31, 31,
                               30, 31, 30, 31};
        return daysOfMonth[month];
    }
}

void IncreaseDate(Date *date) {
    ++date->day;
    if (date->day > GetDaysOfMonth(date->year, date->month)) {
        date->day = 1;
        ++date->month;
        if (date->month > 12) {
            date->month = 1;
            ++date->year;
        }
    }
}

void DecreaseDate(Date *date) {
    --date->day;
    if (date->day < 1) {
        --date->month;
        if (date->month < 1) {
            date->month = 12;
            --date->year;
        }
        date->day = GetDaysOfMonth(date->year, date->month);
    }
}
```

#### 关于返回值的一些问题

此外还发现一个问题，一开始提交时函数

```c

Date *CreateDate(int year, int month, int day) {
    Date *date = (Date *)malloc(sizeof(Date));
    date->year = year;
    date->month = month;
    date->day = day;
    // return date;
}
```

居然能得100分。

原因是malloc的返回值存在eax，正好成了函数的返回值。

对于申请内存这种特权操作，肯定是操作系统内核来做比较合适，但是频繁的进行系统调用，在用户态和内核态之间切换，也就是频繁的调用中断处理程序，性能是较差的。所以，事实上都是通过c语言的运行库封装好的库函数，预申请一块设当的内存，然后零售给程序用，可以采用空闲链表或者位图等来管理。这取决于运行库的实现了。

比如glibc运行库的malloc函数是这样处理的：对于小于128KB的请求，他会在现有的堆里面，按照分配算法给他分配一块空间并返回，对于大于128KB的请求，则使用mmap()为它分配一块匿名空间（mmap可以映射到某个文件，而把使用mmap申请却不映射到文件的空间称为匿名空间），然后在这块空间里面分配给用户内存。

函数将返回值存储在eax中，然后调用方读取eax。但是，eax本身只有4个字节，大于4个字节的返回值，则是通过eax存储了一个指针，而实际内容在栈上的其他地方。

当返回值是大于4个字节的的数据结构时，在压栈入参数的时候，假设函数调用如下：

```c
some_struct foo(int arg1, int arg2);
some_struct s = foo(1, 2);
```

编译器会把把它处理成类似这样的概念：

```c
some_struct* foo(some_struct* ret_val, int arg1, int arg2);
some_struct s; // constructor isn‘t called
foo(&s, 1, 2); // constructor will be called in foo
```

也就是给它安排一个隐藏的参数，大概流程如下
    a. 首先在栈上额外开辟一片空间，并把这块空间的一部分作为传递返回值的临时对象
    b. 把这个对象的地址作为隐藏参数传递给函数
    c. 函数把数据拷贝给临时对象，并把地址用eax传出
    d. 调用方再把临时对象拷贝给返回值。


参考：https://www.cnblogs.com/weijiaen/p/3983492.html