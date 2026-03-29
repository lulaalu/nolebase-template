---
归类:
  - "[[数据结构与算法]]"
考点:
  - "[[set]]"
  - "[[stl]]"
---
https://www.cnblogs.com/luckyblock/p/18159496
https://blog.csdn.net/BLCJY/article/details/140054703

两个multiset，A，B

A存小于等于中位数的权值
B存大于等于中位数的权值

保持：$size(A)>=size(B),|size(A)-size(B)|\le1$

1. 先将极小值极大值插入AB
2. 每次插入，与根据A的最大值，也就是当前的中位数比，小于等于就插入A
3. 每次删除，在AB中查找
4. 每次插入删除后，调整AB的大小为相等或者 $size(A)=size(B)+1$
	- $size(A)>size(B)+1$ 取出A的最大值插入B
	- 取出B的最小值插入A

```CPP
#include <bits/stdc++.h>
using namespace std;

#define endl "\n"
#define IOS ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
#define rep(i, j, k) for (int i = (j); i < (k); i++)
#define all(x) x.begin(), x.end()
#define pii pair<int, int>

multiset<int> le, gr;
multiset<int>::iterator lst(multiset<int> &st) {
    auto en = st.end();
    en--;
    return en;
}
// 4
void adj() {
    if (le.size() > gr.size() + 1) {
        int Amx = *lst(le);
        le.erase(lst(le));
        gr.insert(Amx);
    }
    if (le.size() < gr.size()) {
        int Bmi = *gr.begin();
        gr.erase(gr.begin());
        le.insert(Bmi);
    }
}
void erase(int num) {
	// 3
    if (le.find(num) != le.end()) {
        le.erase(le.find(num));
    } else if (gr.find(num) != gr.end()) {
        gr.erase(gr.find(num));
    }
    adj();
}

void insert(int num) {
    if (le.size() == 0) {
        le.insert(num);
        return;
    }
    // 2
    int Amx = *lst(le);
    if (num <= Amx)
        le.insert(num);
    else
        gr.insert(num);
    adj();
}

void show(int len) {
    if (len & 1)
        cout << *lst(le) << endl;
    else {
        cout << *lst(le) << " " << *gr.begin() << endl;
    }
}

int main() {
    IOS;
    vector<int> a = {4, 2, 5, 7, 1, 8, 6};
    int n = a.size();
    int len = 4;
    // 1
    for (int i = 0; i < len; i++) {
        insert(a[i]);
    }
    show(len);
    for (int i = len; i < n; i++) {
        erase(a[i - len]);
        insert(a[i]);
        show(len);
    }
    return 0;
}
```