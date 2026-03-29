---
创建时间: 2024_11_13
考点:
  - "[[Dijsktra]]"
  - "[[最短路]]"
归类:
  - "[[图论]]"
---
[dijsktra5min视频讲解](https://www.bilibili.com/video/BV1uT4y1p7Jy/?spm_id_from=333.337.search-card.all.click)

# 代码及细节([[链式前向星]]+[[堆优化]])

[模板测试链接](https://www.luogu.com.cn/problem/P4779)


```CPP
#include <bits/stdc++.h>
using namespace std;

/*
要求：求s到其他点的最短路径
1. dis[v] 表示s到v点的最短距离
2. 每次找出dis的最小值的点（堆优化，<距离,点>堆顶为距离最小）
3. 从这个点出发走到其他点，更新dis
*/

const int N = 2e5 + 10;
const int MAXINT = 2147483647;

// 存储最小距离和是否最小
int dis[N], check[N];

/*
1. head 数组，head[i] 表示 i 点的头边
2. next 数组，next[i] 表示第 i 条边的下一条边的编号
3. to 数组，to[i] 表示第 i 条边去往的点
4. cnt，表示边的编号
*/
int Head[N], Next[N], To[N];
int Weight[N];
int cnt = 1;

void addEdge(int u, int v, int w)
{
    // 注意此时cnt编号为新边的编号，cnt从1开始
    Next[cnt] = Head[u]; // 把新边插在Head和Head[u]之间
    To[cnt] = v;         // 新边指向v点
    Weight[cnt] = w;
    Head[u] = cnt++; // u点的头边是cnt，然后cnt++
}

// 顶点个数 路径数 和起始点
int n, m, s;

int main()
{
    cin >> n >> m >> s;
    // 初始化dis为无穷大
    for (int i = 1; i <= n; i++)
    {
        dis[i] = MAXINT;
        check[i] = 0;
    }
    // 读入数据，初始化邻接矩阵
    for (int i = 1; i <= m; i++)
    {
        int a, b, c;
        cin >> a >> b >> c;
        addEdge(a, b, c);
    }

    /*开始处理*/
    // 设置起点的距离
    dis[s] = 0;
    // 遍历所有点

    // 堆优化
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;
    pq.push({0, s});
    while (pq.size())
    {
        // 找出最小的dis，然后从最小的开始处理
        int minn = pq.top().first, mini = pq.top().second; // 分别是最小dis和最小dis的下标
        pq.pop();

        if (check[mini])
            continue;
        // 把找到的最小值标记
        check[mini] = 1;

        // 在找到最小的值后，从该点出发
        for (int e = Head[mini]; e > 0; e = Next[e])
        {
            int v = To[e];
            // 如果这maxi和j相互连接，那么进行比较
            // 判断dis[j]和dis[maxi]+graph[][]的距离
            if (Weight[e] + minn < dis[v])
            {
                dis[v] = Weight[e] + minn;
                pq.push({dis[v], v});
            }
        }
    }

    for (int i = 1; i <= n; i++)
        cout << dis[i] << " ";
    return 0;
}
```

# 好题

https://codeforces.com/contest/2059/problem/D

```cpp
/*
 *@Author: lulaalu
 *@Date: 2025-02-03 20:14:15
 *@Application:
 *@Difficulty:
 *@Website:https://codeforces.com/contest/2059/problem/D
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
using i128 = __int128_t;
using u128 = __uint128_t;
using f32 = float;
using f64 = double;
using f80 = long double;
using CI = const i32;
using CL = const i64;
/*
使用dij算法计算从初始的(s1,s2)状态到所有的状态的最短路径
*/

const i32 INF = 1e9 + 1;
using a3 = array<i32, 3>;

CI N = 1010;
i32 dis[N][N];

vector<i32> a[N];
vector<i32> b[N];
vector<i32> sort_b[N];

void solve()
{

    i32 n, m1, m2, s1, s2;
    cin >> n >> s1 >> s2;

    for (i32 i = 1; i <= n; i++)
    {
        a[i].clear();
        b[i].clear();
        sort_b[i].clear();
    }

    cin >> m1;
    for (i32 i = 0; i < m1; i++)
    {
        i32 u, v;
        cin >> u >> v;
        a[u].push_back(v);
        a[v].push_back(u);
    }

    cin >> m2;
    for (i32 i = 0; i < m2; i++)
    {
        i32 u, v;
        cin >> u >> v;
        b[u].push_back(v);
        b[v].push_back(u);
    }

    for (i32 i = 0; i <= n; i++)
    {
        fill_n(dis[i], n + 1, INF);
        sort_b[i] = b[i];
        sort(all(sort_b[i]));
    }
    priority_queue<a3, vector<a3>, greater<a3>> pq;
    dis[s1][s2] = 0;
    pq.push({0, s1, s2});
    while (!pq.empty())
    {
        auto [d, v1, v2] = pq.top();
        pq.pop();

        if (dis[v1][v2] > d)
            continue;

        for (auto u1 : a[v1])
        {
            for (auto u2 : b[v2])
            {
                i32 cost = abs(u1 - u2);
                if (dis[u1][u2] > d + cost)
                {
                    dis[u1][u2] = d + cost;
                    pq.push({d + cost, u1, u2});
                }
            }
        }
    }

    // 找共联的点
    i32 ans = INF;
    for (i32 i = 1; i <= n; i++)
    {
        for (auto va : a[i])
        {
            auto it = lower_bound(all(sort_b[i]), va);
            if (it != sort_b[i].end() && *it == va)
            {
                ans = min(dis[va][*it], ans);
                break;
            }
        }
    }
    if (ans == INF)
        cout << "-1\n";
    else
        cout << ans << endl;
}

signed main()
{
    IOSIO;
    i32 _ = 1;
    cin >> _;
    while (_--)
        solve();

    return 0;
}
```