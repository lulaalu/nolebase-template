---
归类:
  - "[[图论]]"
创建时间: 2025-01-17T17:01:00
---
[[最小生成树]]算法之一，使用[[并查集]]实现。

```cpp
/*
 *@Author: lulaalu
 *@Date: 2025-01-17 17:19:10
 *@Application: 算法实现
 *@Difficulty: 普及/提高−
 *@Website: https://www.luogu.com.cn/problem/P3366
 */
#include <bits/stdc++.h>
using namespace std;

// 定义边结构体，包含起点u，终点v和权重w
struct edge
{
    int u, v, w;
    // 重载小于运算符，用于排序，按权重升序排列
    bool operator<(const edge &e) const
    {
        return w < e.w;
    }
} e[200010]; // 存储边的数组

// 并查集数组，用于记录每个节点的父节点
int fa[5010];

// 并查集查找函数，带路径压缩
int Find(int x)
{
    // 如果当前节点的父节点是它自己，返回它自己
    // 否则递归查找其父节点，并进行路径压缩
    return fa[x] == x ? x : fa[x] = Find(fa[x]);
}

int main()
{
    // 初始化并查集，每个节点的父节点初始化为自己
    iota(fa, fa + 5010, 0);

    int n, m;
    // 读取节点数n和边数m
    cin >> n >> m;

    // 读取每条边的信息
    for (int i = 0; i < m; i++)
    {
        cin >> e[i].u >> e[i].v >> e[i].w;
    }

    // 对边按权重进行排序
    sort(e, e + m);

    // 初始化最小生成树的总权重和边数计数器
    int ans = 0;
    int cnt = 0;

    // 遍历排序后的边数组，构建最小生成树
    for (int i = 0; i < m && cnt < n - 1; i++)
    {
        int u = e[i].u, v = e[i].v;
        int fu = Find(u), fv = Find(v);

        // 如果两个节点不在同一个集合，则合并它们
        if (fu != fv)
        {
            fa[fu] = fv;
            ans += e[i].w; // 累加边权重到总权重
            cnt++;         // 增加已选边数
        }
    }

    // 如果选中的边数小于n-1，说明图不连通，输出"orz"
    if (cnt < n - 1)
        cout << "orz";
    else
        // 否则输出最小生成树的总权重
        cout << ans;

    return 0;
}

```
