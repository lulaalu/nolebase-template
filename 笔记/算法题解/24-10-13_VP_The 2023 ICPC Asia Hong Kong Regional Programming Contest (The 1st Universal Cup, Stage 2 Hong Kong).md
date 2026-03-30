---
归类:
  - "[[笔记/算法/算法题解/XCPC/XCPC]]"
---
## L. Permutation Compression

### 题意

有初始序列 $a$ ，终序列 $b$ ，$k$ 个工具，每个工具 $i$ 可以删除 $l_i$ 长度内的最大值，能不能要把 $a$ 删成 $b$
### 思路

首先，[[../../../数据结构与算法/贪心]]的想一定是从大到小删除，先把最大的删了，那么要删除后续的数字，就可以用相对较大的 $l_i$ 

否则被大的数字限制就只能用较小的 $l_i$ ，不利于整体

然后，按照这个策略，当删除到某个数 $x$ 时，先获取这个数的最大可用的 $l$ ，也就是获取左右的最近的比 $x$ 大的位置，然后从工具中挑选小于等于 $L$ 的最大 $l$ 

一开始假设都删除的话，工具是随便用的，当出现某个值要保留时，将这个数的位置存在 $set$ 中

然后下次删除其他数 $x$ 时，由于 $set$ 中的所有数都大于 $x$ ，那么找一下在哪两个数中间，再找一下符合的 $l$

由于通过 $set$ 找的区间不能确定在这之间还剩下多少数字，用[[树状数组]]维护
### 代码

```CPP
##pragma GCC optimize(2)
##include <bits/stdc++.h>
using namespace std;

##define endl "\n"
##define IOS ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
##define rep(i, j, k) for (int i = (j); i < (k); i++)
##define all(x) x.begin(), x.end()
##define pii pair<int, int>

const int N = 2e5 + 10;

// 区间剩余数的数量
int tree[N];
int lowbit(int x) { return x & (-x); }
void add(int x, int p) {
    while (p < N) {
        tree[p] += x;
        p += lowbit(p);
    }
}

int query(int p) {
    int ans = 0;
    while (p) {
        ans += tree[p];
        p -= lowbit(p);
    }
    return ans;
}

int n, m, k;
int a[N], b[N];
int pos[N];
multiset<int> l;
multiset<int> bst;

set<int> rese; // a中reserve的数的下标

// 根据posi找到最大的区间
pii Find(int p) {
    auto itl = rese.upper_bound(p);
    auto itr = itl;
    itl--;
    return {*itl, *itr};
}

// 根据rg，从l中找出符合的最大l
bool Erase(int rg) {
    auto it = l.lower_bound(rg);

    // 找到等于rg的
    if (it != l.end() && *it == rg) {
        l.erase(l.find(rg));
        return true;
    }

    // 找不到比rg小的
    if (it != l.end() && it == l.begin()) {
        return false;
    }

    // 找到
    it--;
    l.erase(l.find(*it));
    return true;
}

string solve() {
    cin >> n >> m >> k;

    rese.clear();
    fill_n(tree, n + 1, 0);
    l.clear();
    rese.clear();
    bst.clear();

    rep(i, 1, n + 1) {
        cin >> a[i];
        pos[a[i]] = i;
        add(1, i); // i位置加1
    }
    rep(i, 1, m + 1) {
        cin >> b[i];
        bst.insert(b[i]);
    }
    rep(i, 0, k) {
        int tmp;
        cin >> tmp;
        l.insert(tmp);
    }

    // 确认道具数量
    if (m + k < n) return "NO";
    // 确定b是a的子串
    int aa = 1, bb = 1;
    while (aa <= n && bb <= m) {
        if (a[aa] == b[bb])
            aa++, bb++;
        else
            aa++;
    }
    if (bb != m + 1) return "NO";

    rese.insert(0);
    rese.insert(n + 1);
    for (int x = n; x >= 1; x--) { // 从大到小删
        if (bst.find(x) != bst.end()) {
            rese.insert(pos[x]);
        } else {
            auto [l, r] = Find(pos[x]);
            l++, r--;
            int rg = query(r) - query(l - 1);
            if (!Erase(rg)) return "NO";
            add(-1, pos[x]);
        }
        bst.erase(x); // 可有可无
    }
    return "YES";
}

int main() {
    IOS;
    int t;
    cin >> t;
    while (t--) cout << solve() << endl;
    return 0;
}
```
## E. Goose, Goose, DUCK?

### 题意

给一个数组 $a$ ，每次选一个区间，从区间内是否有大于等于 $k$ 个数字是相等的，如果有，这个区间是危险的

找有多少不危险的区间
### 思路

假定初始是空数组，每次往里加入一个数字

对于新加入的数字，以这个位置为 $r$ ，存在一个 $l$ 的范围使 $[l,r]$ 为危险区间，那么这段范围上的 $l$ 对于这个 $r$ 来说不可用，我们先记作 $r_1$

那么现在再次加入一个数，位置是 $r_2$

首先 $r_2$ 有自己的危险区间，同时要想往左扩充， $r_1$ 的危险区间也是不能接触的，那么就意味着其他的区间是安全的

所以用线段数维护，0为安全部分，非0为危险

线段树要能 1.查询最小值和最小值数量  2.区间+1

## 题目名

### 题意

### 思路

### 代码

```CPP

```