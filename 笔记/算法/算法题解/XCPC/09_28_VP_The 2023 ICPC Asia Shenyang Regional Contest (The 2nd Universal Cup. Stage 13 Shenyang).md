---
归类:
  - "[[XCPC]]"
---
# K. Maximum Rating

原题：[K. Maximum Rating](https://codeforces.com/gym/104869/problem/K)
## 题意

总共有 $n$ 个回合，每个回合都有固定分数

你每次可以篡改一个回合的分数，然后将分数任意排列

初始分数和最高评分记录都为0，在某种排序下最高评分能破 $x$ 次记录

每次篡改分数后输出这 $n$ 个回合所有的x有几个

以序列 $[1,2,3]$ 为例

第一次篡改1位置分数为-2，可能性有：$[2,3,-2],[-2,2,3]$ 分别破了2、3次记录

所以输出2，其他的排序如 $[2,-2,3]$ 也是破2次记录
## 思路

每次篡改分数后，先将正数排序，考虑负数的总和 $negsum$ 能消除多少正数的影响

即 $negsum+\sum_{i=1}^{k}\le0$ ，$k+1$ 就是答案

那么现在考虑用一个数据结构维护每次操作后的前缀和

采用[[../../../数据结构与算法/线段树/权值线段树]]

### 权值线段树

普通的线段树维护区间最大最小等能从子树加工而来的参数

线段树维护当前结点维护的范围内的点的个数和总和

比如维护 $[1,1,2,2,3,4,4]$ ，那么线段树维护的总长就是4，因为有4个不同的数字

枚举出维护的点：

```
(l,r):i  sum[i]  cnt[i]
(1,4):1  17      7
(1,2):2  5       4
(3,4):3  11      3
(1,1):4  2       2
(2,2):5  2       4
(3,3):6  3       1
(4,4):7  8       2
```

一般离散化后维护
## 代码

本题中0没用，不处理

```CPP
#include <bits/stdc++.h>
using namespace std;

#define endl "\n"
#define IOS ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
#define rep(i, j, k) for (int i = (j); i < (k); i++)
#define all(x) x.begin(), x.end()
#define pii pair<int, int>
#define int long long
const int N = 4e5 + 10;
int n, q;

int arr[N >> 1];
pii query[N >> 1];
int cnt[N << 2];
int sum[N << 2];

int negsum = 0; // 采用正数

// 离散化
int dis[N];
int discnt = 1;// 去重后的长度
void discretizing() {
    sort(dis, dis + discnt);
    int m = 1;
    for (int i = 2; i < discnt; i++) {
        if (dis[m] != dis[i]) {
            dis[++m] = dis[i];
        }
    }
    discnt = m;
}

void up(int i) {
    cnt[i] = cnt[i << 1] + cnt[i << 1 | 1];
    sum[i] = sum[i << 1] + sum[i << 1 | 1];
}

void func_add(int op, int jobv, int l, int r, int i) {
    if (l == r) {
        cnt[i] += op;
        sum[i] += op * dis[jobv];
        return;
    }
    int mid = (l + r) >> 1;
    if (jobv <= mid)
        func_add(op, jobv, l, mid, i << 1);
    else
        func_add(op, jobv, mid + 1, r, i << 1 | 1);
    up(i);
}
int func_query(int now, int l, int r, int i) {
    if (l == r) {
        if (negsum >= now + sum[i])
            return cnt[i];
        else {
            int tmp = negsum - now;
            return tmp / dis[l];
        }
    }
    int mid = (l + r) >> 1;
    if (sum[i << 1] + now >= negsum)
        return func_query(now, l, mid, i << 1);
    else if (sum[i << 1] + now < negsum)
        return func_query(sum[i << 1] + now, mid + 1, r, i << 1 | 1) + cnt[i << 1];
}

int getRank(int x) {
    return lower_bound(dis, dis + discnt + 1, x) - dis;
}

signed main() {
    IOS;

    fill_n(cnt, N << 2, 0);
    fill_n(sum, N << 2, 0);

    cin >> n >> q;
    dis[0] = -1e18;// 获取排名从1开始

	// 获取所有参与的正整数，不超过4e5个
    rep(i, 1, n + 1) {
        cin >> arr[i];
        if (arr[i] > 0) dis[discnt++] = arr[i];
    }
	
    rep(i, 0, q) {
        int pos, num;
        cin >> pos >> num;
        query[i].first = pos;
        query[i].second = num;
        if (num > 0) dis[discnt++] = num;
    }
	// 去重离散化
    discretizing();

    rep(i, 1, n + 1) {
        if (arr[i] < 0)
            negsum -= arr[i];
        else if (arr[i] > 0) {
            int rk = getRank(arr[i]);// getRank，获取排名
            func_add(1, rk, 1, discnt, 1);
        }
    }

    rep(i, 0, q) {
        int pos = query[i].first;
        int num = query[i].second;
        if (arr[pos] > 0) {
            func_add(-1, getRank(arr[pos]), 1, discnt, 1);
        } else if (arr[pos] < 0) {
            negsum += arr[pos];
        }

        arr[pos] = num;

        if (num > 0) {
            func_add(1, getRank(num), 1, discnt, 1);
        } else if (num < 0) {
            negsum -= num;
        }

        cout << func_query(0, 1, discnt, 1) + 1 << endl;
    }

    return 0;
}
```