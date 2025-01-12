---
title: 'Day8 - 链表'
---

链表（Linked List）是 C 语言中一种常见的数据结构，属于线性数据结构。它不同于数组，链表的长度可以动态变化，适合需要频繁插入、删除元素的场景，当然，也是大学 c 语言考试中不可缺少的一部分。下面将详细介绍链表的原理、类型、实现方式、应用及其优缺点。

## 基本概念

链表由多个节点（Node）组成，每个节点包含两个部分：

- **数据域（Data）**：存储节点的数据。
- **指针域（Pointer）**：指向下一个节点的地址。

链表通过指针将各个节点串联起来，形成一个链式结构。

## 分类

链表根据结构的不同，可以分为以下几种类型：

### 单向链表（Singly Linked List）

- 每个节点只有一个指针，指向下一个节点。
- 适合从头到尾的单向遍历。
- 最后一个节点的指针为 `NULL`。

### 双向链表（Doubly Linked List）

- 每个节点有两个指针：一个指向前一个节点，另一个指向后一个节点。
- 支持双向遍历，插入和删除操作更灵活。
- 需要额外的内存来存储两个指针。

### 循环链表（Circular Linked List）

- 尾节点的指针指向头节点，形成一个环状结构。
- 可以是单向循环链表，也可以是双向循环链表。
- 适用于需要循环访问的场景。

### 多重链表（Multi Linked List）

- 每个节点可以有多个指针，形成复杂的链表结构。
- 常用于表示图、稀疏矩阵等复杂数据结构。

## 链表与数组的比较

| 特性      | 链表                 | 数组                   |
| --------- | -------------------- | ---------------------- |
| 存储方式  | 动态分配，分散存储   | 静态分配，连续存储     |
| 访问时间  | O(n)，需逐个节点访问 | O(1)，可直接访问索引   |
| 插入/删除 | 高效，不需要移动元素 | 低效，需要移动元素     |
| 内存使用  | 节点需额外的指针空间 | 仅存储数据，无额外开销 |

## 单向链表

### 节点定义

单向链表的节点定义如下：

```c
#include <stdio.h>
#include <stdlib.h>

// 定义单向链表的节点结构
struct Node {
    int data;           // 数据域
    struct Node* next;  // 指针域，指向下一个节点
};
```

### 创建节点

节点的内存通过 `malloc` 函数动态分配：

```c
struct Node* createNode(int data) {
    struct Node* newNode = (struct Node*)malloc(sizeof(struct Node));
    if (newNode == NULL) {
        printf("内存分配失败\n");
        exit(1);
    }
    newNode->data = data; // 设置数据域
    newNode->next = NULL; // 初始化指针域
    return newNode;
}
```

### 基本操作

#### 插入节点

在链表头部插入

```c
struct Node* insertAtHead(struct Node* head, int data) {
    struct Node* newNode = createNode(data);
    newNode->next = head; // 新节点的指针指向旧的头节点
    return newNode;       // 返回新的头节点
}
```

在链表尾部插入

```c
void insertAtTail(struct Node** head, int data) {
    struct Node* newNode = createNode(data);
    if (*head == NULL) {
        *head = newNode;
        return;
    }

    struct Node* temp = *head;
    while (temp->next != NULL) {
        temp = temp->next;
    }
    temp->next = newNode;
}
```

#### 删除节点

删除第一个匹配指定值的节点

```c
struct Node* deleteNode(struct Node* head, int key) {
    struct Node* temp = head;
    struct Node* prev = NULL;

    // 处理头节点
    if (temp != NULL && temp->data == key) {
        head = temp->next;
        free(temp);
        return head;
    }

    // 查找要删除的节点
    while (temp != NULL && temp->data != key) {
        prev = temp;
        temp = temp->next;
    }

    if (temp == NULL) return head; // 未找到节点

    prev->next = temp->next; // 跳过要删除的节点
    free(temp);              // 释放内存
    return head;
}
```

#### 遍历链表

通过循环逐个访问节点

```c
void printList(struct Node* head) {
    struct Node* temp = head;
    while (temp != NULL) {
        printf("%d -> ", temp->data);
        temp = temp->next;
    }
    printf("NULL\n");
}
```

### 完整代码

```c
#include <stdio.h>
#include <stdlib.h>

// 定义链表节点结构
struct Node {
    int data;
    struct Node* next;
};

// 创建新节点
struct Node* createNode(int data) {
    struct Node* newNode = (struct Node*)malloc(sizeof(struct Node));
    newNode->data = data;
    newNode->next = NULL;
    return newNode;
}

// 头部插入
struct Node* insertAtHead(struct Node* head, int data) {
    struct Node* newNode = createNode(data);
    newNode->next = head;
    return newNode;
}

// 遍历链表
void printList(struct Node* head) {
    struct Node* temp = head;
    while (temp != NULL) {
        printf("%d -> ", temp->data);
        temp = temp->next;
    }
    printf("NULL\n");
}

// 主函数
int main() {
    struct Node* head = NULL; // 初始化链表为空

    // 插入元素
    head = insertAtHead(head, 10);
    head = insertAtHead(head, 20);
    head = insertAtHead(head, 30);

    printf("链表内容：\n");
    printList(head);

    return 0;
}
```

## 双向链表

在双向链表中，每个节点包含三个部分：

- **数据域（data）**
- **指向前一个节点的指针（prev）**
- **指向后一个节点的指针（next）**

```c
#include <stdio.h>
#include <stdlib.h>

// 定义双向链表的节点结构
struct DNode {
    int data;              // 数据域
    struct DNode* prev;    // 指向前一个节点
    struct DNode* next;    // 指向后一个节点
};
```

### 创建新节点

```c
struct DNode* createNode(int data) {
    struct DNode* newNode = (struct DNode*)malloc(sizeof(struct DNode));
    if (newNode == NULL) {
        printf("内存分配失败\n");
        exit(1);
    }
    newNode->data = data;
    newNode->prev = NULL;
    newNode->next = NULL;
    return newNode;
}
```

### 插入操作

#### 在链表头部插入

```c
struct DNode* insertAtHead(struct DNode* head, int data) {
    struct DNode* newNode = createNode(data);
    if (head != NULL) {
        head->prev = newNode;
        newNode->next = head;
    }
    return newNode; // 新节点成为头节点
}
```

#### 在链表尾部插入

```c
void insertAtTail(struct DNode* head, int data) {
    struct DNode* newNode = createNode(data);
    struct DNode* temp = head;

    while (temp->next != NULL) { // 找到尾节点
        temp = temp->next;
    }

    temp->next = newNode;
    newNode->prev = temp;
}
```

### 删除节点

删除包含指定值的节点

```c
struct DNode* deleteNode(struct DNode* head, int key) {
    struct DNode* temp = head;

    // 查找要删除的节点
    while (temp != NULL && temp->data != key) {
        temp = temp->next;
    }

    if (temp == NULL) return head; // 未找到节点

    if (temp->prev != NULL) {
        temp->prev->next = temp->next; // 更新前节点的next
    } else {
        head = temp->next; // 删除的是头节点
    }

    if (temp->next != NULL) {
        temp->next->prev = temp->prev; // 更新后节点的prev
    }

    free(temp);
    return head;
}
```

### 遍历链表

从头到尾遍历

```c
void printList(struct DNode* head) {
    struct DNode* temp = head;
    printf("正向遍历：\n");
    while (temp != NULL) {
        printf("%d -> ", temp->data);
        temp = temp->next;
    }
    printf("NULL\n");
}
```

### 完整示例

```c
#include <stdio.h>
#include <stdlib.h>

// 双向链表的节点结构
struct DNode {
    int data;
    struct DNode* prev;
    struct DNode* next;
};

// 创建新节点
struct DNode* createNode(int data) {
    struct DNode* newNode = (struct DNode*)malloc(sizeof(struct DNode));
    newNode->data = data;
    newNode->prev = NULL;
    newNode->next = NULL;
    return newNode;
}

// 在头部插入节点
struct DNode* insertAtHead(struct DNode* head, int data) {
    struct DNode* newNode = createNode(data);
    if (head != NULL) {
        head->prev = newNode;
        newNode->next = head;
    }
    return newNode;
}

// 遍历链表
void printList(struct DNode* head) {
    struct DNode* temp = head;
    printf("链表内容：\n");
    while (temp != NULL) {
        printf("%d -> ", temp->data);
        temp = temp->next;
    }
    printf("NULL\n");
}

// 主函数
int main() {
    struct DNode* head = NULL;

    head = insertAtHead(head, 10);
    head = insertAtHead(head, 20);
    head = insertAtHead(head, 30);

    printList(head);

    return 0;
}
```

## 循环链表

循环链表的特点是尾节点的 `next` 指针指向头节点，形成一个闭环。

```c
#include <stdio.h>
#include <stdlib.h>

// 定义循环链表的节点结构
struct CNode {
    int data;
    struct CNode* next;
};
```

### 创建新节点

```c
struct CNode* createNode(int data) {
    struct CNode* newNode = (struct CNode*)malloc(sizeof(struct CNode));
    if (newNode == NULL) {
        printf("内存分配失败\n");
        exit(1);
    }
    newNode->data = data;
    newNode->next = newNode; // 初始时指向自己
    return newNode;
}
```

### 插入操作

#### 在头部插入

```c
struct CNode* insertAtHead(struct CNode* tail, int data) {
    struct CNode* newNode = createNode(data);
    if (tail == NULL) {
        return newNode; // 如果链表为空，新节点即是尾节点
    }
    newNode->next = tail->next; // 新节点指向原头节点
    tail->next = newNode;       // 尾节点指向新节点
    return tail;
}
```

#### 在尾部插入

```c
struct CNode* insertAtTail(struct CNode* tail, int data) {
    struct CNode* newNode = createNode(data);
    if (tail == NULL) {
        return newNode; // 如果链表为空，新节点即是尾节点
    }
    newNode->next = tail->next; // 新节点指向头节点
    tail->next = newNode;       // 尾节点指向新节点
    return newNode;             // 新节点成为尾节点
}
```

### 遍历循环链表

```c
void printList(struct CNode* tail) {
    if (tail == NULL) return;
    struct CNode* temp = tail->next; // 从头节点开始
    do {
        printf("%d -> ", temp->data);
        temp = temp->next;
    } while (temp != tail->next); // 循环到头节点结束
    printf("(HEAD)\n");
}
```

### 完整的循环链表示例

```c
#include <stdio.h>
#include <stdlib.h>

// 定义循环链表的节点
struct CNode {
    int data;
    struct CNode* next;
};

// 创建节点
struct CNode* createNode(int data) {
    struct CNode* newNode = (struct CNode*)malloc(sizeof(struct CNode));
    newNode->data = data;
    newNode->next = newNode;
    return newNode;
}

// 在尾部插入节点
struct CNode* insertAtTail(struct CNode* tail, int data) {
    struct CNode* newNode = createNode(data);
    if (tail == NULL) {
        return newNode;
    }
    newNode->next = tail->next;
    tail->next = newNode;
    return newNode;
}

// 遍历链表
void printList(struct CNode* tail) {
    if (tail == NULL) return;
    struct CNode* temp = tail->next;
    do {
        printf("%d -> ", temp->data);
        temp = temp->next;
    } while (temp != tail->next);
    printf("(HEAD)\n");
}

// 主函数
int main() {
    struct CNode* tail = NULL;

    tail = insertAtTail(tail, 10);
    tail = insertAtTail(tail, 20);
    tail = insertAtTail(tail, 30);

    printList(tail);

    return 0;
}
```

## 总结

- **链表**：是一种线性数据结构，通过指针将多个节点串联起来。
- **双向链表**：支持双向遍历，插入和删除操作更灵活。
- **循环链表**：尾节点指向头节点，适合循环访问的场景。

链表是一种动态数据结构，灵活性高，适用于需要频繁插入和删除元素的场景。掌握链表的关键在于理解指针的使用，包括节点的创建、链接、遍历和释放内存。
