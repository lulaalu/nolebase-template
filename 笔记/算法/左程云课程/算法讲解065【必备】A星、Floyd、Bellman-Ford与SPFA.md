---
creation date group: "[[../../../02 Project/日记周记/日记/2024_12_13]]"
content:
  - "[[算法讲解065【必备】A星、Floyd、Bellman-Ford与SPFA]]"
---
- [算法讲解065【必备】A星、Floyd、Bellman-Ford与SPFA PPT](D:\L.Document\KNOWLEDGE\VScode\learn\左程云\左程云算法资料\algorithm-journey-main\ppt\算法讲解065【必备】A星、Floyd、Bellman-Ford与SPFA.pptx)
- [算法讲解065【必备】A星、Floyd、Bellman-Ford与SPFA](https://www.bilibili.com/video/BV1t94y187zW/?spm_id_from=333.999.0.0&vd_source=10b3ee1feea99fbdd76960a61a32f71e)

# A*

目的：从原点怎么最快的到达目标点，也就是点到点的[[最短路]]

特点
- 与dij相比，dij是原点到当前点的距离排序
- A星是原点到当前点加到目标点的估计距离

比如这种图，dij用堆优化没用，从G开始，还是会把所有点跑一遍，比bfs还多一个log的复杂度

![|300](assets/Pasted%20image%2020241213155540.png)

而A星，如下图，distance是距离点G的距离，小跟堆中按到G的距离+到U的估计距离

这里的估计距离用的是曼哈顿距离

![](assets/Pasted%20image%2020241213155903.png)

预估终点距离经常选择：
- 曼哈顿距离
- 欧式距离
- 对角线距离


# Bellman-Ford

解决**单源最短路径算法**，可以有**负边**

## 松弛操作

假设源点为 $A$ ，从 $A$ 到任意点 $F$ 的最短距离为 $distance[F]$
假设从点 $P$ 出发某条边，去往点 $S$ ，边权为 $W$
如果发现，$distance[P]+W<distance[S]$ ，也就是通过该边可以让$distance[S]$变小 
那么就说，$P$ 出发的这条边对点 $S$ 进行了松弛操作

比如![](assets/Pasted%20image%2020241213162200.png)

从 $a$ 出发，按固定顺序松弛，比如按边<b,c><c,d><d,a><a,c><a,b>的顺序遍历，即这个顺序

![](assets/Pasted%20image%2020241213162443.png)

```
dis  a  b  c  d
     0  ?  ?  ?
第一轮松弛，按照边的顺序：
dis  a  b  c  d
     0  5 -1  6
     
有改变，那么第二轮松弛，仍旧按照原本边的顺序
dis  a  b  c  d
     0  2 -1  4
有改变，那么第三轮松弛，仍旧按照原本边的顺序，没变化，结束
```

轮数必然小于等于 $n-1$ ，$n$ 为点的数量

复杂度 $O(M*N)$，$N$ 个点，$M$ 条边

## 重要推广

可以判断从某一点出发是否遇到负环，如果遇到，无休止的进行松弛操作

## 思想的应用

[787. K 站中转内最便宜的航班](https://leetcode.cn/problems/cheapest-flights-within-k-stops/)

```CPP
int findCheapestPrice(int n, vector<vector<int>> &flights, int src, int dst, int k)
{
	vector<int> dis(n, 1e9);
	dis[src] = 0;
	for (int t = 0; t <= k; t++)
	{
		vector<int> tmp(dis);
		for (auto f : flights)
		{
			int from = f[0], to = f[1], price = f[2];
			tmp[to] = min(tmp[to], dis[from] + price);
		}
		dis = tmp;
	}
	return dis[dst] == 1e9 ? -1 : dis[dst];
}
```
# SPFA

松弛时，有些边是不需要反复遍历的，只需要处理那些被松弛过的点就行

用一个队列和一个bool数组

BeLlman-Ford+SPFA优化的用途
1) 适用于小图
2) 解决有负边（没有负环）的图的单源最短路径问题
3) 可以判断从某个点出发是否能遇到负环，**如果想判断整张有向图有没有负环，需要设置虚拟源点**
4) 并行计算时会有很大优势，因为每一轮多点判断松弛操作是相互独立的，可以交给**多线程**处理

## 例题

```cpp
#include <bits/stdc++.h>
using namespace std;

/*
 *@Author: lulaalu
 *@Date: 2024-12-06 16:29:26
 *@Difficulty: 普及/提高−
 *@Website: https://www.luogu.com.cn/problem/P3385
 */

using i64 = long long;
using i32 = int;

/*链式前向星*/
const i32 M = 6e3 + 10;
i32 Head[M], Next[M], To[M], W[M], cnt = 1;
void Add(i32 u, i32 v, i32 w)
{
    Next[cnt] = Head[u];
    To[cnt] = v;
    W[cnt] = w;
    Head[u] = cnt++;
}

const i32 N = 2e3 + 10;
i32 dis[N];
bool vis[N];
i32 updateCnt[N];
queue<i32> q;
bool spfa(i32 n)
{
    dis[1] = 0;
    updateCnt[1]++;
    q.push(1);
    vis[1] = 1;
    while (q.size())
    {
        i32 u = q.front();
        q.pop();
        vis[u] = 0;
        for (i32 e = Head[u], v, w; e; e = Next[e])
        {
            v = To[e];
            w = W[e];
            if (dis[u] + w < dis[v])
            {
                dis[v] = dis[u] + w;
                if (!vis[v])
                {
                    updateCnt[v]++;
                    if (updateCnt[v] >= n)
                        return true;
                    q.push(v);
                    vis[v] = 1;
                }
            }
        }
    }
    return false;
}

void init(i32 n)
{
    cnt = 1;
    q = queue<i32>();
    fill_n(Head, n + 10, 0);
    fill_n(vis, n + 10, 0);
    fill_n(dis, n + 10, 1e9);
    fill_n(updateCnt, n + 10, 0);
}
signed main()
{
    i32 t;
    cin >> t;
    while (t--)
    {
        i32 n, m;
        cin >> n >> m;
        init(n);
        for (i32 i = 0; i < m; i++)
        {
            i32 u, v, w;
            cin >> u >> v >> w;
            if (w < 0)
                Add(u, v, w);
            else
            {
                Add(u, v, w);
                Add(v, u, w);
            }
        }
        if (spfa(n))
            cout << "YES\n";
        else
            cout << "NO\n";
    }
    return 0;
}
```