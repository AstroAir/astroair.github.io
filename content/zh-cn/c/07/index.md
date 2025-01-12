---
title: 'Day7 - 结构体'
---

结构体（struct）是 C 语言中用来聚合不同类型数据的工具，它允许用户定义自己的复合数据类型。结构体可以用于存储和管理数据，提高程序的可读性和维护性。

## 定义与基本使用

结构体的定义格式基本如下：

```c
struct 结构体名 {
数据类型 成员名 1;
数据类型 成员名 2;
...
};
```

来一个简单的例子：

```c
#include <stdio.h>

struct Book {
    char title[50];
    char author[30];
    int pages;
    float price;
};

int main() {
    struct Book book1;

    // 为结构体的成员赋值
    strcpy(book1.title, "C Programming");
    strcpy(book1.author, "Dennis Ritchie");
    book1.pages = 500;
    book1.price = 39.99;

    // 输出结构体的成员
    printf("Title: %s\n", book1.title);
    printf("Author: %s\n", book1.author);
    printf("Pages: %d\n", book1.pages);
    printf("Price: %.2f\n", book1.price);

    return 0;

}
```

输出结果:

```bash
Title: C Programming
Author: Dennis Ritchie
Pages: 500
Price: 39.99
```

## 定义与初始化

结构体变量可以有多种定义方式：

```c
struct Book b1; // 定义一个结构体变量 b1
struct Book b2 = {"Title", "Author", 250, 59.99}; // 初始化
```

### 初始化结构体的方式

顺序初始化

```c
struct Book book1 = {"C Language", "Brian Kernighan", 300, 45.5};
```

指定成员初始化

```c
struct Book book2 = {.title="Data Structures", .price=39.5};
```

逐个赋值

```c
struct Book book3;
strcpy(book3.title, "Operating Systems");
strcpy(book3.author, "Silberschatz");
book3.pages = 700;
book3.price = 99.9;
```

## 结构体数组

结构体数组存储多个结构体变量。

```c
#include <stdio.h>

struct Student {
    int id;
    char name[20];
    float score;
};

int main() {
    struct Student students[3] = {
        {101, "Alice", 95.5},
        {102, "Bob", 88.5},
        {103, "Charlie", 90.0}
    };

    // 遍历结构体数组
    for (int i = 0; i < 3; i++) {
        printf("ID: %d, Name: %s, Score: %.2f\n",
               students[i].id, students[i].name, students[i].score);
    }

    return 0;

}
```

输出结果

```bash
ID: 101, Name: Alice, Score: 95.50
ID: 102, Name: Bob, Score: 88.50
ID: 103, Name: Charlie, Score: 90.00
```

## 结构体指针

结构体指针可以指向结构体变量的地址，通过->操作符访问成员。

```c
#include <stdio.h>

struct Car {
    char name[20];
    float price;
};

int main() {
    struct Car car1 = {"Toyota", 25000.0};
    struct Car *ptr = &car1; // 定义结构体指针

    // 使用指针访问成员
    printf("Car Name: %s\n", ptr->name);
    printf("Car Price: %.2f\n", ptr->price);

    // 修改结构体内容
    ptr->price = 26000.0;
    printf("Updated Price: %.2f\n", ptr->price);

    return 0;

}
```

输出结果

```bash
Car Name: Toyota
Car Price: 25000.00
Updated Price: 26000.00
```

## 结构体嵌套

结构体可以作为另一个结构体的成员。

```c
#include <stdio.h>

struct Address {
    char city[20];
    char street[20];
};

struct Person {
    char name[20];
    int age;
    struct Address addr; // 嵌套结构体
};

int main() {
    struct Person p1;

    // 赋值
    strcpy(p1.name, "John");
    p1.age = 30;
    strcpy(p1.addr.city, "New York");
    strcpy(p1.addr.street, "5th Avenue");

    // 输出
    printf("Name: %s\n", p1.name);
    printf("Age: %d\n", p1.age);
    printf("City: %s\n", p1.addr.city);
    printf("Street: %s\n", p1.addr.street);

    return 0;

}
```

输出结果

```bash
Name: John
Age: 30
City: New York
Street: 5th Avenue
```

## 结构体作为函数参数

传值方式

```c
#include <stdio.h>

struct Point {
    int x;
    int y;
};

void printPoint(struct Point p) { // 结构体传值
    printf("Point: (%d, %d)\n", p.x, p.y);
}

int main() {
    struct Point p1 = {10, 20};
    printPoint(p1);
    return 0;
}
```

传指针方式

```c
#include <stdio.h>

struct Point {
    int x;
    int y;
};

void modifyPoint(struct Point \*p) { // 结构体传指针
    p->x += 10;
    p->y += 20;
}

int main() {
    struct Point p1 = {10, 20};
    modifyPoint(&p1);
    printf("Modified Point: (%d, %d)\n", p1.x, p1.y);
    return 0;
}
```

## 结构体与 typedef

typedef 用于简化结构体的使用，相当于取个绰号。

```c
#include <stdio.h>

typedef struct {
    char name[20];
    int age;
} Person;

int main() {
    Person p1 = {"John", 25};
    printf("Name: %s, Age: %d\n", p1.name, p1.age);
    return 0;
}
```

## 结构体大小与内存对齐

结构体的大小受内存对齐规则影响：

- 每个成员按自身类型的大小对齐。
- 结构体的总大小是最大成员大小的整数倍。

```c
#include <stdio.h>

struct Test {
    char a; // 1 字节
    int b; // 4 字节
    char c; // 1 字节
};

int main() {
    printf("Size of struct Test: %zu bytes\n", sizeof(struct Test));
    return 0;
}
```

```bash
输出：（可能为 12 字节，取决于编译器）
Size of struct Test: 12 bytes
```

## 联合体与结构体的区别

- 结构体：所有成员独立存在，占用的内存是所有成员大小之和。
- 联合体：所有成员共享一块内存，大小取决于最大成员。

```c
#include <stdio.h>

union Data {
    int i;
    float f;
    char str[20];
};

int main() {
    union Data data;

    data.i = 10;
    printf("Int: %d\n", data.i);

    data.f = 3.14;
    printf("Float: %.2f\n", data.f);

    strcpy(data.str, "Hello");
    printf("String: %s\n", data.str);

    return 0;

}
```

## 实际应用

结构体在实际项目中非常有用，例如：

- 学生管理系统：存储学生信息。
- 图形系统：定义点、线、面等结构。
- 文件系统：定义文件属性（名称、大小、路径等）。
- 当然，还有学校的 C 语言考试
