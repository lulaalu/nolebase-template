---
归类:
  - "[[笔记/算法/算法题解/XCPC/XCPC]]"
---
[The 2024 CCPC Online Contest](https://codeforces.com/gym/105336)
- [ ] A 48/684
- [x] B 1948/5995
- [ ] C 149/1016
- [ ] D 1029/3177
- [a] E 472/1465
- [ ] F 65/329
- [ ] G 331/18677
- [ ] H 6/246
- [ ] I 313/785
- [a] J 429/2669
- [x] K 1897/6186
- [x] L 2129/2506
## E. 随机过程

### 题意

$n$ 个长度为 $m$ 的字符串，插入字典树

求字典树的结点最大值和期望

### 思路

单独考虑每一层，对于最大值，要求每个字符串的同一个位置尽量不同

第 $i$ 层的节点数最多为 $n$ 或者 $26^i$ ，所以答案就是 $\sum_{i=0}^{m}min(n,26^i)$

期望，同样考虑每一层，每层的 $26^i$ 个点出现概率是相同的，也就是期望相同

在一个字符串中，这个点出现的概率是 $\frac{1}{26^i}$

不出现的概率是 $1-\frac{1}{26^i}$

在 $n$ 个串中都不出现的概率是 $(1-\frac{1}{26^i})^n$ 

在 $n$ 个串中出现的概率是 $1-(1-\frac{1}{26^i})^n$

一个点是这么多概率，每层是 $26^i$ 个点，有 $m+1$ 层

所以答案为 $\sum_{i=0}^{m}[1-(1-\frac{1}{26^i})^n]26^i$ 

### 代码

在代码中，将答案化为
=>$\sum_{i=0}^{m}[1-(\frac{26^i-1}{26^i})^n]26^i$ 
=>$\sum_{i=0}^{m}[\frac{(26^i)^n-(26^i-1)^n}{(26^i)^n}]26^i$ 

```CPP
##include <bits/stdc++.h>
##define int long long
using namespace std;

int MOD = 998244353;
const int N = 1e5 + 10;
int f26i[N];    // 26^i
int f26in[N];   // 26^i^n
int f26in_1[N]; //(26^i-1)^n
int inv26in[N]; //(26^i^n)^{-1}
int ksm(int a, int b, int mod) {
    int ans = 1;
    while (b) {
        if (b & 1) ans = (ans * a) % mod;
        a = (a * a) % mod;
        b >>= 1;
    }
    return ans;
}

void init(int n, int m) {
    f26i[0] = 1;
    f26in[0] = 1;
    f26in_1[0] = 0;
    int k = ksm(26, n, MOD);
    for (int i = 1; i <= m; i++) {
        f26i[i] = (f26i[i - 1] * 26) % MOD;
        f26in[i] = (f26in[i - 1] * k) % MOD;
        f26in_1[i] = ksm(f26i[i] - 1, n, MOD);
    }
    inv26in[m] = ksm(f26in[m], MOD - 2, MOD);
    for (int i = m - 1; i >= 1; i--) inv26in[i] = (inv26in[i + 1] * k) % MOD;
    inv26in[0] = 1;
}

signed main() {
    int n, m;
    cin >> n >> m;
    init(n, m);
    int mx = 0;
    int ans = 0;
    bool flag = true;
    for (int i = 0; i <= m; i++) {
        if (f26i[i] > n) flag = false;
        if (!flag)
            mx = (mx + n) % MOD;
        else
            mx = (mx + f26i[i]) % MOD;
    }

    for (int i = 0; i <= m; i++) {
        int fenzi = ((f26in[i] - f26in_1[i]) % MOD + MOD) % MOD;
        int t1 = (fenzi * inv26in[i]) % MOD;
        int t2 = (t1 * f26i[i]) % MOD;
        ans = (ans + t2) % MOD;
    }
    cout << mx << " " << ans << endl;
    return 0;
}
```


## J. 找最小

### 题意

给序列 $a,b$ 长度为 $n$ 

可以任意交换 $a_i,b_i$ 要求最后的两个序列分别异或起来为 $A,B$ ，使 $max(A,B)$ 最小
### 思路

首先算出初始的 $A,B$

再求出每个 $i$ 的 $a_i \bigoplus b_i = c_i$

依次交换操作相当于对 $A,B$ 异或一个 $c_i$ 

那么先构造出线性基

如果：
1. $A,B$ 的对应位都为 $1$ ，异或上对应位为1的数
2. 如果对应位数都是 $0$，直接下一位
3. 如果对应位不同，记录这一位的位置，假定是 $A$ 的某一位是 $1$，$B$ 的对应位是 $0$，将 $A$ 的后续位数为 $1$ 的都尽量异或
### 代码

```CPP
##include <bits/stdc++.h>
using namespace std;

##define int long long
##define endl "\n"
##define IOS ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
##define rep(i, j, k) for (int i = (j); i < (k); i++)
##define all(x) x.begin(), x.end()
##define vi vector<int>

const int BIT = 32;
const int N = 1e6 + 10;
int n;
int a[N], b[N], basis[BIT + 1];
int A = 0, B = 0;
int len = 1;

void compute() {
    len = 1;
    for (int i = BIT; i >= 0; i--) {
        for (int j = len; j <= n; j++) {
            if (basis[j] >> i & 1) {
                if (j != len) swap(basis[j], basis[len]);
                break;
            }
        }
        if (basis[len] >> i & 1) {
            for (int j = 1; j <= n; j++) {
                if (j != len && (basis[j] >> i & 1)) {
                    basis[j] ^= basis[len];
                }
            }
            len++;
        }
    }
    len--;
}

void solve() {
    cin >> n;
    A = 0, B = 0;
    rep(i, 1, n + 1) cin >> a[i];

    rep(i, 1, n + 1) cin >> b[i];
    rep(i, 1, n + 1) {
        basis[i] = a[i] ^ b[i];
        A ^= a[i];
        B ^= b[i];
    }
    rep(i, n + 1, BIT) basis[i] = 0; // 注意归0

    compute();

    int dif = -1, index = 1;
    for (int i = BIT; i >= 0; i--) {
        if ((A >> i & 1) == (B >> i & 1)) {
            if (A >> i & 1) {
                A ^= basis[index];
                B ^= basis[index];
            }
        } else {
            if (B >> i & 1) swap(A, B);
            dif = i;
            if (basis[index] >> i & 1) index++;
            break;
        }
        if (basis[index] >> i & 1) index++;
    }

    for (int i = dif - 1; i >= 0; i--) {
        if ((A >> i & 1) && (basis[index] >> i & 1)) {
            A ^= basis[index];
            B ^= basis[index];
        }
        if (basis[index] >> i & 1) index++;
    }
    cout << max(A, B) << endl;
}

signed main() {
    IOS;
    int t = 1;
    cin >> t;
    while (t--) solve();
    return 0;
}
```

