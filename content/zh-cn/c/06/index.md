---
title: "Day6 - 函数那些事"
---

众所周知，如果把所有代码都写在 main 函数里不可取的（如果你是`oier`我没有意见），所以我们需要将代码分割成多个函数，这样可以有效提高代码的可读性和可维护性。

## 定义和声明

### 定义

函数定义是提供函数的具体实现，描述了函数的功能、输入参数以及输出结果的 **类型**。下面是通用的函数定义语法：

```c
返回值类型 函数名(参数类型 参数名, ...) {
    // 函数体
    return 返回值;
}
```

这样可能有点抽象，下面是一个最简单的例子：

```c
int add(int a, int b) {
    return a + b;
}
```

让我们让我们根据通用公式来仔细看看这个函数都干了什么：

- 函数名：`add`
- 返回值类型：`int`
- 参数类型：`int a` 和 `int b`
- 函数体：`return a + b;`

该函数 `add` 接受两个 `int` 类型的参数 `a` 和 `b`，返回它们的和，返回值类型为 `int`。

### 声明（前置声明）

函数声明（或称原型）是告诉编译器函数的名称、返回类型和参数类型，但不需要给出具体实现。通常，函数声明放在文件的开头或头文件中，因此也被称为前置声明

```c
int add(int a, int b);
```

像上面的代码，我们只告诉了编译器函数的形式，但是没有具体的实现，这个需要我们在后面补齐。

## 调用

定义函数后，如果不去调用，那么只能是无用的代码，那么，我们该如何调用呢？调用时需要给定匹配的参数：

通用公式：

```c
返回值类型 返回值名称 = 函数名(参数);
```

- 返回值是 **可选** 的
- 参数必须与函数定义中的一一对应

我们继续以上面实现好的`add`函数为例子

```c
int result = add(5, 3); // 调用函数，result 得到 8
```

## 参数传递

### 值传递

默认情况下，C 语言中的函数参数是按值传递的，即函数内部得到的是参数值的副本，函数的修改不会影响原始数据。

```c
void changeValue(int x) {
    x = 10;
}

int main() {
    int a = 5;
    changeValue(a); // 传递 a 的副本
    printf("%d", a); // 输出仍然是 5
    return 0;
}
```

### 引用传递（指针传递）

通过传递指针，可以使函数内部修改参数的值，从而影响原始变量。

```c
void changeValue(int *x) {
    *x = 10;
}

int main() {
    int a = 5;
    changeValue(&a); // 传递 a 的地址
    printf("%d", a); // 输出 10
    return 0;
}
```

## 返回值

C 语言的函数可以返回一个值，返回值类型在函数定义时指定。返回值通过 return 语句返回给调用者。函数只能返回一个值，但可以通过指针返回多个值（或使用结构体等）。

```c
int max(int x, int y) {
    if (x > y)
        return x;
    else
        return y;
}
```

如果函数不需要返回值，返回类型应该声明为 `void`。

```c
void printHello() {
    printf("Hello, World!\n");
}
```

## 递归

递归是函数调用自身的机制。递归函数必须有终止条件，否则会陷入无限循环。

```c
int factorial(int n) {
    if (n == 0)
        return 1; // 终止条件
    return n * factorial(n - 1);
}
```

例如，factorial(5) 的计算过程如下：

```txt
factorial(5) = 5 _ factorial(4)
factorial(4) = 4 _ factorial(3)
factorial(3) = 3 _ factorial(2)
factorial(2) = 2 _ factorial(1)
factorial(1) = 1 \* factorial(0)
factorial(0) = 1
```

最终返回 120.

## 变量作用域和生命周期

### 局部变量

在函数内部声明的变量，只能在函数内部访问，函数执行完毕后销毁。

```c
void foo() {
    int x = 10; // 局部变量
    printf("%d", x);
}

int main() {
    foo(); // 输出 10
    printf("%d", x); // 报错，x 未定义
}
```

### 全局变量

在所有函数外部声明的变量，可以被程序中所有函数访问，直到程序结束时才销毁。

```c
int globalVar = 10; // 全局变量

void foo() {
    printf("%d", globalVar); // 可以访问全局变量
}

int main() {
    foo(); // 输出 10
    printf("%d", globalVar); // 输出 10
}
```

### 静态变量

通过 `static` 关键字声明的局部变量，生命周期贯穿程序运行始终，但只能在函数内部访问。

```c
void foo() {
    static int count = 0; // 静态变量
    count++;
    printf("%d", count); // 每次调用后，count 都会递增
}

int main() {
    foo(); // 输出 1
    foo(); // 输出 2
    foo(); // 输出 3
}
```

## 函数指针

函数指针是一种可以指向函数的指针，用于函数的动态调用或作为 **参数** 传递给其他函数。

```c
int add(int a, int b) {
    return a + b;
}

int main() {
    int (*funcPtr)(int, int) = add; // 定义函数指针
    int result = funcPtr(5, 3); // 通过指针调用函数
    printf("%d", result); // 输出 8
    return 0;
}
```

函数指针使得程序更灵活，尤其在实现 **回调函数** 时非常有用。

## 可变参数函数

C 语言允许定义接受可变数量参数的函数，如标准库中的 printf。这种函数使用 `<stdarg.h>` 中的宏来处理参数列表。

```c
#include <stdarg.h>

void printNumbers(int count, ...) {
    va_list args;
    va_start(args, count);
    for (int i = 0; i < count; i++) {
        int num = va_arg(args, int);
        printf("%d ", num);
    }
    va_end(args);
}

int main() {
    printNumbers(3, 1, 2, 3); // 输出：1 2 3
    return 0;
}
```

## 内联函数

通过 `inline` 关键字，C 语言可以将小函数扩展为内联函数，避免函数调用的开销，从而提高程序性能。

```c
inline int square(int x) {
    return x * x;
}
```

内联函数的定义建议放在头文件中，因为它要求编译器在调用时能够直接看到函数实现。

## 函数重载（C 语言不支持，你可能需要神奇的 C++）

C 语言不支持函数重载，即不能像 C++ 那样定义多个同名函数并根据参数类型或数量来区分。在 C 语言中，函数名必须 **唯一** 。若要实现类似的功能，可以通过不同的函数名或使用可变参数来处理。

例如，C++ 中可以这样做：

```cpp
int add(int a, int b) { return a + b; }
double add(double a, double b) { return a + b; }
```

而在 C 中，你需要使用不同的函数名来区分：

```c
int add_int(int a, int b) { return a + b; }
double add_double(double a, double b) { return a + b; }
```

## 函数返回指针

C 语言函数可以返回指针，这是动态分配内存或返回数组等数据结构的常见方法。但需要注意指针的生命周期问题，避免返回无效的内存地址。

**返回局部变量的指针（危险！）**

局部变量在函数结束后被销毁，返回它们的指针会导致`悬空指针`（dangling pointer），这会引发 `未定义行为`。

```c
int* wrongFunction() {
    int localVar = 10;
    return &localVar; // 错误：局部变量的生命周期结束后，内存不可再访问
}
```

动态分配内存并返回指针（正确做法）

通过 `malloc` 分配动态内存并返回指针，确保在函数外部能够继续使用这些数据。

```c
int* createArray(int size) {
    int *arr = (int *)malloc(size * sizeof(int));
    if (arr == NULL) {
        printf("Memory allocation failed\n");
        return NULL;
    }
    return arr;
}
```

调用者需要负责释放动态分配的内存：

```c
int main() {
    int *arr = createArray(10);
    if (arr != NULL) {
        // 使用数组
        free(arr);  // 释放内存
    }
    return 0;
}
```

## 宏与函数

C 语言中的宏是通过预处理器进行文本替换的，因此它们可以像函数一样使用。但宏与函数不同，宏的展开不会进行类型检查，并且可能引发难以发现的错误。

```c
#define SQUARE(x) ((x) * (x))

int main() {
    int a = 5;
    int result = SQUARE(a);  // 正常
    int b = 2;
    int result2 = SQUARE(a + b);  // 意外：((a + b) * (a + b)) -> (7 * 7) = 49，而不是 (a^2 + 2ab + b^2)
    return 0;
}
```

使用内联函数可以避免宏展开的问题，同时保留性能优势：

```c
inline int square(int x) {
    return x * x;
}
```

## 静态函数

在 C 语言中，static 关键字不仅可以修饰变量，还可以修饰函数。如果将函数声明为 `static`，它的作用域将仅限于当前源文件。这样可以实现文件内函数的封装，避免其他文件误用该函数。

```c
static void helperFunction() {
    printf("This is a static function\n");
}

int main() {
    helperFunction();  // 可以在当前文件中调用
    return 0;
}
```

静态函数 **不会** 暴露给其他文件，确保了实现细节的隐藏。

## 变参宏与变参函数的结合

结合 C 语言的变参机制（<stdarg.h>）和宏，可以实现更灵活的函数定义。例如，printf 的实现就是通过变参函数来处理任意数量的参数。

```c
#include <stdarg.h>
#include <stdio.h>

void myPrint(const char \*format, ...) {
    va_list args;
    va_start(args, format);
    vprintf(format, args); // vprintf 是处理变参的标准函数
    va_end(args);
}

int main() {
    myPrint("Hello %s, you have %d new messages\n", "Alice", 5);
    return 0;
}
```

这种机制常用于日志函数或调试工具中，可以处理不同类型和数量的输入。

## 注意点

在 C 语言中，函数操作相关的未定义行为主要涉及函数调用、参数传递、返回值、指针操作等方面。以下是与函数相关的常见注意事项和可能导致未定义行为的操作。

### 不匹配的函数指针调用

在 C 语言中，函数指针允许将函数地址赋值给指针并调用函数。但如果使用不匹配的函数指针类型调用函数，则会产生未定义行为。例如，将一个返回 int 类型的函数赋值给一个返回 void 的函数指针并调用时，会导致未定义行为。

```c
void myFunction() {
    printf("This is a void function\n");
}

int main() {
    int (*funcPtr)(void) = (int (*)(void))myFunction;  // 错误的强制类型转换
    funcPtr();  // 调用不匹配的函数指针，未定义行为
    return 0;
}
```

### 递归函数无终止条件

递归函数在调用自身时，如果没有正确的终止条件，将导致无限递归，最终导致栈溢出。这种栈溢出虽然不是严格意义上的未定义行为，但通常会引发程序崩溃。

```c
void faultyRecursion() {
    faultyRecursion();  // 没有终止条件的递归
}

int main() {
    faultyRecursion();  // 导致栈溢出，崩溃
    return 0;
}
```

解决方法：确保递归函数有明确的终止条件。

```c
void properRecursion(int count) {
    if (count <= 0) return;  // 递归终止条件
    properRecursion(count - 1);
}
```

### 调用 main 函数

C 语言标准规定，main 函数是程序的入口点，不应被显式调用。如果在程序中显式调用 main 函数，行为未定义。

```c
int main() {
    main();  // 显式调用 main 函数，未定义行为
    return 0;
}
```

### 未匹配的参数类型

C 语言在函数调用时不会检查参数的类型匹配，如果函数的声明和定义的参数类型不一致，可能导致未定义行为。例如，传递一个 int 指针给一个期望 float 指针的函数。

```c
void func(float *f) {
    // ...
}

int main() {
    int x = 5;
    func(&x);  // 传递不匹配的指针类型，未定义行为
    return 0;
}
```

### 未返回值的非 void 函数（相当常见的错误）

如果一个非 void 类型的函数在某些路径下没有返回值，编译器可能不会捕捉到这个错误，从而导致未定义行为。

```c
int faultyFunction(int x) {
    if (x > 0)
        return x;  // 仅在 x > 0 时返回值
    // x <= 0 时未返回任何值，未定义行为
}
```

解决方法：确保非 void 函数在所有可能的路径中都有明确的返回值。

```c
int properFunction(int x) {
    if (x > 0)
        return x;
    return 0;  // 为所有路径定义返回值
}
```

### 递归函数使用静态变量

递归函数使用静态变量时要小心，静态变量在整个程序生命周期内只有一份。如果递归函数修改静态变量，会导致意外的结果，甚至未定义行为，特别是在并发或递归调用中。

```c
void faultyRecursion(int n) {
    static int counter = 0;  // 静态变量在每次递归调用中共享
    counter++;
    if (n > 0) faultyRecursion(n - 1);
    printf("%d\n", counter);
}

int main() {
    faultyRecursion(3);  // 静态变量共享，递归结果不正确
    return 0;
}
```

## 总结

函数是 C 语言的核心机制，掌握函数的定义、声明、调用、参数传递、返回值、递归、变量作用域和生命周期、函数指针、宏与函数、静态函数、变参宏与变参函数的结合等知识点，可以帮助我们更好地理解和使用 C 语言。未来可期啊！
