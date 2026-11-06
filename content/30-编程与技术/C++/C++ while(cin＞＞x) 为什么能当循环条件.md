---
tags:
  - 编程/cpp
---

# C++ while (cin >> x) 为什么能当循环条件

> 来源：洛谷 P1042 代码分析中的疑问——`while (cin >> c && c != 'E')` 为什么成立。

## `cin >> c` 返回什么？

`operator>>` 返回**流本身**（`istream&`），而流能通过 `operator bool()` 报告自己的状态：

| 状态 | 含义 | bool 值 |
| ---- | ---- | ---- |
| 正常 | 读取成功 | true |
| failbit | 读取失败（如类型不匹配） | false |
| eofbit | 遇到文件结尾 EOF | false（failbit 通常同时置位） |

所以 `while (cin >> c)` 的实际过程是：

1. 执行 `cin >> c`，尝试读一个字符
2. 表达式求值为 `cin`（`istream&` 类型）
3. `while` 需要 bool → 调用 `operator bool()`
4. 读取成功 → true 继续循环；读到 EOF/失败 → false 退出循环

> `explicit` 关键字表示这个转换不会在任意场合隐式发生（比如不能写 `int x = cin;`），但在 `while`、`if` 这类"布尔语境"中被标准特别允许隐式调用。

## 回到代码

```cpp
char c;
while (cin >> c && c != 'E')
    match += c;
```

执行顺序（`&&` 从左到右、短路求值）：

1. `cin >> c`：读入一个字符（`operator>>` 对 char 会自动跳过空白符，所以换行被忽略）
2. 转换为 bool：若读到 EOF → 整个条件为 false，退出
3. 若读取成功，再判断 `c != 'E'`：遇到 `E` 也退出循环
4. 两者都满足 → 把字符追加进 `match`

一行代码同时完成了「读入」「判断是否还能读」「判断是否为结束符」三件事，这是 C++ 读取不定长输入的经典写法：

```cpp
int x;
while (cin >> x) { ... }        // 读到文件末尾为止
string s;
while (getline(cin, s)) { ... } // getline 同理，也返回 istream&
```

> 一句话总结：`>>` 返回流本身，流能通过 `operator bool` 报告自己"是否还健康"，因此可以直接当作循环条件。

## 相关笔记

- [[C++ cin 输入流]] — cin 的成员函数（peek/get/ignore 等）
