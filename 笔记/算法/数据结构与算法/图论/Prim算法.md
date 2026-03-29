---
归类:
  - "[[图论]]"
创建时间: 2025-01-17T17:31:00
---
[[最小生成树]]算法之一，使用[[堆优化]]实现。

```cpp
/*
 *@Author: lulaalu
 *@Date: 2025-01-17 17:41:55
 *@Application: 算法实现
 *@Difficulty: 普及/提高−
 *@Website: https://www.luogu.com.cn/problem/P3366
 */
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
const int N = 4e5 + 10;

// 定义邻接表的头节点数组、下一条边的索引数组、目标节点数组以及边的权重数组
int Head[N], Next[N], To[N], W[N], cnt = 1;

// 添加边到邻接表
void add(int u, int v, int w)
{
    Next[cnt] = Head[u]; // 当前边的下一条边是头节点指向的边
    To[cnt] = v;         // 当前边的目标节点是v
    W[cnt] = w;          // 当前边的权重是w
    Head[u] = cnt++;     // 头节点指向当前边，并将边索引加1
}

bool vis[N]; // 访问标记数组，记录节点是否已被访问

// 使用优先队列存储待处理的边，按照边的权重从小到大排序
priority_queue<array<int, 2>, vector<array<int, 2>>, greater<array<int, 2>>> pq;

signed main()
{
    fill_n(vis, N, 0); // 初始化访问标记数组为false
    int n, m;
    cin >> n >> m; // 读取节点数和边数
    for (int i = 0; i < m; i++)
    {
        int u, v, w;
        cin >> u >> v >> w; // 读取每条边的起点、终点和权重
        add(u, v, w);       // 添加边到邻接表
        add(v, u, w);       // 添加反向边到邻接表（无向图）
    }

    int ans = 0;   // 最小生成树的总权重
    int ecnt = -1; // 记录加入生成树的边数，初始值为-1

    pq.push({0, 1});                    // 从节点1开始，初始权重为0
    while (!pq.empty() && ecnt < n - 1) // 当优先队列不为空且边数不足n-1时循环
    {
        auto [w, v] = pq.top(); // 取出权重最小的边
        pq.pop();

        if (vis[v]) // 如果目标节点已被访问，跳过
            continue;
        vis[v] = true; // 标记目标节点为已访问
        ans += w;      // 累加权重到总权重
        ecnt++;        // 边数加1

        for (int e = Head[v]; e; e = Next[e]) // 遍历目标节点的所有邻接边
        {
            int vv = To[e];      // 获取邻接边的目标节点
            pq.push({W[e], vv}); // 将邻接边加入优先队列
        }
    }

    if (ecnt == n - 1) // 如果加入生成树的边数为n-1，输出总权重
        cout << ans << endl;
    else // 否则，输出"orz"
        cout << "orz" << endl;
    return 0;
}

```
