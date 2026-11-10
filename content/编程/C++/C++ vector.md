---
tags:
  - 编程/cpp
---

# C++ vector

vector 是标准库里的**动态数组**：长度可变、自动管理内存，不用自己 `new`/`delete`。头文件 `<vector>`。

## 定义方式

```cpp
#include <vector>
using namespace std;

vector<int> v1;                 // 空 vector，长度为 0
vector<int> v2(10);             // 10 个元素，全部初始化为 0
vector<int> v3(10, 7);          // 10 个元素，全部初始化为 7
vector<int> v4{1, 2, 3, 4, 5};  // 列表初始化，直接给值
vector<int> v5(v4);             // 拷贝 v4，复制一份
vector<int> v6(v4.begin(), v4.begin() + 3);  // 用 v4 的前 3 个元素构造

// 二维 vector（矩阵、图邻接表常用）
vector<vector<int>> grid(3, vector<int>(4, 0));  // 3 行 4 列，全 0
```

> 模板参数可以是任意类型：`vector<double>`、`vector<string>`、`vector<pair<int,int>>` 等。

## 基本操作

```cpp
vector<int> v = {10, 20, 30};

// 访问元素
v[0];            // 下标访问，越界不检查（快）
v.at(0);         // 下标访问，越界抛异常（安全）
v.front();       // 第一个元素 → 10
v.back();        // 最后一个元素 → 30

// 遍历
for (int i = 0; i < (int)v.size(); i++) cout << v[i];      // 下标
for (int x : v) cout << x;                                 // 范围 for，推荐
for (auto it = v.begin(); it != v.end(); it++) cout << *it;  // 迭代器

// 修改
v[1] = 99;
v.push_back(40);      // 尾部追加：{10, 99, 30, 40}
v.pop_back();         // 删除尾部：{10, 99, 30}
```

## 常用成员函数

| 函数 | 作用 | 示例（`v = {10,20,30}`） |
| ---- | ---- | ---- |
| `v.size()` | 元素个数 | `3` |
| `v.empty()` | 是否为空 | `false` |
| `v.clear()` | 清空所有元素 | `{}` |
| `v.push_back(x)` | 尾部追加 | `v.push_back(40)` → `{10,20,30,40}` |
| `v.pop_back()` | 删除尾部 | → `{10,20}` |
| `v.front()` / `v.back()` | 首/尾元素 | `10` / `30` |
| `v.resize(n)` | 改变长度，多出的补 0 | `v.resize(5)` → `{10,20,30,0,0}` |
| `v.resize(n, x)` | 改变长度，多出的补 x | `v.resize(5, 7)` → `{10,20,30,7,7}` |
| `v.insert(it, x)` | 在迭代器位置插入 | `v.insert(v.begin()+1, 5)` → `{10,5,20,30}` |
| `v.erase(it)` | 删除迭代器位置的元素 | `v.erase(v.begin())` → `{20,30}` |
| `v.assign(n, x)` | 重置为 n 个 x | `v.assign(3, 0)` → `{0,0,0}` |

## 配合 `<algorithm>` 的常用算法

```cpp
sort(v.begin(), v.end());                  // 升序排序
sort(v.begin(), v.end(), greater<int>());  // 降序
reverse(v.begin(), v.end());               // 反转
find(v.begin(), v.end(), 20);              // 查找，找到返回迭代器，否则返回 v.end()
count(v.begin(), v.end(), 20);             // 统计出现次数
max_element(v.begin(), v.end());           // 最大值的位置，解引用 * 取值
```

## 传给函数

```cpp
void fun(vector<int>& v) {          // 加 & 传引用，避免整个拷贝
    v.push_back(1);
}

void print(const vector<int>& v) {  // 只读就加 const
    for (int x : v) cout << x << " ";
}
```

> 一句话总结：vector 就是替你管好了一块堆内存的数组，能申请时给初始值、随时变长、自动释放。
> 手动 `new`/`delete` 的写法见 [[C++ 栈、堆与动态数组]]。
