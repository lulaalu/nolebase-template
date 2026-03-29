---
leetcode难度: 困难
创建时间: 2024-09-02T17:36:00
考点:
  - "[[../../../数据结构与算法/dp]]"
归类:
  - "[[leetcode]]"
---
原题： [920. 播放列表的数量](https://leetcode.cn/problems/number-of-music-playlists/)
# 题意

有 $n$ 首不同的歌，计划听 $goal$ 首，但是要间隔 $k$ 首歌才能播放同一首歌

要求每首歌至少播放一次

求有几种播放顺序

- `0 <= k < n <= goal <= 100`
# 思路

1. 第一步

先只考虑 $n$ 首不同的歌，计划听 $goal$ 首

那么方法数就是 $n^{goal}$ 

2. 第二步

如果间隔 $k$ 首，则前 $k$ 首一定是不同的歌

前 $k$ 个排列有 $A_{n}^{k}$ 

第 $k+1$ 位置有 $n-k$ 种选择，往后的每个位置都有 $n-k$ 中

总数就是 $A_{n}^{k}*(n-k)^{goal-k}$  种

可以表示为 $f[n][goal][k]$ ，表示用 $n$ 首歌达到 $goal$ 的长度，并且有 $k$ 的限制

3. 第三步

如果每首歌都要播放一次，可以通过 $f$ 和[[../../../数据结构与算法/数学/容斥]]

假定最后一首歌我们不使用，那么现在的方法数是 $f[n-1][goal][k]$ 

由于歌曲顺序不影响 $f$ ，任意一首歌不使用的方法数都相同

那么根据容斥原理我们可以很容易得到

答案=总数-没有第一首歌-没2-没3-....
+(没1∩没2)+没1∩没3+....
-(没1∩没2∩没3)-...

$$ans=f[n][goal][k]-C_{n}^{1}f[n-1][goal][k]+...$$

也可以写成$$ans=C_n^0 f[n][goal][k]-C_{n}^{1}f[n-1][goal][k]+...$$
也就是 $$ans=\sum_{i=0}^{n}(-1)^{i}C_{n}^{i}f[n-i][goal][k],i<n-k$$
公式化简得
$$ans=\sum_{i=0}^{n-k-1}(-1)^{i}\frac{n!(n-i-k)^{goal-k}}{i!(n-i-k)!}$$

# 代码

```CPP
const int N = 110;
const int MOD = 1e9 + 7;
#define vi vector<int>
#define ll long long
ll ksm(ll a, ll b) {
    ll ans = 1;
    while (b) {
        if (b & 1) ans = ans * a % MOD;
        a = a * a % MOD;
        b >>= 1;
    }
    return ans;
}
ll inv[N];
ll fa[N];
ll f(int n) {
    ll ans = 1;
    for (int i = 1; i <= n; i++) ans = ans * i % MOD;
    return ans;
}

void lr(int n) {
    inv[n] = ksm(f(n), MOD - 2);
    for (int i = n - 1; i >= 0; i--) inv[i] = (inv[i + 1] * (i + 1)) % MOD;
    fa[1] = 1;
    for (int i = 2; i <= n; i++) fa[i] = (fa[i - 1] * i) % MOD;
}
class Solution {
public:
    int numMusicPlaylists(int n, int goal, int k) {
        lr(100);
        ll ans = 0;
        ll sta = -1;
        for (int i = 0; i < n - k; i++) {
            sta = -sta;
            ll t = fa[n] * ksm(n - k - i, goal - k) % MOD;
            t = t * inv[i] % MOD;
            t = t * inv[n - k - i] % MOD;
            if (sta == -1) t = t * (MOD - 1) % MOD;
            ans = (ans + t) % MOD;
        }
        return ans;
    }
};
```