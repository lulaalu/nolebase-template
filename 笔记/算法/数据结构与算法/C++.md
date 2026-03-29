---
归类:
  - "[[数据结构与算法]]"
---
# $std::array$ 的一些基本用法

## 1. 初始化

`array<int, 5> arr = {1, 2, 3, 4};`
即 $array<type,size>$
## 2. 赋值

直接用索引访问和赋值，或者`arr.at(i)`
## 3. 内置函数

`arr.begin()`
`arr.end()`
# 标准库 iomanip

[总结参考](https://www.runoob.com/cplusplus/cpp-libs-iomanip.html)

- `setw(int)` 设置宽度
剩余部分用空格填充

- `setprecision(int)` 设置精度
设置小数点后的位数

- `fixed` 和 `scientific`
```C++
double num = 123456789.0;  
cout << fixed << num;      // 123456789.000000
cout << scientific << num; // 1.23456789e+08
```

- `setfill(char)` 设置填充字符
```CPP
cout << setfill('*') << setw(10) << "World";
// *****World
```

# 局部函数

```C++
int n = 10, m = 20;
auto f = [n, &m](int a, int b) {
	// n = 30; 不可修改
	m = 30;
	cout << m << " " << a << " " << b << endl;
};
f(1, 2);
// output:30 1 2
```

[细节讲解](https://www.bilibili.com/video/BV1fG41157Ew/?spm_id_from=333.337.search-card.all.click&vd_source=10b3ee1feea99fbdd76960a61a32f71e)
# STL priority_queue

## 自定义排序方式

```CPP
auto cmp = [&](int a, int b) { return a < b; };
priority_queue<int, vector<int>, decltype(cmp)> pq(cmp);
```

# 快读快写

```CPP
template <typename T>
inline T read() {
    T x = 0, f = 1;
    char ch = 0;
    for (; !isdigit(ch); ch = getchar())
        if (ch == '-') f = -1;
    for (; isdigit(ch); ch = getchar()) x = (x << 3) + (x << 1) + (ch - '0');
    return x * f;
}

template <typename T>
inline void write(T x) {
    if (x < 0) putchar('-'), x = -x;
    if (x > 9) write(x / 10);
    putchar(x % 10 + '0');
}

template <typename T>
inline void print(T x, char ed = '\n') {
    write(x), putchar(ed);
}
```
# STL去重

- `unique` 将arr中相邻的相同元素放在末尾
```CPP
vector<int>arr={...};
arr.erase(unique(arr.begin(),arr.end()),arr.end());
```

# 随机数生成

```CPP
unsigned seed1 = std::chrono::system_clock::now().time_since_epoch().count();
std::mt19937_64 g(seed1);// 数据范围 ull
```

# bitwise

参考：https://oi-wiki.org/lang/csl/bitset/

