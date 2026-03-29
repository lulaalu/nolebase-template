---
归类:
  - "[[XCPC]]"
---
[The 2023 ICPC Asia Jinan Regional Contest (The 2nd Universal Cup. Stage 17: Jinan)](https://codeforces.com/gym/104901)
# K. Rainbow Subarray

## 题意

给定数组和操作次数 $k$，每次操作可以对某个数 $+1/-1$ ，要使一个最长的子串达到 $a_i+1=a_{i+1}$ 的情况

## 思路

首先将 $a_i+1=a_{i+1}$ 转化成 $a_i-i=a_{i+1}-(i+1)$ 

那么将所有 $a_i-i$ ，现在的问题转化为花费小于等于 $k$，使子数组相等的最长子数组长度
 
1. 我们考虑一个任意的数组，要将其用最小的代价转换成完全一样，肯定是都转化成中位数
2. 我们考虑长度为 $n$ 的数组，在后面加上任意一个数，那么将其转化为相等的花费一定大于等于原本长度

所以假定长度为 $m$ 的数组转化为中位数的花费小于 $k$ ，那么就有加长的空间

可以通过[[../../../数据结构与算法/双set维护中位数]]，这个讲的还挺详细的=>https://www.cnblogs.com/luckyblock/p/18159496

关于上面的第一点：
	想象在有序数组中取mid，假定数组长度为n，那么所有数转化为mid的花费就是
	$mid*\frac{n}{2}-\sum_{i=1}^{\frac{n}{2}}a + \sum_{i=\frac{n}{2}}^{n}a-mid*\frac{n}{2}$
	只是大概写一下，n不一定是偶数
	如果mid往左偏1格，对原本的贡献就是 $abs(cnt_{l}+cnt_r)$，如果mid左右数量相等贡献不变，但是一直偏一定会导致 数量不平衡

## 代码

```CPP
#include <bits/stdc++.h>
using namespace std;

#define int long long
#define endl "\n"
#define IOS ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
#define rep(i, j, k) for (int i = (j); i < (k); i++)
#define all(x) x.begin(), x.end()
#define pii pair<int, int>

multiset<int> le, gr;
int sle = 0, sgr = 0;// sumle，sumgr
int n, k;
int arr[500010];

// lst<=>max(st)
multiset<int>::iterator lst(multiset<int> &st) {
    auto en = st.end();
    en--;
    return en;
}

void adj() {
    if (le.size() > gr.size() + 1) {
        int Amx = *lst(le);
        le.erase(lst(le));
        gr.insert(Amx);
        sle -= Amx;
        sgr += Amx;
    }
    if (le.size() < gr.size()) {
        int Bmi = *gr.begin();
        le.insert(Bmi);
        gr.erase(gr.begin());
        sle += Bmi;
        sgr -= Bmi;
    }
}

void erase(int num) {
    if (le.find(num) != le.end()) {
        le.erase(le.find(num));
        sle -= num;
    } else if (gr.find(num) != gr.end()) {
        gr.erase(gr.find(num));
        sgr -= num;
    }
    adj();
}

void insert(int num) {
    if (le.size() == 0) {
        le.insert(num);
        sle += num;
        return;
    }
    int Amx = *lst(le);
    if (num <= Amx) {
        sle += num;
        le.insert(num);
    } else {
        sgr += num;
        gr.insert(num);
    }
    adj();
}

int cal() {
    int mid = *lst(le);
    return mid * le.size() - sle + sgr - mid * gr.size();
}

void solve() {
    sle = 0, sgr = 0;
    le.clear(), gr.clear();
    cin >> n >> k;
    rep(i, 0, n) {
        cin >> arr[i];
        arr[i] -= i + 1;
    }
    int len = -1;
    // 先找到临界值，为len长度时正好不会超过k
    for (int i = 0; i < n; i++) {
        insert(arr[i]);
        if (cal() > k) {
            len = i;
            break;
        }
    }
    if (len == -1) {
        cout << n << endl;
        return;
    }
    int ans = len;
    len++;
    // 由于已经找到临界值，所以以下操作使le，gr维护的区间长度大于等于len
    for (int i = len; i < n; i++) {
        if (cal() > k) {
            erase(arr[i - len]);
            insert(arr[i]);
        } else {
            ans++;
            insert(arr[i]);
            len++;
        }
    }
    if (cal() <= k) {
        ans++;
    }
    cout << ans << endl;
}

signed main() {
    IOS;
    int t = 1;
    cin >> t;
    while (t--) {
        solve();
    }
    return 0;
}
```