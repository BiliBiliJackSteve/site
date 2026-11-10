---
tags:
  - 编程/cpp
---

# C++ 栈、堆与动态数组

## 栈（Stack）与堆（Heap）怎么选

| 场景 | 选择 | 原因 |
| ---- | ---- | ---- |
| 小变量、小数组、大小已知 | 栈（`int a; int arr[100];`） | 省事 |
| 数组很大（几百 KB 以上） | 堆 | 栈放不下 |
| 大小运行时才确定（先问用户 n） | 堆 | 这是唯一办法（或用 vector） |
| 数据要跨函数 / 长时间存活 | 堆 | 栈变量出作用域就没了 |
| 追求极致速度的小对象 | 栈 | 分配更快 |

## 动态数组（堆上的数组）

### 创建与释放

```cpp
int n;
cin >> n;                  // 大小运行时才确定 → 必须用堆
int* arr = new int[n];     // 在堆上申请 n 个 int

// ... 使用 arr ...

delete[] arr;              // 必须配对释放，数组要用 delete[]
arr = nullptr;             // 防止悬空指针
```

要点：
- `new[]` 配 `delete[]`，`new` 配 `delete`，不能混用
- 谁申请谁释放，忘记 `delete[]` 就是内存泄漏

### 初始化

**申请时初始化**：

```cpp
int n = 10;

int* a = new int[n];                    // 基本类型是随机垃圾值，不是 0！
int* b = new int[n]();                  // 值初始化：全部清零
int* c = new int[n]{};                  // C++11 推荐写法：同上，全 0
int* d = new int[5]{1, 2, 3, 4, 5};     // C++11：逐个指定，少了的补 0
```

> ⚠️ 最常踩的坑：`new int[n]` 里的是垃圾值。想清零必须加 `()` 或 `{}`。

**申请后初始化**：

```cpp
for (int i = 0; i < n; i++) arr[i] = i * 2;   // 循环赋值
std::fill_n(arr, n, 0);                        // 清零/固定值，需 <algorithm>
memset(arr, 0, n * sizeof(int));               // 只用于清零，需 <cstring>
for (int i = 0; i < n; i++) cin >> arr[i];     // 从输入读入
```

> ⚠️ `memset` 按字节填充，清零没问题，但 `memset(arr, 1, ...)` 得到的是 `0x01010101` 不是 1，不能用来赋非零值。

**更省心的选择：vector**（不用自己 `delete[]`）：

```cpp
vector<int> v(n);        // n 个元素，全部初始化为 0
vector<int> v(n, 7);     // n 个元素，全部初始化为 7
vector<int> v{1, 2, 3};  // 直接给初始值
```

更多关于vector的内容见[[C++ vector]]

### 传给函数（fun 传参范例）

数组传参本质是传指针，因此**大小要一起传**：

```cpp
void fun(int* arr, int n) {   // 等价写法：void fun(int arr[], int n)
    for (int i = 0; i < n; i++) {
        // 处理 arr[i]
    }
}

int main() {
    int* a = new int[n];
    fun(a, n);
    delete[] a;
}
```

### 配合 cin 读入"个数未知的一行数据"（prc13 模式）

```cpp
while (cin.peek() != '\n') {   // 先"偷看"下一个字符是不是回车
    cin >> arr[i++];           // 不是回车就读一个数
}
```

- 是回车 → 停止，且**不取走**它，数据不会读丢
- 不是回车 → 继续 `cin >>` 读数

`cin.peek()` 的更多用法见 [[C++ cin 输入流]]。
