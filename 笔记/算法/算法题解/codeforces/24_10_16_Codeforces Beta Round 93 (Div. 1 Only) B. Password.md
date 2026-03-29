---
codeforces难度: "1500"
创建时间: 2024-09-02T17:36:00
考点:
  - "[[../../../数据结构与算法/字符串/字符串]]"
  - "[[字符串哈希]]"
  - "[[../../../数据结构与算法/字符串/KMP]]"
  - "[[Z函数]]"
归类: []
题目链接: https://codeforces.com/problemset/problem/126/B
tags:
  - codeforces
---

学完Z函数，先用哈希做了一遍，再用Z函数做了一遍，然后搜其他人的题解发现用next数组也能做，就又做了一遍

# B. Password

## 题意

给一串字符串 $s$，要求找一个最长的 $t$

$t$ 既是 $s$ 的前缀串，也是后缀串，还是除了前缀后缀外的一个子串
## 哈希

前缀和后缀同时哈希，如果匹配上了就根据这个长度遍历一遍 $s$，看看能不能匹配上

注意要所有的前后缀都判一下，确保是最长的 $t$ 串

```CPP
const int N = 1e6 + 10;
const int p = 21839; // 以为卡哈希，随便找了个素数，结果是因为最大值
const int MOD = 1e9 + 7;
string s;
int n;
int hf[N]; // hf[i]=p^i
void prepare(int n) {
    hf[0] = 1;
    for (int i = 1; i <= n; i++) {
        hf[i] = (hf[i - 1] * p) % MOD;
    }
}

bool check(int sta, int hpre) {
    int hinit = hpre;
    for (int i = sta + 1; i < n - 1; i++) {
        hpre = ((hpre - s[i - sta - 1] * hf[sta]) % MOD + MOD) % MOD;
        hpre = (hpre * p % MOD) + s[i];
        if (hpre == hinit) {
            return true;
        }
    }
    return false;
}
void solveHash() {
    cin >> s;
    n = s.length();
    prepare(n);
    int ans = -1;
    int hpre = 0, haft = 0;
    for (int i = 0; i < n; i++) {
        hpre = (hpre * p + s[i]) % MOD;
        haft = (haft + (hf[i] * s[n - i - 1]) % MOD) % MOD;
        if (hpre == haft) {
            if (check(i, hpre)) {
                ans = i + 1;
            }
        }
    }
    if (ans != -1)
        cout << s.substr(0, ans);
    else
        cout << "Just a legend";
}
```

## [[Z函数]]

这个其实只用 $Z$ 数组，不用 $e$ 数组，我推荐看[左程云的讲解](https://www.bilibili.com/video/BV1YH4y1h7MU/?spm_id_from=333.999.0.0&vd_source=10b3ee1feea99fbdd76960a61a32f71e)

从后往前看，如果 $i+z[i]==n$ 说明 $s[i\dots n-1]$ 是后缀也是前缀

接下来就是找有没有比 $z[i]$ 更大的 $z$ ，如果有，说明这个更大的 $z$ 可以包含住这个后缀

```CPP
const int N = 1e6 + 10;
int z[N];
int e[N];
string s, t;
void zArray(string &s, int n) {
    z[0] = n;
    for (int i = 1, c = 1, r = 1, len; i < n; i++) {
        len = r > i ? min(r - i, z[i - c]) : 0;
        while (i + len < n && s[i + len] == s[len]) len++;
        if (i + len > r) {
            r = i + len;
            c = i;
        }
        z[i] = len;
    }
}

void solveZ() {
    cin >> s;
    t = s;
    int n = s.length(), m = n;
    zArray(t, m);
    int ans = -1;

    set<int> match;
    // match中存储能与前缀匹配的后缀长度
    // 如果在match有比某个z[i]小的值x，说明这个x是答案
    for (int i = n - 1; i >= 1; i--) {
        if (match.size() > 0) {
            auto it = match.upper_bound(z[i]);
            // *it是大于z[i]的
            // 所以it往前的会小于等于z[i]
            if (it != match.begin()) {
                it--;
                ans = max(*it, ans);
            }
        }
        if (i + z[i] == n) match.insert(z[i]);
    }

    if (ans != -1) {
        cout << s.substr(0, ans);
    } else {
        cout << "Just a legend";
    }
}
```

## [[../../../数据结构与算法/字符串/KMP]]（用next数组）

$next[i]$ 表示从$s[0\dots i]$ 的最长相等前后缀

先把所有的 $next$ 值存起来，然后 $x=next[n-1]$

如果该 $x$ 出现过，就说明这是答案

如果该 $x$ 没出现过，可能还存在较短的前后缀，也就是在 $next[n-1]$ 这个长度内有重复结构

将 $x=next[x-1]$，之所以是 $x-1$，因为 $x$ 表示的是字符串长度，对应下来的长度对应的 $next$ 数组应该要减一位（如果你的 $next$ 数组下标从 $0$ 开始

```CPP
const int N = 1e6 + 10;
int nxt[N];
string t;
void buildNext(string &t) {
    nxt[0] = 0;
    int i = 1;
    int nlen = 0;
    int tlen = t.length();
    while (i < tlen) {
        if (t[nlen] == t[i]) {
            nlen++;
            nxt[i] = nlen;
            i++;
        } else {
            if (nlen == 0) {
                nxt[i] = 0;
                i++;
            } else
                nlen = nxt[nlen - 1];
        }
    }
}

bool vis[N];
void solveKMP() {
    cin >> t;
    buildNext(t);
    int n = t.length();
    fill_n(vis, n + 5, false);
    for (int i = 1; i < n - 1; i++) {
        vis[nxt[i]] = 1;
    }
    int x = nxt[n - 1];
    while (!vis[x] && x != 0) {
        x = nxt[x - 1];
    }
    if (x != 0)
        cout << t.substr(0, x);
    else
        cout << "Just a legend";
}
```