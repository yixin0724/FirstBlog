---
title: C语言基础
date: 2024-09-24 15:54:09
tags: 
  - C
  - 编程语言
cover: https://pic.imgdb.cn/item/66f271e0f21886ccc0a9ffbe.jpg
categories: 技术记录
description: C语言，伟大的编程语言！
---
# 1. C语言学习路线

C 语言学习不能只背语法，真正核心是理解：

1. 程序如何被 `预处理 → 编译 → 汇编 → 链接`。
2. 变量、数组、指针、结构体在内存中如何存放。
3. 指针如何保存地址、如何解引用、如何参与运算。
4. 字符串为什么是以 `\0` 结尾的字符数组。
5. `malloc()`、`calloc()`、`realloc()`、`free()` 如何管理堆内存。
6. 多文件工程中 `.h` 和 `.c` 如何分工。
7. 如何定位 `Segmentation fault`、内存泄漏、数组越界、野指针等问题。

推荐学习顺序：

| 阶段 | 学习重点 | 学习目标 |
|---|---|---|
| 入门阶段 | 基本语法、数据类型、输入输出、流程控制 | 能写简单程序 |
| 核心阶段 | 函数、数组、指针、字符串、结构体 | 理解 C 的核心表达方式 |
| 内存阶段 | 栈、堆、静态区、动态内存、野指针 | 能排查常见内存错误 |
| 工程阶段 | 头文件、多文件、Makefile、静态库、动态库 | 能组织真实 C 项目 |
| 提升阶段 | 数据结构、文件操作、调试工具、性能优化 | 能写稳定的底层程序 |

---

# 2. C语言概述

## 2.1 C语言是什么

`C` 是一种通用的、过程式的、编译型编程语言。它靠近硬件，又保留高级语言的结构化表达能力，所以经常用于：

- 操作系统内核
- 嵌入式开发
- 编译器
- 数据库底层
- 网络协议栈
- 高性能计算
- 驱动程序
- 系统工具
- 算法竞赛
- 语言运行时和虚拟机底层

## 2.2 C语言特点

### 1. 编译型语言

C 程序一般需要经过：

```text
源代码 .c
  ↓ 预处理
预处理文件 .i
  ↓ 编译
汇编文件 .s
  ↓ 汇编
目标文件 .o / .obj
  ↓ 链接
可执行文件 .exe / a.out
```

### 2. 靠近底层

C 可以直接操作地址、内存、位、字节和二进制数据，因此适合系统级开发。

### 3. 手动内存管理

C 没有 Java 那样的垃圾回收机制，需要程序员自己通过 `malloc()` 申请内存，通过 `free()` 释放内存。

### 4. 运行效率高

C 编译后通常直接生成本地机器码，运行时开销小。

### 5. 标准库较小

C 标准库比 Java 标准库小很多，很多工程能力需要自己封装或依赖操作系统 API / 第三方库。

## 2.3 常见 C 标准

| 标准 | 说明 |
|---|---|
| `C89 / C90` | 早期标准，很多经典教材基于它 |
| `C99` | 支持 `//` 注释、`for` 中定义变量、变长数组、`long long` 等 |
| `C11` | 增加原子操作、线程相关内容、匿名结构体等 |
| `C17 / C18` | 主要是修正版本 |
| `C23` | 当前新一代 C 标准，增加更多现代化语法和库支持 |

学习建议：

- 学校课程和算法刷题：重点掌握 `C99`。
- 工程和面试：重点掌握 `C11` 的基础能力，尤其是指针、内存、编译链接。
- 新标准了解：知道 `C23` 已经是当前新标准即可，不要把学习重点放在新语法上。

---

# 3. 开发环境与编译运行

## 3.1 常用编译器

| 编译器 | 常见平台 | 说明 |
|---|---|---|
| `GCC` | Linux / WSL / MinGW / MSYS2 | 最常见 |
| `Clang` | macOS / Linux / Windows | 错误提示友好 |
| `MSVC` | Windows | Visual Studio 默认 |
| `TCC` | Linux / Windows | 小型 C 编译器 |

## 3.2 Windows 推荐环境

推荐任选一种：

- `VSCode + MinGW / MSYS2`
- `CLion + MinGW`
- `Visual Studio`
- `WSL + GCC`

如果想和 Linux 开发环境接近，推荐 `WSL + GCC`。

## 3.3 Linux 安装

```bash
sudo apt update
sudo apt install gcc gdb make
```

编译运行：

```bash
gcc main.c -o main
./main
```

## 3.4 macOS 安装

```bash
xcode-select --install
```

编译运行：

```bash
clang main.c -o main
./main
```

## 3.5 常用编译命令

基础：

```bash
gcc main.c -o main
```

指定标准：

```bash
gcc -std=c99 main.c -o main
gcc -std=c11 main.c -o main
gcc -std=c17 main.c -o main
gcc -std=c23 main.c -o main
```

推荐学习阶段使用：

```bash
gcc -std=c11 -Wall -Wextra -Wpedantic -g -O0 main.c -o main
```

内存检查：

```bash
gcc -std=c11 -Wall -Wextra -g -O0 -fsanitize=address,undefined main.c -o main
```

常用参数：

| 参数 | 作用 |
|---|---|
| `-Wall` | 开启常见警告 |
| `-Wextra` | 开启更多警告 |
| `-Wpedantic` | 检查标准兼容问题 |
| `-g` | 生成调试信息 |
| `-O0` | 不优化，方便调试 |
| `-O2` | 常用优化等级 |
| `-fsanitize=address` | 检查内存越界、释放后使用等问题 |
| `-fsanitize=undefined` | 检查未定义行为 |

---

# 4. 第一个 C 程序

```c
#include <stdio.h>

int main(void) {
    printf("Hello, C!\n");
    return 0;
}
```

## 4.1 程序解释

```c
#include <stdio.h>
```

引入标准输入输出头文件，里面声明了 `printf()`、`scanf()` 等函数。

```c
int main(void)
```

程序入口函数。操作系统运行程序时，会从 `main()` 开始执行。

```c
printf("Hello, C!\n");
```

调用标准库函数输出字符串。

```c
return 0;
```

表示程序正常结束。通常 `0` 表示成功，非 `0` 表示异常退出。

## 4.2 `main` 函数写法

推荐：

```c
int main(void) {
    return 0;
}
```

带命令行参数：

```c
int main(int argc, char *argv[]) {
    return 0;
}
```

参数含义：

- `argc`：命令行参数个数。
- `argv`：命令行参数字符串数组。

示例：

```c
#include <stdio.h>

int main(int argc, char *argv[]) {
    for (int i = 0; i < argc; i++) {
        printf("argv[%d] = %s\n", i, argv[i]);
    }
    return 0;
}
```

运行：

```bash
./main hello world
```

输出：

```text
argv[0] = ./main
argv[1] = hello
argv[2] = world
```

---

# 5. 注释、标识符与关键字

## 5.1 注释

单行注释：

```c
// 这是单行注释
```

多行注释：

```c
/*
  这是多行注释
*/
```

## 5.2 标识符

标识符用于给变量、函数、数组、结构体等命名。

规则：

- 可以由字母、数字、下划线组成。
- 不能以数字开头。
- 区分大小写。
- 不能使用关键字。
- 不建议使用以下划线开头的名字，因为可能与系统或标准库保留标识符冲突。

推荐：

```c
int age;
double total_price;
char user_name[32];
```

不推荐：

```c
int 1age;      // 错误
int int;       // 错误
int _Value;    // 不建议
```

## 5.3 常见关键字

| 类别 | 关键字 |
|---|---|
| 数据类型 | `char`、`short`、`int`、`long`、`float`、`double`、`void` |
| 类型修饰 | `signed`、`unsigned`、`const`、`volatile` |
| 流程控制 | `if`、`else`、`switch`、`case`、`default`、`for`、`while`、`do`、`break`、`continue`、`return` |
| 存储类别 | `auto`、`register`、`static`、`extern` |
| 自定义类型 | `struct`、`union`、`enum`、`typedef` |
| 其他 | `sizeof`、`goto` |

---

# 6. 数据类型

## 6.1 类型体系

```text
基本类型
  ├── 整型
  ├── 浮点型
  └── 字符型

构造类型
  ├── 数组
  ├── 结构体
  ├── 联合体
  └── 枚举

指针类型

空类型 void
```

## 6.2 整型

| 类型 | 常见大小 | 说明 |
|---|---:|---|
| `char` | 1 字节 | 字符，也可以当小整数 |
| `short` | 2 字节 | 短整型 |
| `int` | 4 字节 | 默认整数类型 |
| `long` | 4 或 8 字节 | 长整型 |
| `long long` | 8 字节 | 更长整型 |

查看大小：

```c
#include <stdio.h>

int main(void) {
    printf("sizeof(char) = %zu\n", sizeof(char));
    printf("sizeof(short) = %zu\n", sizeof(short));
    printf("sizeof(int) = %zu\n", sizeof(int));
    printf("sizeof(long) = %zu\n", sizeof(long));
    printf("sizeof(long long) = %zu\n", sizeof(long long));
    return 0;
}
```

`%zu` 用于输出 `sizeof` 返回的 `size_t` 类型。

## 6.3 有符号与无符号

```c
signed int a = -10;
unsigned int b = 10;
```

简写：

```c
signed a = -10;
unsigned b = 10;
```

注意：

```c
unsigned int x = 0;
x = x - 1;
printf("%u\n", x);
```

不会得到 `-1`，而是发生无符号整数回绕，得到一个很大的数。

## 6.4 固定宽度整数

头文件：

```c
#include <stdint.h>
```

常用类型：

| 类型 | 说明 |
|---|---|
| `int8_t` | 有符号 8 位整数 |
| `uint8_t` | 无符号 8 位整数 |
| `int16_t` | 有符号 16 位整数 |
| `uint16_t` | 无符号 16 位整数 |
| `int32_t` | 有符号 32 位整数 |
| `uint32_t` | 无符号 32 位整数 |
| `int64_t` | 有符号 64 位整数 |
| `uint64_t` | 无符号 64 位整数 |

示例：

```c
#include <stdint.h>
#include <stdio.h>

int main(void) {
    int32_t age = 18;
    uint64_t count = 10000000000ULL;

    printf("age = %d\n", age);
    printf("count = %llu\n", (unsigned long long)count);
    return 0;
}
```

网络协议、二进制文件、嵌入式开发中推荐使用固定宽度类型。

## 6.5 浮点型

| 类型 | 常见大小 | 说明 |
|---|---:|---|
| `float` | 4 字节 | 单精度 |
| `double` | 8 字节 | 双精度 |
| `long double` | 平台相关 | 扩展精度 |

示例：

```c
float f = 3.14f;
double d = 3.1415926;
long double ld = 3.141592653589793L;
```

注意：浮点数不能精确表示很多十进制小数，因此不适合直接做金额精确计算。

## 6.6 字符类型

```c
char ch = 'A';
printf("%c\n", ch);
printf("%d\n", ch);
```

在 ASCII 编码中：

```text
'A' = 65
'a' = 97
'0' = 48
```

字符转数字：

```c
char ch = '7';
int num = ch - '0';
```

## 6.7 布尔类型

`C99` 引入：

```c
#include <stdbool.h>
```

用法：

```c
#include <stdbool.h>
#include <stdio.h>

int main(void) {
    bool flag = true;

    if (flag) {
        printf("true\n");
    }

    return 0;
}
```

## 6.8 `void`

`void` 表示“无类型”。

函数无返回值：

```c
void print_hello(void) {
    printf("hello\n");
}
```

函数无参数：

```c
int main(void) {
    return 0;
}
```

通用指针：

```c
void *ptr;
```

`void *` 可以接收任意对象指针，但使用前通常需要转换为具体类型。

```c
int a = 10;
void *p = &a;

printf("%d\n", *(int *)p);
```

---

# 7. 变量、常量与作用域

## 7.1 变量定义

```c
int age = 18;
double score = 99.5;
char grade = 'A';
```

C 语言变量在使用前必须先定义。

## 7.2 常量

字面量常量：

```c
10
3.14
'A'
"hello"
```

`const` 常量：

```c
const int MAX_SIZE = 100;
```

宏常量：

```c
#define MAX_SIZE 100
```

区别：

| 方式 | 特点 |
|---|---|
| `const` | 有类型、有作用域，编译器能检查 |
| `#define` | 预处理阶段文本替换，无类型检查 |

推荐优先使用 `const`，需要条件编译或文本替换时使用宏。

## 7.3 局部变量

```c
void func(void) {
    int x = 10;
}
```

特点：

- 只在当前代码块内有效。
- 通常存放在栈上。
- 未初始化时值不确定。

## 7.4 全局变量

```c
int global_count = 0;
```

特点：

- 定义在函数外。
- 程序启动时初始化。
- 未显式初始化时默认为 `0`。
- 过度使用会导致耦合增加。

## 7.5 静态变量

局部静态变量：

```c
void counter(void) {
    static int count = 0;
    count++;
    printf("%d\n", count);
}
```

特点：

- 作用域仍在函数内部。
- 生命周期贯穿整个程序运行期间。
- 只初始化一次。

## 7.6 存储类别

| 关键字 | 说明 |
|---|---|
| `auto` | 自动变量，局部变量默认就是它 |
| `register` | 建议编译器放寄存器，现代很少显式写 |
| `static` | 静态存储期，或限制文件可见性 |
| `extern` | 声明外部变量或函数 |
| `typedef` | 给类型起别名 |

---

# 8. 输入输出

## 8.1 `printf`

```c
#include <stdio.h>

int main(void) {
    int age = 18;
    double score = 95.5;
    char grade = 'A';

    printf("age = %d\n", age);
    printf("score = %.2f\n", score);
    printf("grade = %c\n", grade);
    return 0;
}
```

常见格式控制符：

| 格式 | 类型 |
|---|---|
| `%d` | `int` |
| `%u` | `unsigned int` |
| `%ld` | `long` |
| `%lld` | `long long` |
| `%f` | 输出 `float` / `double` |
| `%lf` | `scanf` 读取 `double` |
| `%c` | 字符 |
| `%s` | 字符串 |
| `%p` | 指针地址 |
| `%zu` | `size_t` |
| `%%` | 输出 `%` |

## 8.2 `scanf`

```c
int age;
scanf("%d", &age);
printf("age = %d\n", age);
```

`scanf()` 需要传入变量地址，所以要用 `&`。

错误：

```c
int age;
scanf("%d", age);
```

正确：

```c
scanf("%d", &age);
```

## 8.3 读取字符串

```c
char name[32];
scanf("%31s", name);
```

注意：

- 数组名 `name` 本身可以退化为首元素地址，所以不用写 `&name`。
- `%31s` 表示最多读取 31 个字符，预留 1 个位置给 `\0`。
- 不限制宽度的 `%s` 可能导致缓冲区溢出。

## 8.4 `fgets`

推荐用 `fgets()` 读取一整行：

```c
char line[128];

if (fgets(line, sizeof(line), stdin) != NULL) {
    printf("%s", line);
}
```

去掉换行符：

```c
#include <string.h>

line[strcspn(line, "\n")] = '\0';
```

---

# 9. 运算符

## 9.1 算术运算符

| 运算符 | 作用 |
|---|---|
| `+` | 加 |
| `-` | 减 |
| `*` | 乘 |
| `/` | 除 |
| `%` | 取模 |

整数除法：

```c
printf("%d\n", 5 / 2);  // 2
```

浮点除法：

```c
printf("%f\n", 5.0 / 2);  // 2.500000
```

## 9.2 自增自减

```c
int a = 10;

a++;
++a;
a--;
--a;
```

不推荐写复杂表达式：

```c
int i = 1;
int x = i++ + ++i;  // 不推荐
```

## 9.3 关系与逻辑运算符

关系运算符：

```c
>   <   >=   <=   ==   !=
```

逻辑运算符：

```c
&&   ||   !
```

短路示例：

```c
if (p != NULL && *p == 10) {
    printf("ok\n");
}
```

## 9.4 位运算符

| 运算符 | 作用 |
|---|---|
| `&` | 按位与 |
| `|` | 按位或 |
| `^` | 按位异或 |
| `~` | 按位取反 |
| `<<` | 左移 |
| `>>` | 右移 |

判断奇偶：

```c
if ((n & 1) == 0) {
    printf("even\n");
} else {
    printf("odd\n");
}
```

设置某一位：

```c
x = x | (1 << k);
```

清除某一位：

```c
x = x & ~(1 << k);
```

判断某一位是否为 1：

```c
if ((x & (1 << k)) != 0) {
    printf("bit is 1\n");
}
```

## 9.5 `sizeof`

```c
printf("%zu\n", sizeof(int));
```

数组长度：

```c
int arr[10];
int len = sizeof(arr) / sizeof(arr[0]);
```

注意：数组作为函数参数时会退化为指针，此时 `sizeof(arr)` 得到的是指针大小。

---

# 10. 流程控制

## 10.1 `if`

```c
if (score >= 90) {
    printf("A\n");
} else if (score >= 80) {
    printf("B\n");
} else {
    printf("C\n");
}
```

## 10.2 `switch`

```c
switch (choice) {
    case 1:
        printf("add\n");
        break;
    case 2:
        printf("delete\n");
        break;
    default:
        printf("unknown\n");
        break;
}
```

注意：

- `case` 后一般要写 `break`。
- 不写 `break` 会继续执行下一个 `case`，称为“贯穿”。

## 10.3 循环

`for`：

```c
for (int i = 0; i < 10; i++) {
    printf("%d\n", i);
}
```

`while`：

```c
int i = 0;
while (i < 10) {
    printf("%d\n", i);
    i++;
}
```

`do while`：

```c
int i = 0;
do {
    printf("%d\n", i);
    i++;
} while (i < 10);
```

## 10.4 `break` 与 `continue`

`break` 结束当前循环：

```c
for (int i = 0; i < 10; i++) {
    if (i == 5) {
        break;
    }
    printf("%d\n", i);
}
```

`continue` 跳过本次循环：

```c
for (int i = 0; i < 10; i++) {
    if (i % 2 == 0) {
        continue;
    }
    printf("%d\n", i);
}
```

## 10.5 `goto`

不建议滥用，但可以用于统一资源释放：

```c
int func(void) {
    char *buffer = malloc(1024);
    if (buffer == NULL) {
        goto error;
    }

    // 业务逻辑

    free(buffer);
    return 0;

error:
    free(buffer);
    return -1;
}
```

---

# 11. 函数

## 11.1 函数定义

```c
int add(int a, int b) {
    return a + b;
}
```

格式：

```text
返回值类型 函数名(参数列表) {
    函数体
}
```

## 11.2 函数声明

如果函数定义在调用之后，需要先声明：

```c
#include <stdio.h>

int add(int a, int b);

int main(void) {
    printf("%d\n", add(1, 2));
    return 0;
}

int add(int a, int b) {
    return a + b;
}
```

## 11.3 值传递

C 函数参数默认是值传递。

```c
void change(int x) {
    x = 100;
}

int main(void) {
    int a = 10;
    change(a);
    printf("%d\n", a);  // 10
    return 0;
}
```

要修改外部变量，需要传地址：

```c
void change(int *p) {
    *p = 100;
}

int main(void) {
    int a = 10;
    change(&a);
    printf("%d\n", a);  // 100
    return 0;
}
```

## 11.4 不要返回局部变量地址

错误：

```c
int *bad(void) {
    int x = 10;
    return &x;
}
```

函数结束后，局部变量 `x` 的栈空间失效，返回地址成为悬空指针。

## 11.5 递归

```c
int factorial(int n) {
    if (n <= 1) {
        return 1;
    }
    return n * factorial(n - 1);
}
```

递归三要素：

1. 函数含义明确。
2. 有终止条件。
3. 每次递归都向终止条件靠近。

## 11.6 函数指针

```c
#include <stdio.h>

int add(int a, int b) {
    return a + b;
}

int main(void) {
    int (*fp)(int, int) = add;
    printf("%d\n", fp(1, 2));
    return 0;
}
```

常见用途：

- 回调函数
- 策略选择
- 表驱动编程
- 简单多态模拟

回调示例：

```c
int add(int a, int b) { return a + b; }
int sub(int a, int b) { return a - b; }

int calculate(int a, int b, int (*op)(int, int)) {
    return op(a, b);
}
```

---

# 12. 数组

## 12.1 一维数组

```c
int arr[5] = {1, 2, 3, 4, 5};
```

未完全初始化：

```c
int arr[5] = {1, 2};  // 后面自动补 0
```

全部初始化为 0：

```c
int arr[5] = {0};
```

自动推断长度：

```c
int arr[] = {1, 2, 3};
```

## 12.2 遍历数组

```c
int arr[] = {1, 2, 3, 4, 5};
int n = sizeof(arr) / sizeof(arr[0]);

for (int i = 0; i < n; i++) {
    printf("%d\n", arr[i]);
}
```

## 12.3 数组越界

错误：

```c
int arr[3] = {1, 2, 3};
printf("%d\n", arr[3]);
```

C 不会自动检查数组越界，越界访问是未定义行为。

## 12.4 二维数组

```c
int matrix[2][3] = {
    {1, 2, 3},
    {4, 5, 6}
};
```

遍历：

```c
for (int i = 0; i < 2; i++) {
    for (int j = 0; j < 3; j++) {
        printf("%d ", matrix[i][j]);
    }
    printf("\n");
}
```

二维数组按行连续存储。

## 12.5 数组作为函数参数

```c
void print_array(int arr[], int n) {
    for (int i = 0; i < n; i++) {
        printf("%d\n", arr[i]);
    }
}
```

等价：

```c
void print_array(int *arr, int n) {
    // ...
}
```

数组传参时会退化为指针，所以必须额外传长度。

---

# 13. 指针

## 13.1 指针是什么

指针就是保存地址的变量。

```c
int a = 10;
int *p = &a;
```

解释：

- `a` 是一个 `int` 变量。
- `&a` 表示 `a` 的地址。
- `p` 是 `int *` 类型指针，保存 `a` 的地址。
- `*p` 表示访问 `p` 指向的值。

```c
printf("%d\n", a);
printf("%p\n", (void *)&a);
printf("%p\n", (void *)p);
printf("%d\n", *p);
```

## 13.2 取地址与解引用

| 符号 | 作用 |
|---|---|
| `&` | 取地址 |
| `*` | 定义指针或解引用 |

```c
int a = 10;
int *p = &a;

*p = 20;
printf("%d\n", a);  // 20
```

## 13.3 指针类型的意义

```c
int *p;
char *q;
double *r;
```

指针类型决定：

1. 解引用时读取多少字节。
2. 指针加减时移动多少字节。

```c
int arr[] = {10, 20, 30};
int *p = arr;

printf("%d\n", *p);       // 10
printf("%d\n", *(p + 1)); // 20
```

`p + 1` 不是地址数值加 1，而是移动 `sizeof(int)` 个字节。

## 13.4 空指针

```c
int *p = NULL;
```

使用前检查：

```c
if (p != NULL) {
    printf("%d\n", *p);
}
```

不要解引用空指针：

```c
int *p = NULL;
printf("%d\n", *p);  // 错误
```

## 13.5 野指针

野指针指向未知或已经失效的内存。

来源：

```c
int *p;
*p = 10;  // p 未初始化
```

释放后继续使用：

```c
int *p = malloc(sizeof(int));
free(p);
*p = 10;  // 错误
```

避免：

```c
free(p);
p = NULL;
```

## 13.6 二级指针

```c
int a = 10;
int *p = &a;
int **pp = &p;

printf("%d\n", **pp);
```

常见用途：

- 修改函数外部的指针变量。
- 动态二维数组。
- 字符串数组。
- `main(int argc, char *argv[])`。

函数中分配内存：

```c
#include <stdio.h>
#include <stdlib.h>

int create_int(int **pp) {
    *pp = malloc(sizeof(int));
    if (*pp == NULL) {
        return -1;
    }
    **pp = 100;
    return 0;
}

int main(void) {
    int *p = NULL;

    if (create_int(&p) == 0) {
        printf("%d\n", *p);
        free(p);
    }

    return 0;
}
```

## 13.7 `const` 与指针

指向常量的指针：

```c
const int *p = &a;
```

不能通过 `p` 改值，但 `p` 可以改指向。

指针常量：

```c
int *const p = &a;
```

`p` 不能改指向，但可以通过 `p` 改值。

指向常量的指针常量：

```c
const int *const p = &a;
```

指向和值都不能通过 `p` 改。

## 13.8 `void *`

```c
void *p;
int a = 10;

p = &a;
printf("%d\n", *(int *)p);
```

典型用途：

- `malloc()` 返回 `void *`
- 泛型容器
- 回调函数参数

---

# 14. 指针与数组

## 14.1 数组名退化为指针

```c
int arr[] = {1, 2, 3};

printf("%p\n", (void *)arr);
printf("%p\n", (void *)&arr[0]);
```

多数表达式中，数组名会退化为首元素指针。

不会退化的常见情况：

```c
sizeof(arr)
&arr
```

## 14.2 `arr` 与 `&arr`

```c
int arr[5];

arr + 1;   // 移动 sizeof(int)
&arr + 1;  // 移动 sizeof(int[5])
```

地址值可能一样，但类型不同：

- `arr` 退化为 `int *`
- `&arr` 类型是 `int (*)[5]`

## 14.3 指针访问数组

```c
int arr[] = {10, 20, 30};
int *p = arr;

printf("%d\n", arr[0]);
printf("%d\n", *(arr + 0));
printf("%d\n", p[0]);
printf("%d\n", *(p + 0));
```

四种写法等价。

## 14.4 指针数组

数组中的每个元素都是指针：

```c
char *names[] = {"Tom", "Jerry", "Alice"};
```

遍历：

```c
for (int i = 0; i < 3; i++) {
    printf("%s\n", names[i]);
}
```

## 14.5 数组指针

指向数组的指针：

```c
int arr[3] = {1, 2, 3};
int (*p)[3] = &arr;

printf("%d\n", (*p)[0]);
```

---

# 15. 字符串

## 15.1 C字符串本质

C 字符串本质是以 `\0` 结尾的字符数组。

```c
char str[] = "hello";
```

实际存储：

```text
'h' 'e' 'l' 'l' 'o' '\0'
```

## 15.2 字符数组与字符串字面量

```c
char s1[] = "hello";
char *s2 = "hello";
```

区别：

```c
s1[0] = 'H';  // 可以
s2[0] = 'H';  // 错误
```

推荐：

```c
const char *s = "hello";
```

## 15.3 常用字符串函数

头文件：

```c
#include <string.h>
```

| 函数 | 作用 |
|---|---|
| `strlen()` | 获取字符串长度，不含 `\0` |
| `strcpy()` | 字符串复制 |
| `strncpy()` | 限长复制，但有细节风险 |
| `strcat()` | 字符串拼接 |
| `strcmp()` | 字符串比较 |
| `strncmp()` | 限长比较 |
| `strchr()` | 查找字符 |
| `strstr()` | 查找子串 |
| `memcpy()` | 内存复制 |
| `memmove()` | 可处理重叠区域的内存移动 |
| `memset()` | 设置内存 |
| `memcmp()` | 内存比较 |

## 15.4 `strlen` 与 `sizeof`

```c
char str[] = "hello";

printf("%zu\n", strlen(str)); // 5
printf("%zu\n", sizeof(str)); // 6
```

- `strlen()` 统计 `\0` 之前的字符数量。
- `sizeof` 统计整个数组占用字节数。

## 15.5 字符串比较

错误：

```c
if (s1 == s2) {
    // 比较的是地址
}
```

正确：

```c
if (strcmp(s1, s2) == 0) {
    printf("equal\n");
}
```

## 15.6 字符串输入安全

危险：

```c
char name[16];
scanf("%s", name);
```

更好：

```c
scanf("%15s", name);
```

更推荐：

```c
fgets(name, sizeof(name), stdin);
name[strcspn(name, "\n")] = '\0';
```

---

# 16. 结构体、联合体、枚举

## 16.1 结构体

```c
struct Student {
    int id;
    char name[32];
    double score;
};
```

定义变量：

```c
struct Student stu = {1, "Tom", 98.5};
```

访问成员：

```c
printf("%d\n", stu.id);
printf("%s\n", stu.name);
printf("%.2f\n", stu.score);
```

## 16.2 结构体指针

```c
struct Student *p = &stu;

printf("%d\n", (*p).id);
printf("%d\n", p->id);
```

`p->id` 等价于 `(*p).id`。

## 16.3 `typedef` 简化结构体

```c
typedef struct Student {
    int id;
    char name[32];
    double score;
} Student;
```

使用：

```c
Student stu = {1, "Tom", 98.5};
```

## 16.4 结构体作为函数参数

值传递：

```c
void print_student(Student stu) {
    printf("%d %s %.2f\n", stu.id, stu.name, stu.score);
}
```

指针传递：

```c
void print_student(const Student *stu) {
    printf("%d %s %.2f\n", stu->id, stu->name, stu->score);
}
```

推荐使用指针传递，避免结构体复制开销。

## 16.5 结构体内存对齐

```c
struct A {
    char c;
    int i;
};
```

虽然 `char` 1 字节，`int` 4 字节，但 `sizeof(struct A)` 可能是 8，因为存在内存对齐。

优化成员顺序：

```c
struct Bad {
    char a;
    int b;
    char c;
};

struct Good {
    int b;
    char a;
    char c;
};
```

## 16.6 联合体 `union`

```c
union Data {
    int i;
    float f;
    char str[20];
};
```

特点：

- 所有成员共享同一片空间。
- `sizeof(union)` 等于最大成员大小再考虑对齐。
- 同一时刻通常只使用一个成员。

## 16.7 枚举 `enum`

```c
enum Color {
    RED,
    GREEN,
    BLUE
};
```

默认：

```text
RED = 0
GREEN = 1
BLUE = 2
```

指定值：

```c
enum Status {
    OK = 200,
    NOT_FOUND = 404,
    ERROR = 500
};
```

配合 `typedef`：

```c
typedef enum {
    STATE_IDLE,
    STATE_RUNNING,
    STATE_STOPPED
} State;
```

---

# 17. 动态内存管理

## 17.1 为什么需要动态内存

数组长度固定：

```c
int arr[100];
```

运行时决定大小：

```c
int *arr = malloc(n * sizeof(int));
```

## 17.2 `malloc`

```c
#include <stdlib.h>

int *p = malloc(sizeof(int));
if (p == NULL) {
    return 1;
}

*p = 10;
printf("%d\n", *p);

free(p);
p = NULL;
```

## 17.3 `calloc`

```c
int *arr = calloc(n, sizeof(int));
```

特点：

- 分配 `n * sizeof(int)` 字节。
- 自动初始化为 0。

## 17.4 `realloc`

```c
int *new_arr = realloc(arr, new_size * sizeof(int));
if (new_arr == NULL) {
    // 原 arr 仍然有效
} else {
    arr = new_arr;
}
```

不要直接写：

```c
arr = realloc(arr, new_size);
```

失败时会丢失原地址，造成内存泄漏。

## 17.5 `free`

```c
free(p);
p = NULL;
```

注意：

- `free(NULL)` 是安全的。
- 同一块内存不能 `free` 两次。
- `free` 后不要继续使用原指针。

## 17.6 动态数组示例

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int n;
    scanf("%d", &n);

    int *arr = malloc(n * sizeof(*arr));
    if (arr == NULL) {
        return 1;
    }

    for (int i = 0; i < n; i++) {
        arr[i] = i + 1;
    }

    for (int i = 0; i < n; i++) {
        printf("%d ", arr[i]);
    }

    free(arr);
    arr = NULL;
    return 0;
}
```

## 17.7 常见动态内存错误

| 错误 | 示例 |
|---|---|
| 内存泄漏 | `malloc()` 后忘记 `free()` |
| 重复释放 | `free(p); free(p);` |
| 释放后使用 | `free(p); *p = 1;` |
| 越界写 | `arr[n] = 1;` |
| 分配大小错误 | `malloc(n)` 而不是 `malloc(n * sizeof(*arr))` |

---

# 18. 预处理器与宏

## 18.1 `#include`

```c
#include <stdio.h>
#include "student.h"
```

区别：

| 写法 | 说明 |
|---|---|
| `<stdio.h>` | 搜索系统头文件目录 |
| `"student.h"` | 优先搜索当前项目目录 |

## 18.2 宏定义

```c
#define PI 3.1415926
#define MAX_SIZE 100
```

宏是预处理阶段的文本替换。

## 18.3 带参数宏

```c
#define SQUARE(x) ((x) * (x))
```

必须加括号，避免优先级问题。

错误：

```c
#define SQUARE(x) x * x
```

调用：

```c
SQUARE(1 + 2)
```

会变成：

```c
1 + 2 * 1 + 2
```

## 18.4 宏副作用

```c
#define SQUARE(x) ((x) * (x))

int i = 2;
int y = SQUARE(i++);  // i++ 执行两次
```

复杂逻辑优先使用函数或 `static inline`。

## 18.5 条件编译

```c
#ifdef DEBUG
printf("debug mode\n");
#endif
```

```c
#ifndef HEADER_H
#define HEADER_H

// 头文件内容

#endif
```

## 18.6 头文件保护

```c
#ifndef STUDENT_H
#define STUDENT_H

typedef struct {
    int id;
    char name[32];
} Student;

#endif
```

现代编译器也普遍支持：

```c
#pragma once
```

---

# 19. 多文件编程与工程组织

## 19.1 推荐目录结构

```text
project/
  ├── include/
  │   └── student.h
  ├── src/
  │   ├── main.c
  │   └── student.c
  └── Makefile
```

## 19.2 头文件 `.h`

`student.h`

```c
#ifndef STUDENT_H
#define STUDENT_H

typedef struct {
    int id;
    char name[32];
    double score;
} Student;

void print_student(const Student *student);

#endif
```

头文件通常放：

- 类型定义
- 函数声明
- 宏定义
- 常量声明
- 外部变量声明

## 19.3 源文件 `.c`

`student.c`

```c
#include <stdio.h>
#include "student.h"

void print_student(const Student *student) {
    printf("%d %s %.2f\n", student->id, student->name, student->score);
}
```

`main.c`

```c
#include "student.h"

int main(void) {
    Student stu = {1, "Tom", 98.5};
    print_student(&stu);
    return 0;
}
```

编译：

```bash
gcc src/main.c src/student.c -Iinclude -o app
```

## 19.4 `extern`

`config.c`

```c
int global_count = 0;
```

`config.h`

```c
#ifndef CONFIG_H
#define CONFIG_H

extern int global_count;

#endif
```

头文件中不要直接定义全局变量，否则多个 `.c` 文件包含时会重复定义。

## 19.5 `static` 限制文件作用域

```c
static int helper(void) {
    return 1;
}
```

被 `static` 修饰的函数只能在当前 `.c` 文件中使用。

## 19.6 Makefile 简例

```makefile
CC = gcc
CFLAGS = -std=c11 -Wall -Wextra -g -Iinclude

app: src/main.o src/student.o
	$(CC) src/main.o src/student.o -o app

src/main.o: src/main.c include/student.h
	$(CC) $(CFLAGS) -c src/main.c -o src/main.o

src/student.o: src/student.c include/student.h
	$(CC) $(CFLAGS) -c src/student.c -o src/student.o

clean:
	rm -f src/*.o app
```

运行：

```bash
make
make clean
```

---

# 20. 文件操作

## 20.1 文件指针

```c
FILE *fp;
```

头文件：

```c
#include <stdio.h>
```

## 20.2 打开文件

```c
FILE *fp = fopen("data.txt", "r");
if (fp == NULL) {
    perror("fopen");
    return 1;
}
```

常见模式：

| 模式 | 说明 |
|---|---|
| `"r"` | 只读，文件必须存在 |
| `"w"` | 只写，文件不存在则创建，存在则清空 |
| `"a"` | 追加写 |
| `"r+"` | 读写，文件必须存在 |
| `"w+"` | 读写，清空或创建 |
| `"a+"` | 读和追加 |
| `"rb"` | 二进制读 |
| `"wb"` | 二进制写 |

## 20.3 关闭文件

```c
fclose(fp);
```

打开文件后必须关闭。

## 20.4 字符读写

```c
int ch = fgetc(fp);
fputc('A', fp);
```

## 20.5 行读写

```c
char line[128];

while (fgets(line, sizeof(line), fp) != NULL) {
    printf("%s", line);
}
```

写入：

```c
fputs("hello\n", fp);
```

## 20.6 格式化读写

```c
fprintf(fp, "%d %s %.2f\n", id, name, score);
fscanf(fp, "%d %31s %lf", &id, name, &score);
```

## 20.7 二进制读写

```c
fwrite(&stu, sizeof(stu), 1, fp);
fread(&stu, sizeof(stu), 1, fp);
```

注意：结构体二进制写入会受到对齐、大小端、平台差异影响，不适合跨平台持久化协议。

---

# 21. 常见数据结构 C 实现思路

## 21.1 顺序表

```c
typedef struct {
    int *data;
    int size;
    int capacity;
} Vector;
```

初始化：

```c
int vector_init(Vector *v, int capacity) {
    v->data = malloc(capacity * sizeof(int));
    if (v->data == NULL) {
        return -1;
    }
    v->size = 0;
    v->capacity = capacity;
    return 0;
}
```

销毁：

```c
void vector_destroy(Vector *v) {
    free(v->data);
    v->data = NULL;
    v->size = 0;
    v->capacity = 0;
}
```

## 21.2 单链表

```c
typedef struct Node {
    int data;
    struct Node *next;
} Node;
```

创建节点：

```c
Node *create_node(int data) {
    Node *node = malloc(sizeof(Node));
    if (node == NULL) {
        return NULL;
    }
    node->data = data;
    node->next = NULL;
    return node;
}
```

头插法：

```c
void push_front(Node **head, int data) {
    Node *node = create_node(data);
    if (node == NULL) {
        return;
    }
    node->next = *head;
    *head = node;
}
```

释放链表：

```c
void free_list(Node *head) {
    while (head != NULL) {
        Node *next = head->next;
        free(head);
        head = next;
    }
}
```

## 21.3 栈

核心操作：

- `push`
- `pop`
- `peek`
- `is_empty`
- `is_full`

顺序栈结构：

```c
typedef struct {
    int *data;
    int top;
    int capacity;
} Stack;
```

## 21.4 队列

循环队列结构：

```c
typedef struct {
    int *data;
    int front;
    int rear;
    int capacity;
} Queue;
```

判断：

```c
// 队空
front == rear

// 队满，浪费一个空间
(rear + 1) % capacity == front
```

---

# 22. C语言内存模型

## 22.1 常见内存区域

```text
高地址
┌──────────────┐
│ 栈 stack      │ 局部变量、函数调用信息
├──────────────┤
│ 堆 heap       │ malloc/calloc/realloc 动态分配
├──────────────┤
│ BSS段         │ 未初始化的全局/静态变量
├──────────────┤
│ 数据段 data   │ 已初始化的全局/静态变量
├──────────────┤
│ 代码段 text   │ 程序指令、只读常量
└──────────────┘
低地址
```

## 22.2 栈

特点：

- 自动分配和释放。
- 存放局部变量、函数参数、返回地址等。
- 空间通常较小。
- 函数结束后局部变量失效。

## 22.3 堆

特点：

- 由程序员手动申请和释放。
- 空间较大。
- 生命周期由 `malloc` 到 `free` 控制。
- 容易出现内存泄漏、野指针、重复释放等问题。

## 22.4 数据段和 BSS 段

已初始化的全局或静态变量在数据段：

```c
int global_value = 10;
static int count = 1;
```

未初始化或初始化为 0 的全局或静态变量在 BSS 段：

```c
int global_count;
static int value;
```

## 22.5 代码段

存放程序机器指令，通常只读。字符串字面量通常也位于只读区域：

```c
const char *s = "hello";
```

---

# 23. 编译、链接与程序执行过程

## 23.1 预处理

处理：

- `#include`
- `#define`
- 条件编译
- 删除注释

命令：

```bash
gcc -E main.c -o main.i
```

## 23.2 编译

将预处理结果编译为汇编代码：

```bash
gcc -S main.i -o main.s
```

## 23.3 汇编

将汇编代码转为目标文件：

```bash
gcc -c main.s -o main.o
```

或者：

```bash
gcc -c main.c -o main.o
```

## 23.4 链接

将目标文件和库文件链接为可执行文件：

```bash
gcc main.o -o main
```

多个文件：

```bash
gcc main.o student.o -o app
```

## 23.5 静态库

创建静态库：

```bash
gcc -c math_utils.c -o math_utils.o
ar rcs libmathutils.a math_utils.o
```

使用：

```bash
gcc main.c -L. -lmathutils -o app
```

## 23.6 动态库

创建动态库：

```bash
gcc -fPIC -shared math_utils.c -o libmathutils.so
```

使用：

```bash
gcc main.c -L. -lmathutils -o app
```

运行时可能需要配置：

```bash
export LD_LIBRARY_PATH=.:$LD_LIBRARY_PATH
```

## 23.7 常见链接错误

### 1. `undefined reference`

原因：

- 函数只声明没有定义。
- 定义所在 `.c` 文件没有参与编译链接。
- 库链接顺序错误。

### 2. `multiple definition`

原因：

- 头文件中定义了全局变量。
- 多个 `.c` 文件定义了同名函数或变量。

解决：

- 头文件中只写 `extern` 声明。
- 真正定义放在一个 `.c` 文件中。

---

# 24. 调试与错误排查

## 24.1 `printf` 调试

```c
printf("x = %d\n", x);
```

优点简单直接，缺点是复杂问题效率低。

## 24.2 `gdb`

编译：

```bash
gcc -g -O0 main.c -o main
```

启动：

```bash
gdb ./main
```

常用命令：

| 命令 | 作用 |
|---|---|
| `break main` | 在 `main` 处打断点 |
| `run` | 运行 |
| `next` | 单步，不进入函数 |
| `step` | 单步，进入函数 |
| `continue` | 继续运行 |
| `print x` | 打印变量 |
| `backtrace` | 查看调用栈 |
| `quit` | 退出 |

## 24.3 AddressSanitizer

```bash
gcc -g -O0 -fsanitize=address main.c -o main
```

可以检测：

- 数组越界
- 堆越界
- 释放后使用
- 重复释放
- 部分内存泄漏

## 24.4 Valgrind

```bash
valgrind --leak-check=full ./main
```

可以检查：

- 内存泄漏
- 非法读写
- 未初始化内存读取
- 重复释放

## 24.5 段错误常见原因

- 解引用空指针。
- 数组越界。
- 使用野指针。
- 修改字符串字面量。
- 栈溢出。
- 释放后继续使用指针。

---

# 25. C标准库常用头文件

| 头文件 | 作用 |
|---|---|
| `<stdio.h>` | 输入输出 |
| `<stdlib.h>` | 内存管理、随机数、程序退出 |
| `<string.h>` | 字符串和内存操作 |
| `<ctype.h>` | 字符判断和转换 |
| `<math.h>` | 数学函数 |
| `<time.h>` | 时间日期 |
| `<stdbool.h>` | 布尔类型 |
| `<stdint.h>` | 固定宽度整数 |
| `<stddef.h>` | `size_t`、`NULL`、`offsetof` |
| `<errno.h>` | 错误码 |
| `<assert.h>` | 断言 |
| `<limits.h>` | 整数范围 |
| `<float.h>` | 浮点范围 |
| `<stdarg.h>` | 可变参数 |
| `<signal.h>` | 信号处理 |

## 25.1 `<stdlib.h>`

常用函数：

```c
malloc()
calloc()
realloc()
free()
exit()
atoi()
strtol()
rand()
srand()
qsort()
bsearch()
```

## 25.2 `<ctype.h>`

```c
isalpha()
isdigit()
isalnum()
isspace()
islower()
isupper()
tolower()
toupper()
```

示例：

```c
if (isdigit(ch)) {
    int num = ch - '0';
}
```

## 25.3 `<math.h>`

```c
sqrt()
pow()
sin()
cos()
tan()
fabs()
ceil()
floor()
round()
```

编译时可能需要链接数学库：

```bash
gcc main.c -lm -o main
```

---

# 26. C语言安全编程规范

## 26.1 永远检查返回值

```c
FILE *fp = fopen("data.txt", "r");
if (fp == NULL) {
    perror("fopen");
    return 1;
}
```

```c
int *p = malloc(sizeof(int));
if (p == NULL) {
    return 1;
}
```

## 26.2 输入限制长度

错误：

```c
char name[16];
scanf("%s", name);
```

正确：

```c
scanf("%15s", name);
```

更推荐：

```c
fgets(name, sizeof(name), stdin);
```

## 26.3 字符串操作考虑缓冲区大小

推荐：

```c
snprintf(buffer, sizeof(buffer), "%s", source);
```

## 26.4 释放后置空

```c
free(p);
p = NULL;
```

## 26.5 不返回局部变量地址

错误：

```c
char *get_name(void) {
    char name[32] = "Tom";
    return name;
}
```

## 26.6 不修改字符串字面量

错误：

```c
char *s = "hello";
s[0] = 'H';
```

正确：

```c
char s[] = "hello";
s[0] = 'H';
```

## 26.7 避免宏副作用

错误：

```c
#define MAX(a, b) ((a) > (b) ? (a) : (b))
int x = MAX(i++, j++);
```

更推荐：

```c
static inline int max_int(int a, int b) {
    return a > b ? a : b;
}
```

---

# 27. C语言与 Java 的核心区别

| 对比点 | C语言 | Java |
|---|---|---|
| 编译运行 | 编译成本地机器码 | 编译为字节码，由 JVM 运行 |
| 内存管理 | 手动管理 | GC 自动管理 |
| 指针 | 显式指针 | 无显式指针 |
| 面向对象 | 不原生支持 | 原生支持 |
| 标准库 | 较小 | 丰富 |
| 跨平台 | 需要重新编译 | 一次编译，多处运行 |
| 常见问题 | 野指针、内存泄漏、越界 | 空指针、GC、并发 |
| 使用场景 | 系统底层、嵌入式、性能关键场景 | 企业应用、Web后端、大数据等 |

Java 学习者学 C 要重点转变：

1. C 没有对象引用概念，只有值和地址。
2. C 没有自动垃圾回收，内存要手动释放。
3. C 数组不保存长度，传参时要额外传长度。
4. C 字符串不是对象，而是以 `\0` 结尾的字符数组。
5. C 函数参数默认是值传递，想修改外部变量要传地址。

---

# 28. 高频面试题

## 28.1 `malloc` 和 `calloc` 区别

| 对比 | `malloc` | `calloc` |
|---|---|---|
| 参数 | 总字节数 | 元素个数 + 单个元素大小 |
| 初始化 | 不初始化 | 初始化为 0 |
| 性能 | 通常略快 | 可能略慢 |
| 用法 | `malloc(n * sizeof(int))` | `calloc(n, sizeof(int))` |

## 28.2 `sizeof` 和 `strlen` 区别

```c
char s[] = "hello";

sizeof(s);  // 6
strlen(s);  // 5
```

- `sizeof` 是运算符，统计对象占用字节数。
- `strlen` 是函数，统计字符串 `\0` 前字符数。

## 28.3 指针和数组区别

| 对比 | 数组 | 指针 |
|---|---|---|
| 本质 | 一块连续内存 | 保存地址的变量 |
| `sizeof` | 整个数组大小 | 指针变量大小 |
| 可否重新赋值 | 数组名不能重新指向 | 指针可以改指向 |
| 函数传参 | 会退化为指针 | 本身就是指针 |

## 28.4 `const int *p`、`int *const p` 区别

```c
const int *p;        // 不能通过 p 改值，p 可改指向
int *const p = &a;   // p 不能改指向，可通过 p 改值
const int *const p = &a; // p 不能改指向，也不能通过 p 改值
```

## 28.5 什么是野指针

野指针是指向未知或非法内存的指针。来源包括：

- 未初始化指针。
- 指向已经释放的内存。
- 指向已经结束生命周期的局部变量。
- 指针越界。

## 28.6 什么是内存泄漏

动态申请的内存没有释放，并且程序已经失去这块内存的指针，导致无法释放。

```c
void func(void) {
    int *p = malloc(sizeof(int));
    // 忘记 free(p)
}
```

## 28.7 栈和堆区别

| 对比 | 栈 | 堆 |
|---|---|---|
| 管理方式 | 编译器自动管理 | 程序员手动管理 |
| 存放内容 | 局部变量、函数调用信息 | 动态分配内存 |
| 生命周期 | 函数调用期间 | `malloc` 到 `free` |
| 速度 | 快 | 相对慢 |
| 常见问题 | 栈溢出 | 内存泄漏、野指针、碎片 |

## 28.8 `static` 的作用

1. 修饰局部变量：生命周期延长到整个程序运行期间。
2. 修饰全局变量：限制变量只在当前文件可见。
3. 修饰函数：限制函数只在当前文件可见。

## 28.9 `extern` 的作用

用于声明外部符号：

```c
extern int global_count;
```

告诉编译器：这个变量在别的文件中定义。

## 28.10 宏和函数区别

| 对比 | 宏 | 函数 |
|---|---|---|
| 阶段 | 预处理阶段文本替换 | 编译后调用 |
| 类型检查 | 无 | 有 |
| 调用开销 | 无函数调用开销 | 有调用开销，可能被内联 |
| 安全性 | 容易出副作用 | 更安全 |
| 调试 | 不方便 | 方便 |

## 28.11 `memcpy` 和 `memmove` 区别

- `memcpy()`：源和目标内存区域不能重叠。
- `memmove()`：可以处理重叠内存区域。

## 28.12 什么是未定义行为

未定义行为是 C 标准没有规定结果的行为。程序可能崩溃，也可能看起来正常，也可能不同编译器结果不同。

常见未定义行为：

- 数组越界。
- 解引用空指针。
- 使用未初始化变量。
- 有符号整数溢出。
- 修改字符串字面量。
- 释放后继续使用指针。
- 同一表达式中多次无序修改同一个变量。

---

# 29. 常见错误速查表

| 错误现象 | 可能原因 | 排查方向 |
|---|---|---|
| `Segmentation fault` | 空指针、野指针、越界访问 | 用 `gdb`、`AddressSanitizer` |
| 输出随机值 | 变量未初始化 | 初始化所有变量 |
| 字符串乱码 | 缺少 `\0`、缓冲区越界 | 检查数组长度和结束符 |
| `undefined reference` | 函数未定义或未链接 | 检查 `.c` 文件是否参与编译 |
| `multiple definition` | 头文件中定义全局变量 | 头文件用 `extern` |
| 程序卡死 | 死循环、阻塞输入 | 检查循环条件和输入 |
| 内存越来越大 | 内存泄漏 | 用 `valgrind` |
| `double free` | 重复释放 | `free` 后置 `NULL` |
| 修改字符串崩溃 | 修改字符串字面量 | 使用字符数组 |
| 数组长度错误 | 函数参数中 `sizeof` 指针 | 额外传入数组长度 |

---

# 30. 后续学习方向

## 30.1 基础练习

- 判断素数
- 最大公约数
- 排序算法
- 二分查找
- 字符串反转
- 统计单词数量
- 简单学生管理系统
- 文件版通讯录

## 30.2 指针练习

- 交换两个变量
- 动态数组
- 字符串拷贝函数
- 字符串比较函数
- 单链表增删改查
- 二级指针分配内存
- 函数指针计算器

## 30.3 数据结构练习

- 顺序表
- 单链表
- 双链表
- 栈
- 队列
- 哈希表
- 二叉树
- 堆
- 图的邻接表

## 30.4 工程练习

推荐实现一个多文件项目：

```text
student_manager/
  ├── include/
  │   ├── student.h
  │   └── vector.h
  ├── src/
  │   ├── main.c
  │   ├── student.c
  │   └── vector.c
  ├── data/
  │   └── students.txt
  └── Makefile
```

功能：

- 新增学生
- 删除学生
- 修改学生
- 查询学生
- 按成绩排序
- 文件保存和加载

## 30.5 深入方向

| 方向 | 后续学习 |
|---|---|
| 操作系统 | 进程、线程、内存管理、文件系统 |
| Linux C | 系统调用、`fork`、`exec`、`pipe`、`socket` |
| 网络编程 | TCP/UDP、`select`、`poll`、`epoll` |
| 嵌入式 | 寄存器、单片机、RTOS、驱动 |
| 编译原理 | 词法分析、语法分析、代码生成 |
| 高性能服务 | 内存池、线程池、无锁队列 |
| 安全方向 | 缓冲区溢出、格式化字符串漏洞、栈保护 |
| C++ 过渡 | RAII、类、模板、STL、智能指针 |

---

# 附录 A：推荐编译命令

普通学习：

```bash
gcc -std=c11 -Wall -Wextra main.c -o main
```

调试：

```bash
gcc -std=c11 -Wall -Wextra -g -O0 main.c -o main
```

内存检查：

```bash
gcc -std=c11 -Wall -Wextra -g -O0 -fsanitize=address,undefined main.c -o main
```

多文件：

```bash
gcc -std=c11 -Wall -Wextra -g -Iinclude src/main.c src/student.c -o app
```

---

# 附录 B：C语言关键概念一句话总结

- `指针`：保存地址的变量。
- `数组`：连续存储的一组同类型元素。
- `字符串`：以 `\0` 结尾的字符数组。
- `结构体`：把多个不同类型的数据组合成一个类型。
- `联合体`：多个成员共用同一块内存。
- `枚举`：给一组整数常量起名字。
- `malloc`：在堆上申请内存。
- `free`：释放堆内存。
- `sizeof`：计算对象或类型占用字节数。
- `static`：延长生命周期或限制文件作用域。
- `extern`：声明外部符号。
- `const`：限制通过某个名字修改值。
- `宏`：预处理阶段文本替换。
- `头文件`：放声明，不放普通变量定义。
- `栈`：自动管理，函数结束后释放。
- `堆`：手动管理，忘记释放会泄漏。
- `段错误`：访问了不该访问的内存。
- `未定义行为`：标准没有规定结果，任何情况都可能发生。

---

# 附录 C：C23 了解即可

C23 是当前较新的 C 标准，常见新增或强化内容包括：

- `nullptr`
- `typeof` / `typeof_unqual`
- 二进制整数字面量，例如 `0b1010`
- 标准属性语法扩展，例如 `[[nodiscard]]`
- `static_assert`
- 更现代的预处理能力
- 一些库函数和语言细节改进

学习建议：

- 基础阶段按 `C99 / C11` 学习即可。
- 面试和工程重点不在 C23 新语法，而在 `指针`、`内存`、`编译链接`、`数据结构`、`调试能力`。
- 了解 C23 可以帮助你阅读新代码，但不要替代 C 语言基本功。
