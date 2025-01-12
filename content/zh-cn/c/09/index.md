---
title: 'Day9 - 文件操作'
---

文件操作是 C 语言中的一项重要功能，用于实现对磁盘文件的读写、创建、修改等操作。C 语言通过标准库函数提供了强大的文件操作支持，这些函数位于头文件 `<stdio.h>` 中。

## 文件的基本概念

文件是存储在磁盘上的数据集合，可以是文本文件或二进制文件：

- **文本文件**：以可读字符存储（ASCII/UTF-8 等编码），适合人类阅读。
- **二进制文件**：以原始数据的形式存储，不能直接阅读。

在 C 语言中，文件操作基于文件指针 `FILE*`，用于引用文件。

## 文件操作的基本步骤

文件操作的基本流程分为四步：

1. **打开文件**（`fopen()` 或 `freopen()`）
2. **读/写文件**（如：`fscanf()`, `fprintf()`, `fread()`, `fwrite()` 等）
3. **关闭文件**（`fclose()`）
4. **处理错误**（通过返回值或 `ferror()` 检测）

## 文件指针

`FILE*` 是一个指向文件的指针，用于操作文件。打开文件后，会返回一个指向文件流的指针，该指针用于后续的读写操作。

## 文件打开与关闭

### 打开文件：`fopen()`

**语法**：

```c
FILE* fopen(const char* filename, const char* mode);
```

**参数说明**：

- `filename`：文件名（包含路径）
- `mode`：文件的打开模式

**常见的文件模式**：

| 模式   | 含义                                      |
| ------ | ----------------------------------------- |
| `"r"`  | 以只读模式打开文件，文件必须存在          |
| `"w"`  | 以写入模式打开文件，若文件存在则清空内容  |
| `"a"`  | 以追加模式打开文件，不存在则创建          |
| `"r+"` | 以读写模式打开文件，文件必须存在          |
| `"w+"` | 以读写模式打开文件，若文件存在则清空内容  |
| `"a+"` | 以读写模式打开文件，从文件尾部追加数据    |
| `"b"`  | 二进制模式（可与以上模式组合，如 `"rb"`） |

**示例**：

```c
FILE* fp = fopen("example.txt", "w");
if (fp == NULL) {
    printf("文件打开失败！\n");
    return 1;
}
```

### 关闭文件：`fclose()`

**语法**：

```c
int fclose(FILE* stream);
```

关闭文件并释放资源。成功返回 `0`，失败返回 `EOF`。

**示例**：

```c
fclose(fp);
```

### 二合一

```c
#include <stdio.h>

int main() {
    FILE* fp = fopen("example.txt", "w");  // 以写入模式打开文件

    if (fp == NULL) {  // 检查文件是否打开成功
        printf("文件打开失败！\n");
        return 1;
    }

    printf("文件打开成功！\n");

    fclose(fp);  // 关闭文件
    printf("文件关闭成功！\n");

    return 0;
}
```

## 文件读写

### 文本文件的读写

#### 写入文本文件：`fprintf()`

用于以格式化文本的方式写入数据。

**语法**：

```c
int fprintf(FILE* stream, const char* format, ...);
```

**示例**：

```c
FILE* fp = fopen("example.txt", "w");
if (fp != NULL) {
    fprintf(fp, "姓名：%s\n年龄：%d\n", "张三", 25);
    fclose(fp);
}
```

#### 读取文本文件：`fscanf()`

用于以格式化文本的方式读取数据。

**语法**：

```c
int fscanf(FILE* stream, const char* format, ...);
```

**示例**：

```c
FILE* fp = fopen("example.txt", "r");
char name[50];
int age;
if (fp != NULL) {
    fscanf(fp, "姓名：%s\n年龄：%d\n", name, &age);
    printf("姓名：%s, 年龄：%d\n", name, age);
    fclose(fp);
}
```

#### 字符操作：`fgetc()` 和 `fputc()`

- `fgetc(FILE* stream)`：从文件中读取一个字符
- `fputc(int ch, FILE* stream)`：向文件中写入一个字符

**示例**：

```c
FILE* fp = fopen("example.txt", "w");
if (fp != NULL) {
    fputc('A', fp);  // 写入字符 'A'
    fclose(fp);
}

fp = fopen("example.txt", "r");
if (fp != NULL) {
    char ch = fgetc(fp);  // 读取字符
    printf("读取的字符：%c\n", ch);
    fclose(fp);
}
```

#### 行操作：`fgets()` 和 `fputs()`

- `fgets(char* str, int n, FILE* stream)`：读取一行，最多读取 `n-1` 个字符
- `fputs(const char* str, FILE* stream)`：写入一行

**示例**：

```c
FILE* fp = fopen("example.txt", "w");
if (fp != NULL) {
    fputs("Hello, World!\n", fp);
    fclose(fp);
}

fp = fopen("example.txt", "r");
if (fp != NULL) {
    char line[100];
    fgets(line, sizeof(line), fp);  // 读取一行
    printf("读取的行：%s", line);
    fclose(fp);
}
```

#### 两个例子

```c
#include <stdio.h>

int main() {
    FILE* fp = fopen("example.txt", "w");  // 以写模式打开文件
    if (fp == NULL) {
        printf("文件打开失败！\n");
        return 1;
    }

    fprintf(fp, "姓名：%s\n", "张三");
    fprintf(fp, "年龄：%d\n", 25);  // 写入文本

    fclose(fp);  // 关闭文件
    printf("写入完成！\n");

    return 0;
}
```

```c
#include <stdio.h>

int main() {
    FILE* fp = fopen("example.txt", "r");  // 以读模式打开文件
    if (fp == NULL) {
        printf("文件打开失败！\n");
        return 1;
    }

    char name[50];
    int age;
    fscanf(fp, "姓名：%s\n", name);
    fscanf(fp, "年龄：%d\n", &age);  // 按格式读取内容

    printf("姓名：%s\n", name);
    printf("年龄：%d\n", age);

    fclose(fp);  // 关闭文件
    return 0;
}
```

### 二进制文件的读写

#### 写入二进制文件：`fwrite()`

用于将数据块写入文件。

**语法**：

```c
size_t fwrite(const void* ptr, size_t size, size_t count, FILE* stream);
```

**示例**：

```c
FILE* fp = fopen("data.bin", "wb");
int data[] = {1, 2, 3, 4, 5};
fwrite(data, sizeof(int), 5, fp);
fclose(fp);
```

#### 读取二进制文件：`fread()`

用于从文件中读取数据块。

**语法**：

```c
size_t fread(void* ptr, size_t size, size_t count, FILE* stream);
```

**示例**：

```c
FILE* fp = fopen("data.bin", "rb");
int data[5];
fread(data, sizeof(int), 5, fp);
for (int i = 0; i < 5; i++) {
    printf("%d ", data[i]);
}
fclose(fp);
```

```c
#include <stdio.h>

int main() {
    FILE* fp = fopen("data.bin", "wb");  // 打开二进制文件
    if (fp == NULL) {
        printf("文件打开失败！\n");
        return 1;
    }

    int numbers[] = {10, 20, 30, 40, 50};
    fwrite(numbers, sizeof(int), 5, fp);  // 写入5个整数

    fclose(fp);
    printf("二进制数据写入完成！\n");

    return 0;
}
```

```c
#include <stdio.h>

int main() {
    FILE* fp = fopen("data.bin", "rb");  // 打开二进制文件
    if (fp == NULL) {
        printf("文件打开失败！\n");
        return 1;
    }

    int numbers[5];
    fread(numbers, sizeof(int), 5, fp);  // 读取5个整数

    for (int i = 0; i < 5; i++) {
        printf("数字%d: %d\n", i + 1, numbers[i]);
    }

    fclose(fp);
    return 0;
}
```

## 文件指针的位置操作

### (1) `ftell()`

返回当前文件指针的位置。

**语法**：

```c
long ftell(FILE* stream);
```

### (2) `fseek()`

移动文件指针到指定位置。

**语法**：

```c
int fseek(FILE* stream, long offset, int origin);
```

**示例**：

```c
FILE* fp = fopen("example.txt", "r");
fseek(fp, 10, SEEK_SET);  // 将指针移动到文件的第10个字节
printf("当前位置：%ld\n", ftell(fp));
fclose(fp);
```

### (3) `rewind()`

将文件指针重置到文件开头。

**语法**：

```c
void rewind(FILE* stream);
```

```c
#include <stdio.h>

int main() {
    FILE* fp = fopen("example.txt", "r");
    if (fp == NULL) {
        printf("文件打开失败！\n");
        return 1;
    }

    fseek(fp, 5, SEEK_SET);  // 将指针移动到文件的第5个字节
    long pos = ftell(fp);   // 获取当前指针位置
    printf("当前指针位置：%ld\n", pos);

    fclose(fp);
    return 0;
}
```

## 7. 文件操作的错误处理

### (1) `ferror()`

检查文件操作是否出错。

**语法**：

```c
int ferror(FILE* stream);
```

### (2) `perror()`

打印文件操作错误信息。

**语法**：

```c
void perror(const char* str);
```

```c
#include <stdio.h>

int main() {
    FILE* fp = fopen("nonexistent.txt", "r");  // 尝试打开不存在的文件
    if (fp == NULL) {
        perror("文件打开失败");  // 打印错误信息
        return 1;
    }

    char ch = fgetc(fp);
    if (ferror(fp)) {  // 检查是否发生错误
        printf("文件读取出错！\n");
    }

    fclose(fp);
    return 0;
}
```

## 8. 常见问题与注意事项

- 文件打开失败要检查返回值是否为 `NULL`。
- 文件操作完成后务必关闭文件以释放资源。
- 注意文件读写模式的正确选择，否则可能导致数据丢失。
- 处理二进制文件时要确保读取/写入的数据大小匹配。

以上代码涵盖了所有常见的 C 语言文件操作，并且每个功能都配有完整的示例代码，可以直接运行测试。
