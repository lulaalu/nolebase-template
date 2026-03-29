---
codeforces难度: "1500"
创建时间: 2024-09-02T17:36:00
考点:
  - "[[../../../数据结构与算法/数学/GCD]]"
  - "[[../../../数据结构与算法/数学/容斥]]"
归类: []
题目链接: https://codeforces.com/problemset/problem/803/F
tags:
  - codeforces
---
原题：[F. Coprime Subsequences](https://codeforces.com/problemset/problem/803/F)
# 题意

给一个序列求好的子序列的数量

好的子序列就是序列的[[../../../数据结构与算法/数学/GCD]]为1

数字相同但索引不同也被认为是不同的子序列
# 思路

以$[1,2,3,6,6]$为例，gcd为6的序列是6及6的倍数，$2^2-1$ ，三个

gcd为3的序列可以根据3和3的倍数算出，但是单纯用数量算的 $gcd$ 可能为 $3$ 也可能为 $6$

由[[../../../数据结构与算法/数学/容斥]]的思路，用 $dp[i]$ 表示 $gcd$ 为 $i$ 的序列数量，$num[i]$ 表示 $i$ 的倍数的数字数量

$dp[3]=2^{num[3]}-1-dp[6]$

通过递推，最后的答案 $dp[1]=2^{n}-1-dp[2]-dp[3]-\cdots$

# 代码

```CPP
#define rep(i, j, k) for (int i = (j); i < (k); i++)
#define vi vector<int>
const int MOD = 1e9 + 7;
const int N = 1e5 + 5;

int dp[N]; // dp[i]表示gcd为i的数量
int cnt[N];
int ksm(int a, int b) {
    int res = 1;
    while (b) {
        if (b & 1) res = res * a % MOD;
        a = a * a % MOD;
        b >>= 1;
    }
    return res;
}

signed main() {
    IOS;
    int n, tmp;
    cin >> n;
    rep(i, 0, N) {
        dp[i] = 0;
        cnt[i] = 0;
    }
    rep(i, 0, n) {
        cin >> tmp;
        cnt[tmp]++;
    }
    for (int i = N - 1; i >= 1; i--) {
        // 大于等于i且是i的倍数的数的数量
        int biggercnt = 0;
        int sum = 0;
        for (int j = 1; j * i < N; j++) {
            biggercnt += cnt[j * i];
            (sum += dp[j * i]) %= MOD;
        }
        int ans = ksm(2, biggercnt) - 1;
        dp[i] = (ans - sum + MOD) % MOD;
    }
    cout << dp[1];
    return 0;
}
```

