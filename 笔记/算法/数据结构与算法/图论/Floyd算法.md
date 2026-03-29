
首先，对该算法有一个大致的了解：
- 通过动态规划的方式，按顺序对每两个点之间的最短距离进行处理
- 而这个顺序用一句话总结就是 :**依次将每个点作为"中间点"做更新**

### 1、存储

- 邻接矩阵存储

![](https://img2023.cnblogs.com/blog/3424847/202404/3424847-20240427220702309-1802848532.png)

- 用两个数组存储信息
    - 一个存储两点长度
    - 一个存储路径Path
    - ![](https://img2023.cnblogs.com/blog/3424847/202404/3424847-20240427220701856-1424646447.png)
        

其中，D(-1)表示初始状态，相当于邻接矩阵，对于Path(-1)对角线上标-1，表示不用走

**Path其他位置存储的是当前点到对应终点的前一个位置！！！**，如下图

![image-20240427203120533](https://img2023.cnblogs.com/blog/3424847/202404/3424847-20240427220701378-2140733048.png)

虽然可以从2到1，但是根据当前的D(-1)是不能到达的，所以先置为-1

### 2、**动态规划**

- 依次将每个点作为"中间点"做更新

```cpp
for (int k = 1; k <= n; k++)   
    for (int i = 1; i <= n; i++)   
        for (int j = 1; j <= n; j++)   
            d[i][j] = min(d[i][j],d[i][k] + d[k][j]);
```

注意：要k在最外层

理论中 应该是d[k,i,j]=min(d[k-1,i,k]+d[k-1,k,j])

但是对于第k-1层的d[i,k]和d[k,j]都不经过k点，所以将三维省为二维

### 3、路径处理

由于需要存储路径，所以在第二步时，对p数组处理
```cpp
 for (int k = 1; k <= n; k++)   
     for (int i = 1; i <= n; i++)   
         for (int j = 1; j <= n; j++)   
             if (d[i][j] > d[i][k] + d[k][j]) {  
                 d[i][j] = d[i][k] + d[k][j];  
                 p[i][j] = k;  
             }
```
在获取到路径后，需要递归输出

原理：假定s和t为起点终点，先输出从s到t的前一个，即k，再输出从k到j

```cpp
void path(int i,int j) {  
	if (p[i][j] == 0)return;  
	int k = p[i][j];  
	path(i, k);  
	cout << k << " ";  
	path(k, j);  
}
```
### 例题：

[https://www.luogu.com.cn/problem/P1339](https://www.luogu.com.cn/problem/P1339) 练手用，Floyd能80

参考：[https://www.bilibili.com/video/BV1u34y1575Y/?spm_id_from=333.337.search-card.all.click&vd_source=10b3ee1feea99fbdd76960a61a32f71e](https://www.bilibili.com/video/BV1u34y1575Y/?spm_id_from=333.337.search-card.all.click&vd_source=10b3ee1feea99fbdd76960a61a32f71e)

[https://www.bilibili.com/video/BV19k4y1Q7Gj/?spm_id_from=333.337.search-card.all.click&vd_source=10b3ee1feea99fbdd76960a61a32f71e](https://www.bilibili.com/video/BV19k4y1Q7Gj/?spm_id_from=333.337.search-card.all.click&vd_source=10b3ee1feea99fbdd76960a61a32f71e)