# Dijkstra算法

## 简单版

### 须知

首先用几个数组表示需要的状态：

- dis[] 	     表示距离从初始点到对应点的距离，初始点置为0，其他置为无穷
- graph[]\[]      邻接矩阵，记录两点间连线的权重，可以记录无向图和有向图
- check[]         判断当前点是否被记录



### 算法思路：

假定a为起点，每次选距离a最近的点然后更新。

![image-20240428172502767](https://img2023.cnblogs.com/blog/3424847/202410/3424847-20241030224042769-578066320.png)

![image-20240428172924153](https://img2023.cnblogs.com/blog/3424847/202410/3424847-20241030224042238-995380554.png)

先从a到b和c，发现到b的距离最小，锁定dis[b]，

再从b到其他点，如图中第二次，发现从b到c为当前状态下最小的，所以记录

不断重复

### 代码

以https://www.luogu.com.cn/problem/P1339为例，AC代码如下

```C++
#define rep(i,j,k) for(i=(j);i<(k);i++)
const int N = 8000 + 10;
int ii, jj;

int n, m, s, t;
int u, v, w;
int graph[N][N];//邻接矩阵
int dis[N],check[N];//距离，状态
int main() {
    cin >> n >> m >> s >> t;
	//初始化，所有dis为无穷
    rep(ii, 0, n+1) {
        dis[ii] = INT_MAX;
        //graph全为0，表示无连接
        rep(jj, 0, n+1)
            graph[ii][jj] = 0;
    }
    
	//初始化，由于无向图，所以graph[u][v]，graph[V][u]
    rep(ii, 0, m) {
        cin >> u >> v >> w;
        graph[u][v] = w;
        graph[v][u] = w;
    }
    //起点定为0
    dis[s] = 0;
    
    for (int i = 1; i <= n; i++) {
        //先遍历所有点，找出最小的
        int minn = INT_MAX, mini = 0;
        //该过程，即第i次寻找最小的dis，还要注意不能是已经选过的
        for (int j = 1; j <= n; j++) 
            if (!check[j] && dis[j] < minn) 
                minn = dis[j], mini = j;
		
        //将选中的置为1
        check[mini] = 1;

        //枚举邻边
        //从当前的最小位置向每个点连接，如果可以连接上，与原本的距离作对比
        for (int j = 1; j <= n; j++) {
            //从mini 到 i有连线，判断
            if (graph[mini][j] > 0) {
                dis[j] = min(dis[j], graph[mini][j] + minn);
            }
        }
        
    }
    cout << dis[t] << endl;
    return 0;
}
```

## 堆优化

### 学习原因

在找到最小距离时，枚举了很多并没有改变的点，额外提高了时间，所以用堆优化，把已经更改的点存入**优先队列**

之所以用优先队列，我们用距离d数组进行排序，最前面的就是我们要的那个mini。

```C++
int minn = INT_MAX, mini = 0;
for (int j = 1; j <= n; j++) 
    if (!check[j] && dis[j] < minn) 
        minn = dis[j], mini = j;
```

这时可以用一个优先队列来存储每次更新的点和路线长度，详情请参考：https://www.bilibili.com/video/BV1Ya411L7gb/?spm_id_from=333.999.0.0&vd_source=10b3ee1feea99fbdd76960a61a32f71e

### 具体实现

同样为上面简单版题目的ac代码

```C++
typedef pair<int, int> pii;
#define rep(i,j,k) for(i=(j);i<(k);i++)

const int N = 8000 + 10;
int ii, jj;

int n, m, s, t;
int u, v, w;
int graph[N][N];
int dis[N], check[N];
priority_queue<pii>pq;

int main() {
    cin >> n >> m >> s >> t;
    rep(ii, 0, n + 1) {
        dis[ii] = INT_MAX;
        rep(jj, 0, n + 1)
            graph[ii][jj] = 0;
    }

    rep(ii, 0, m) {
        cin >> u >> v >> w;
        graph[u][v] = w;
        graph[v][u] = w;
    }
    dis[s] = 0;

    //先将起点push进pq中
    pq.push({ 0,s });

    while(q.size()){
        //找到最小的顶点
        int mini = q.top().second;
        q.pop();
		//每次取top，因为此时的top表示的是从起点到达u点的最短长度
        //那么只需要从最短长度开始遍历其他点，就可以极大地降低时间复杂度
        
        if (vis[mini])continue;
        vis[mini] = 1;

        rep(j, 1, n + 1) {
            if (g[mini][j] > 0) {
                if (d[j] > d[mini] + g[mini][j]) {
                    d[j] = d[mini] + g[mini][j];
                    q.push({ -d[j],j });
                }
            }
        }
    }
    cout << dis[t] << endl;
    return 0;
}
```

**注意**：pq默认为大跟堆，如果用小跟堆会麻烦一点，所以将路径长度取-1再存储。路径只起到标识作用，不对结果产生影响。