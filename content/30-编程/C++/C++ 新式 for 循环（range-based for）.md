---
tags:
  - 编程/cpp
---

# C++ 新式 for 循环（range-based for）

C++11 引入的**基于范围的 for 循环**，极大简化了遍历容器和数组的代码：提高可读性，省去手动设置迭代器或索引的麻烦。

## 基本语法

```cpp
for (auto element : container) {
    // 对 element 进行操作
}
```

- `auto` 关键字用于自动推导类型
- `container` 可以是数组、`std::vector` 等容器

## 示例：遍历数组和容器

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    int arr[] = {1, 2, 3, 4, 5};
    vector<int> vec = {6, 7, 8, 9, 10};

    // 遍历数组
    for (auto n : arr) {
        cout << n << " ";
    }
    cout << endl;

    // 遍历容器
    for (auto n : vec) {
        cout << n << " ";
    }
    cout << endl;

    return 0;
}
```

## 修改元素：使用引用

```cpp
for (auto& n : vec) {
    n += 1;   // 修改每个元素的值
}
```

## 遍历二维数组

新式 for 遍历二维数组，本质是**嵌套两层循环**：外层每次取出一行，内层遍历该行的每个元素。

### 原生二维数组

```cpp
int arr[3][4] = {
    {1, 2, 3, 4},
    {5, 6, 7, 8},
    {9, 10, 11, 12}
};

// 只读遍历
for (const auto& row : arr) {     // row 是 int(&)[4]，即一行
    for (int n : row) {
        cout << n << " ";
    }
    cout << endl;
}

// 修改元素
for (auto& row : arr) {
    for (auto& n : row) {
        n *= 2;
    }
}
```

⚠️ 原生二维数组**必须写 `auto&`**：原生数组类型不可按值拷贝，`auto row` 会尝试复制一个 `int[4]`，直接编译报错。

### `vector<vector<int>>`

```cpp
vector<vector<int>> mat = {{1, 2, 3}, {4, 5, 6}};

// 只读
for (const auto& row : mat) {
    for (int n : row) {
        cout << n << " ";
    }
}

// 修改
for (auto& row : mat) {
    for (auto& n : row) {
        n += 1;
    }
}
```

这里 `auto row` 可以编译，但会**复制整行**，开销大且修改无效，同样推荐 `const auto&` 或 `auto&`。

## 只遍历二维数组的一部分

新式 for 本身**不支持指定下标范围**（只会从头到尾遍历整个范围），想遍历部分，需要先造出一个代表部分的**视图**，再交给新式 for。

### 传统下标循环

范围明确时最直接，不必强行用新式 for：

```cpp
// 遍历第 1~2 行、第 1~2 列
for (int i = 1; i < 3; ++i) {
    for (int j = 1; j < 3; ++j) {
        cout << arr[i][j] << " ";
    }
}
```

### `std::span`（C++20）

```cpp
#include <span>

int arr[3][4] = {/*...*/};

// 只遍历第 1、2 行，每行只取第 1、2 列
for (const auto& row : std::span(arr + 1, 2)) {   // 起点 + 行数
    for (int n : std::span(row + 1, 2)) {         // 起点 + 列数
        cout << n << " ";
    }
}
```

- `std::span` 圈出一段区间，零开销、不拷贝数据
- 对 `vector<vector<int>>` 用法相同，只是把 `arr + 1` 换成 `mat.begin() + 1`

### `std::views`（C++20，适合容器）

```cpp
#include <ranges>
namespace views = std::views;

vector<vector<int>> mat = {{1,2,3,4},{5,6,7,8},{9,10,11,12}};

// 跳过前 1 行取 2 行，每行跳过前 1 列取 2 列
for (const auto& row : mat | views::drop(1) | views::take(2)) {
    for (int n : row | views::drop(1) | views::take(2)) {
        cout << n << " ";
    }
}
```

### `std::mdspan`（C++23）

做规整的二维"切片"（任意行列区间）最优雅，但编译器支持尚不普遍：

```cpp
#include <mdspan>

int arr[3][4] = {/*...*/};
std::mdspan m(&arr[0][0], 3, 4);
auto sub = std::submdspan(m,
              std::pair{1, 3},   // 行 [1,3)
              std::pair{1, 3});  // 列 [1,3)
for (size_t i = 0; i < sub.extent(0); ++i)
    for (size_t j = 0; j < sub.extent(1); ++j)
        cout << sub[i, j] << " ";
```

### 方案选择

| 需求 | 推荐做法 |
|---|---|
| C++11/14/17 | 传统下标循环，或迭代器区间 |
| C++20 | `std::span` / `std::views` 圈定范围后用新式 for |
| C++23、复杂二维切片 | `std::mdspan` + `submdspan` |

## 注意事项

1. **只读限制**：默认情况下新式 for 循环是只读的（`auto n` 是拷贝），要修改元素必须使用引用（`auto&`）
2. **容器特性**：某些容器（如 `std::set`）的元素是只读的，即使使用引用也无法修改
3. **避免修改容器**：在循环中插入或删除元素可能导致迭代器失效，进而引发程序崩溃
4. **函数调用**：如果冒号后是一个函数调用，该函数只会被调用一次

## 优势

相比传统 `for` 循环和迭代器，代码更加简洁直观，尤其适合需要遍历整个容器的场景。
