---
创建时间: 2025-05-26T15:07:00
---
比赛时间：25-05-24

# A

## mean

数组的min和max相加是偶数

可以删数，问最少删几次

## way

最大最小，删其他值没用，所以排个序，从最大或最小一个一个删

由于数据原因，直接两重for循环遍历，就当i前和j后的都删了

## code

```cpp
void solve()
{
    i64 n;
    cin >> n;
    vector<i64> a(n);
    for (auto &x : a)
        cin >> x;
    sort(all(a));
    i64 ans = 1e9;
    for (i32 i = 0; i < n; i++)
    {
        for (i32 j = n - 1; j >= i; j--)
        {
            if ((a[i] + a[j]) % 2 == 0)
            {
                ans = min(ans, n - (j - i + 1));
            }
        }
    }
    cout << ans << endl;
}
```

# B

## mean

对于一个正常闭合括号序列，删除其中任意一个左括号和任意一个右括号，能否使序列出错

## way

手写几个，比如`((()))`，`(()()())`

我的直觉是，必须是最外层有对应的括号把整体包住，这样中间无论删除哪两个都能保证不会失衡

按照官方题解，就是说删除第一个和最后一个对整体平衡度影响最小，所以检测中间部分是否符合要求

实际流程差不多，我是，遇见`(`平衡度+1，遇到`)`平衡度-1，必须保证中间部分没有平衡度为0，知道最后一个`)`

## code

```cpp
string s;
void solve()
{
    cin >> s;
    i32 n = s.length();
    i32 cnt = 1;
    for (int i = 1; i < n; i++)
    {
        if (s[i] == '(')
            cnt++;
        else
            cnt--;
        if (cnt == 0 && i != n - 1)
        {
            cout << "YES\n";
            return;
        }
    }
    cout << "NO\n";
}
```

# C

## mean

驾驶飞机通过n个目标（门），有n次机会让飞机上升1m或保持不变

- 门高已知
- 部分操作已知，未知的为-1，高度不变的为0，升高的为1

求能否通过所有操作门，并给出操作序列

## way

- 直接考虑操作序列有点抽象，考虑不行的情况

从高度0开始，根据操作序列$step$可以知道下一次能通过的高度，如果$step[1]=-1$（下标从1开始），那么能通过$[0,1]$，如果$step[2]=-1$，能通过$[0,2]$，如果$step[3]=1$，能通过$[1,3]$

根据$step$然后将能通过的范围与题给的门高取**交集**，如果取的交集为空，说明通不过

能到达末尾，理论上就能过了

- 然后考虑操作序列

要么顺着Low往上爬，要么顺着High往上爬，但是由于操作序列的不确定性，可能前一个门是$[2,6]$，下一个门是$[4,4]$，不可能预知到后面要爬两格，就提前升高

所以要从后往前降

如倒数第二个样例

- $H:[0,1,2,2,3,3,3,4]$
- $L:[0,0,1,1,1,1,2,4]$ 

从后往前的操作允许有超过1m的高差

## code

从高降和从低降都写了一下

```cpp
vector<i64> step;
vector<i64> L, H;

void solve()
{
    i64 n;
    cin >> n;
    step.resize(n + 1);
    for (i32 i = 1; i <= n; i++)
        cin >> step[i];
    L.resize(n + 1);
    H.resize(n + 1);
    L[0] = 0;
    H[0] = 0;
    for (i32 i = 1; i <= n; i++)
    {
        cin >> L[i] >> H[i];
    }
    for (i64 i = 1; i <= n; i++)
    {
        if (step[i] == 0)
        {
            L[i] = max(L[i - 1], L[i]);
            H[i] = min(H[i - 1], H[i]);
        }
        else if (step[i] == 1)
        {
            L[i] = max(L[i], L[i - 1] + 1);
            H[i] = min(H[i], H[i - 1] + 1);
        }
        else
        {
            L[i] = max(L[i], L[i - 1]);
            H[i] = min(H[i], H[i - 1] + 1);
        }
        if (L[i] > H[i])
        {
            cout << "-1\n";
            return;
        }
    }
    // i64 np = L[n];
    // for (i32 i = n; i >= 1; i--)
    // {
    //     if (step[i] == 1)
    //     {
    //         np--;
    //     }
    //     else if (step[i] == -1)
    //     {
    //         if (np - 1 >= L[i - 1])
    //         {
    //             step[i] = 1;
    //             np--;
    //         }
    //         else
    //         {
    //             step[i] = 0;
    //         }
    //     }
    // }
    i64 np = H[n];
    for (i32 i = n; i >= 1; i--)
    {
        if (step[i] == 1)
        {
            np--;
        }
        else if (step[i] == -1)
        {
            if (np > H[i - 1])
            {
                step[i] = 1;
                np--;
            }
            else
            {
                step[i] = 0;
            }
        }
    }
    for (i32 i = 1; i <= n; i++)
        cout << step[i] << " ";
    cout << endl;
}
```


# D

## mean

无环，有向图，每条边有对应的限制，要求身上携带的电池数在限制及以上才能通行，电池从每个点取

要求从点1到点n携带的最少电池数，如果不行就报告

## way

- 先不考虑最少的限制

从前往后跳，那从1开始跳，遍历与1连接的边，只有需求电池数比1号点有的电池数小，才能经过这条边

经过这条边到达的点是可达的，标记vis

然后带上当前所有的电池，保证可以尽量走每一条边

- 然后考虑最小

二分边限制

（其实应该先想到二分思想，然后带上所有电池埋头走，但是我确实是先想到怎么走的，一开始想用mx数组记录能带的最多的电池，用now数组，根据经过的边动态从mx里取，后面hack了，才想到二分）
## code

```cpp
i64 n, m;
i64 energy[N];
array<i64, 3> input[N];
i64 now[N];
bool vis[N];

vector<array<i64, 2>> g[N];
void init(i64 n)
{
    for (i64 i = 0; i < n + 1; i++)
        g[i].clear();
    fill_n(now, n + 1, 0);
    fill_n(vis, n + 1, 0);
}

bool check(i64 mid)
{
    init(n);
    for (i64 i = 0; i < m; i++)
    {
        if (input[i][2] > mid)
            break;
        g[input[i][0]].push_back({input[i][1], input[i][2]});
    }
    vis[1] = true;
    now[1] = energy[1];
    for (i64 i = 1; i <= n; i++)
    {
        if (!vis[i])
            continue;
        for (auto [y, w] : g[i])
        {
            // 能到达y点
            if (w <= now[i])
            {
                // 能到达，所以把y标记能访问
                vis[y] = true;
                // y点的当前能量
                now[y] = max(now[y], energy[y] + now[i]);
                // printf("%lld to %lld, now[%lld] = %lld, mx[%lld] = %lld\n", i, y, y, now[y], y, mx[y]);
            }
        }
    }
    if (vis[n])
        return true;
    else
        return false;
}

void solve()
{
    cin >> n >> m;
    for (i64 i = 1; i <= n; i++)
        cin >> energy[i];
    for (i64 i = 0; i < m; i++)
    {
        cin >> input[i][0] >> input[i][1] >> input[i][2];
    }
	// 根据限制排序
    sort(input, input + m, [](const array<i64, 3> &a, const array<i64, 3> &b)
         { return a[2] < b[2]; });
    i64 l = 1, r = input[m - 1][2];
    i64 ans = -1;
    while (l <= r)
    {
        i64 mid = (l + r) >> 1;
        if (ans == mid)
            break;
        if (check(mid))
        {
            r = mid - 1;
            ans = mid;
        }
        else
            l = mid + 1;
    }

    if (ans)
        cout << ans << endl;
    else
        cout << -1 << endl;
}
```

# E

看了题解发现是二分图和欧拉路径，然后终于补了

流程很顺

1. 把1e9的数据映射到2e5
2. 记录入度出度和边
3. 检测是否联通
4. 判断是否有欧拉路径或欧拉回路
5. dfs获取路径

欧拉路径然后把每边，也就是v和p两边用多加一层数组处理，不确定二分图是否有其他方式，后面慢慢深入，至少能纯靠自己AC了

```cpp
/*
 *@Author: lulaalu
 *@Date: 2025-06-01 21:52:14
 *@Application: 欧拉路径、
 *@Difficulty:
 *@Website: https://codeforces.com/contest/2110/problem/E
 */
#include <bits/stdc++.h>

#define endl '\n'
#define IOSIO ios::sync_with_stdio(0), cin.tie(0), cout.tie(0)
#define IOSI ios::sync_with_stdio(0), cin.tie(0)
#define rep(i, j, k) for (int i = (j); i < (k); i++)
#define all(x) x.begin(), x.end()
#define sz(x) (int)(x.size())
#define debug(s, x) cerr << s << " = " << x << endl;

using namespace std;
using pii = pair<int, int>;
using i64 = long long;
using u64 = unsigned long long;
using i32 = signed;
using u32 = unsigned int;
// using i128 = __int128_t;
// using u128 = __uint128_t;
using f32 = float;
using f64 = double;
using f80 = long double;
using CI = const i32;
using CL = const i64;

CI N = 4e5 + 10;

// mapping
map<i64, i64> mpv, mpp;
i64 cntv = 0, cntp = 0; // mpv的大小 和 mpp的大小，用于把1e9范围内的点映射到2e5内

// dfs
bool vis[N];   // 边是否走过，由于是无向边，所以要记录
i64 del[2][N]; // 存储删除的边
stack<i64> ans;

i64 n = 2e5 + 5;
array<i64, 2> vp[N]; // 存储两个点
// g[0]表示v->p，g[1]表示p->v
// g[turn][i]表示mapping后的v或p值
// array<2>表示{目标点, 边id}，边id实际上就是输入的顺序，dfs时要用来获取ans
vector<array<i64, 2>> g[2][N];
i64 degree[2][N]; // 存储度，0对应v

// 判断是否联通
bool isConnected()
{
    queue<pair<i64, bool>> q;
    vector<bool> visv(cntv + 1, 0);
    vector<bool> visp(cntp + 1, 0);
    q.push({1, 0}); // 0表示v，1表示p
    while (!q.empty())
    {
        i64 u = q.front().first;
        bool flag = q.front().second;
        q.pop();
        // 如果为0，表示从0到1，即v->p
        if (!flag)
        {
            if (visp[u])
                continue;
            visp[u] = 1;
            for (int i = 0; i < g[1][u].size(); i++)
            {
                i64 v = g[1][u][i][0];
                q.push({v, 1});
            }
        }
        else
        {
            if (visv[u])
                continue;
            visv[u] = 1;
            for (int i = 0; i < g[0][u].size(); i++)
            {
                i64 v = g[0][u][i][0];
                q.push({v, 0});
            }
        }
    }
    for (int i = 1; i <= cntp; i++)
        if (!visp[i])
            return false;
    for (int i = 1; i <= cntv; i++)
        if (!visv[i])
            return false;
    return true;
}

// 找路径
void dfs(i64 u, bool turn, i64 edgeId)
{
    for (int i = del[turn][u]; i < g[turn][u].size(); i = del[turn][u])
    {
        del[turn][u] = i + 1;
        i64 v = g[turn][u][i][0];
        i64 e = g[turn][u][i][1];
        if (vis[e])
            continue;
        vis[e] = true;
        dfs(v, !turn, e);
    }
    ans.push(edgeId);
}

// 映射
void mapping()
{
    for (auto &[v, cnt] : mpv)
        cnt = ++cntv;
    for (auto &[p, cnt] : mpp)
        cnt = ++cntp;
    // cout << "----映射后的结果----" << endl;
    for (int i = 0; i < n; i++)
    {
        vp[i][0] = mpv[vp[i][0]];
        vp[i][1] = mpp[vp[i][1]];
        // cout << vp[i][0] << " " << vp[i][1] << endl;
    }
}

// clear
void init()
{
    mpv.clear(), mpp.clear();
    i64 tn = 2 * n;
    fill_n(degree[0], tn, 0);
    fill_n(degree[1], tn, 0);
    for (int i = 0; i < tn; i++)
        g[0][i].clear(), g[1][i].clear();
    fill_n(del[0], cntv + 1, 0);
    fill_n(del[1], cntp + 1, 0);
    cntp = cntv = 0;
    fill_n(vis, tn, 0);
}

void solve()
{
    init();
    cin >> n;

    // input
    for (int i = 0; i < n; i++)
    {
        cin >> vp[i][0] >> vp[i][1];
        mpv[vp[i][0]] = 1;
        mpp[vp[i][1]] = 1;
    }

    // 1e9=>2e5的映射
    mapping();

    // 记录 出度入度 和 边
    for (int i = 0; i < n; i++)
    {
        degree[0][vp[i][0]]++;
        degree[1][vp[i][1]]++;

        g[0][vp[i][0]].push_back({vp[i][1], i});
        g[1][vp[i][1]].push_back({vp[i][0], i});
    }

    // 先测试是不是是联通的
    if (!isConnected())
    {
        cout << "NO" << endl;
        return;
    }

    // 联通，接下来根据度判断是否有欧拉路径或欧拉回路
    bool ok = 1;
    int s1 = -1, s2 = -1;
    i64 odd_degree_cnt = 0;
    for (int i = 1; i <= n && ok; i++)
    {
        if (degree[0][i] % 2 == 1)
        {
            odd_degree_cnt++;
            s1 = i;
        }
        if (degree[1][i] % 2 == 1)
        {
            odd_degree_cnt++;
            s2 = i;
        }
    }

    // 如果有开头和结尾就继续，或者，如果是欧拉回路，就继续
    if (odd_degree_cnt != 0 && odd_degree_cnt != 2)
    {
        cout << "NO\n";
        return;
    }

    // 前置检查都完毕了，接下来开始dfs找路径
    if (s1 == -1 && s2 == -1)
        dfs(1, 0, g[0][1][0][1]); // 欧拉回路，从1开始，因为1一定存在
    else if (s1 != -1)
        dfs(s1, 0, g[0][s1][0][1]);
    else
        dfs(s2, 1, g[1][s2][0][1]);

    // 输出答案
    cout << "YES\n";
    ans.pop();
    while (!ans.empty())
    {
        cout << ans.top() + 1 << " ";
        ans.pop();
    }
    cout << endl;
}

signed main()
{
    IOSIO;
    i32 _ = 1;
    cin >> _;
    while (_--)
    {
        solve();
    }
    return 0;
}
```