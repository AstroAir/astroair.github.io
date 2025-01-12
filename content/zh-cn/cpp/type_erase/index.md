---
title: C++ 类型擦除与相关技术
---

## 基本概念

### 什么是类型擦除

类型擦除是一种设计模式，用于实现一个通用接口而隐藏具体数据类型的信息。在类型擦除的机制下，客户端代码不需要关心对象的具体类型，只需要使用抽象接口即可。这样，代码提高了灵活性和可复用性。

### 类型擦除的优势

- **增强灵活性**：同一段代码能够处理多种不同的类型。
- **简化 API 设计**：为用户提供一致的接口，隐藏复杂的实现细节。
- **避免代码重复**：减少由于多个相似类型实现同样接口而导致的代码冗余。

## 类型擦除的实现

### 使用基类和虚函数

通过基类和虚函数实现类型擦除是最常见的方式。基类定义虚函数接口，派生类实现具体的功能。通过基类指针或引用调用虚函数时，实际调用的是派生类的实现。

```cpp
#include <iostream>

class Base {
public:
    virtual void print() const = 0;
    virtual ~Base() = default;
};

class DerivedInt : public Base {
    int value;
public:
    DerivedInt(int v) : value(v) {}
    void print() const override {
        std::cout << "Int: " << value << std::endl;
    }
};

class DerivedString : public Base {
    std::string value;
public:
    DerivedString(const std::string& v) : value(v) {}
    void print() const override {
        std::cout << "String: " << value << std::endl;
    }
};

int main() {
    Base* b1 = new DerivedInt(42);
    Base* b2 = new DerivedString("Hello");

    b1->print(); // 输出：Int: 42
    b2->print(); // 输出：String: Hello

    delete b1;
    delete b2;

    return 0;
}
```

### `std::any`

C++17 引入了 `std::any` 类，允许存储任意类型的对象，并提供类型擦除机制。`std::any` 是一种类型安全的容器，可以在运行时动态存储和访问不同类型的值。

```cpp
#include <iostream>
#include <any>
#include <vector>

void processAny(const std::any& value) {
    if (value.type() == typeid(int)) {
        std::cout << "Processing int: " << std::any_cast<int>(value) << std::endl;
    } else if (value.type() == typeid(double)) {
        std::cout << "Processing double: " << std::any_cast<double>(value) << std::endl;
    } else if (value.type() == typeid(std::string)) {
        std::cout << "Processing string: " << std::any_cast<std::string>(value) << std::endl;
    }
}

int main() {
    std::vector<std::any> values;
    values.push_back(10);
    values.push_back(3.14);
    values.push_back(std::string("Hello"));

    for (const auto& value : values) {
        processAny(value); 
    }

    return 0;
}
```

使用 `std::any` 可以存储任意类型的值，提供了动态类型存储的能力。可以通过 `std::any_cast` 进行类型安全的装箱与取出。

### `std::function`

`std::function` 是一个通用的类型擦除机制，用于存储可调用对象（如函数指针、lambda 表达式、绑定表达式等）。它允许使用一种统一的接口来调用不同签名的函数或可调用类型。

```cpp
#include <iostream>
#include <functional>
#include <vector>

void printInt(int value) {
    std::cout << "Integer: " << value << std::endl;
}

void printDouble(double value) {
    std::cout << "Double: " << value << std::endl;
}

int main() {
    std::vector<std::function<void()>> functions;

    // 使用 std::function 将不同类型的函数存储在同一容器中
    functions.push_back([] { printInt(10); });  // Lambda capturing int
    functions.push_back([] { printDouble(2.5); }); // Lambda capturing double

    // 调用所有的函数
    for (const auto& func : functions) {
        func(); // 输出 Integer: 10\n Double: 2.5
    }

    return 0;
}
```

### CRTP（Curiously Recurring Template Pattern）

CRTP 是一种在 C++ 中使用的编程模式，通过将派生类作为模板参数传递给基类来达到静态多态的目的。这个模式的主要特点是可以在编译期进行类型确定，并借助 C++ 的模板机制来实现直接的函数调用而无需虚函数表，从而提供高效的多态实现。

#### 实现模板

```cpp
template <typename Derived>
class Base {
public:
    void interface() {
        // 在基类中可以调用派生类的函数
        static_cast<Derived*>(this)->implementation();
    }
};

class Derived : public Base<Derived> {
public:
    void implementation() {
        // 实现细节
    }
};
```

在这个示例中，`Base` 是一个模板类，接受一个模板参数 `Derived`。当我们实现 `interface()` 函数时，我们可以安全地调用派生类的 `implementation()` 函数。

#### 典型用法

##### 静态多态

CRTP 允许在编译期间代替运行时多态，消除了虚函数的调用开销。当派生类通过 CRTP 继承基类时，所有函数都是编译时解析的。

```cpp
#include <iostream>

// 定义 CRTP 基类
template <typename Derived>
class Base {
public:
    void interface() {
        static_cast<Derived*>(this)->implementation(); // 调用派生类的实现
    }
};

// 派生类 A
class DerivedA : public Base<DerivedA> {
public:
    void implementation() {
        std::cout << "DerivedA implementation" << std::endl;
    }
};

// 派生类 B
class DerivedB : public Base<DerivedB> {
public:
    void implementation() {
        std::cout << "DerivedB implementation" << std::endl;
    }
};

int main() {
    DerivedA a;
    DerivedB b;

    a.interface(); // 输出：DerivedA implementation
    b.interface(); // 输出：DerivedB implementation

    return 0;
}
```

##### 静态策略模式

CRTP 可以结合策略模式，以实现不同的行为，而无需引入虚函数开销。可以在基类中定义通用接口，而在派生类中实现不同的策略。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

template <typename SortAlgorithm>
class Sorter {
public:
    void sort(std::vector<int>& data) {
        static_cast<SortAlgorithm*>(this)->sort(data);
    }
};

class BubbleSort : public Sorter<BubbleSort> {
public:
    void sort(std::vector<int>& data) {
        std::cout << "BubbleSort" << std::endl;
        for (size_t i = 0; i < data.size() - 1; i++) {
            for (size_t j = 0; j < data.size() - i - 1; j++) {
                if (data[j] > data[j + 1]) {
                    std::swap(data[j], data[j + 1]);
                }
            }
        }
    }
};

class SelectionSort : public Sorter<SelectionSort> {
public:
    void sort(std::vector<int>& data) {
        std::cout << "SelectionSort" << std::endl;
        for (size_t i = 0; i < data.size() - 1; i++) {
            size_t minIndex = i;
            for (size_t j = i + 1; j < data.size(); j++) {
                if (data[j] < data[minIndex]) {
                    minIndex = j;
                }
            }
            std::swap(data[i], data[minIndex]);
        }
    }
};

int main() {
    std::vector<int> data = {5, 3, 2, 4, 1};

    BubbleSort bubble_sort;
    bubble_sort.sort(data); // 输出「BubbleSort」

    SelectionSort selection_sort;
    selection_sort.sort(data); // 输出「SelectionSort」

    return 0;
}
```

#### 优缺点

##### 优点

1. **性能**：由于 CRTP 是一种编译期机制，它消除了虚函数的调用开销，因此通常会比传统的虚函数实现多态更快。
2. **类型安全**：CRTP 提供了强类型检查，避免了与 `void*` 或其他类型擦除方法相关的运行时错误。
3. **灵活性**：允许在基类中定义模板参数类型，并可使用任何类型作为参数，包含派生类本身。

##### 缺点

1. **复杂性**：CRTP 可能使得代码更加复杂，特别是对于不熟悉模板编程的开发者而言，理解 CRTP 的实现细节可能较为困难。
2. **可读性**：使用 CRTP 的代码在类型推断和模板参数方面可能使得代码的可读性降低，尤其是与其他类型的混合使用时。
3. **不能在运行时多态**：CRTP 只能提供编译时多态，无法动态决定类型，限制了其使用场景。

### `std::variant`

`std::variant` 是 C++17 标准库中引入的一种类型，它是一种类型安全的联合体（union），可以保存多种不同类型的值，但在任何时候只能持有其中一种类型。它提供了类似于类型擦除的能力，并能够更安全地处理多态和变体类型。

#### 基本用法

```cpp
#include <iostream>
#include <variant>
#include <string>

int main() {
    std::variant<int, double, std::string> v; // v 可以存储 int、double 或 std::string

    v = 42; // 存储 int
    std::cout << std::get<int>(v) << std::endl; // 输出：42

    v = 3.14; // 存储 double
    std::cout << std::get<double>(v) << std::endl; // 输出：3.14

    v = "Hello Variant"; // 存储 string
    std::cout << std::get<std::string>(v) << std::endl; // 输出：Hello Variant

    return 0;
}
```

当访问 `std::variant` 中的值时，可以使用 `std::get` 函数、`std::get_if` 或者访问访式来提取存储的值。

```cpp
#include <iostream>
#include <variant>

std::variant<int, double, std::string> v;

// 存储一个 int
v = 10;

// 使用 std::get 来访问
try {
    int value = std::get<int>(v); // 成功
    std::cout << "Integer value: " << value << std::endl;

    // 试图访问不相关的类型，将抛出异常
    double d = std::get<double>(v); // 这个会抛出 std::bad_variant_access 异常
} catch (const std::bad_variant_access& e) {
    std::cout << "Caught exception: " << e.what() << std::endl;
}

// 使用 std::get_if
if (auto str_ptr = std::get_if<std::string>(&v)) {
    std::cout << "String value: " << *str_ptr << std::endl;
} else {
    std::cout << "Variant does not hold a string." << std::endl;
}
```

当尝试以不匹配的类型访问值时，抛出 `std::bad_variant_access` 异常，因此能够确保安全性。`std::get_if` 可以返回指向存储在 `std::variant` 中类型的指针，如果类型不匹配，则返回 `nullptr`。

#### 访问持有的类型

当我们需要根据持有的类型进行不同操作时，可以使用 `std::visit`，这是方式之一来安全地访问 `std::variant` 中的值。

```cpp
#include <iostream>
#include <variant>

void visitor_function(const std::variant<int, double, std::string>& v) {
    std::visit([](auto&& arg) {
        std::cout << "The value is: " << arg << std::endl;
    }, v);
}

int main() {
    std::variant<int, double, std::string> v = "Hello, Visitor!";
    visitor_function(v); // 输出：The value is: Hello, Visitor!

    return 0;
}
```

## 总结

类型擦除是一种强大的设计模式，能够提高代码的灵活性和可复用性。C++ 提供了多种实现类型擦除的方式，包括基类和虚函数、`std::any`、`std::function`、CRTP 和 `std::variant`。每种方式都有其适用的场景和优缺点，开发者可以根据具体需求选择合适的技术来实现类型擦除。
