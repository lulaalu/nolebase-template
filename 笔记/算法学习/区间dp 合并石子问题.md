## 合并石子问题

https://www.luogu.com.cn/problem/P1880

##### [NOI1995] 石子合并

##### 题目大致描述

$N$堆石子摆成了一个圆，每相邻的两堆合成一堆，新的一堆的石子数为得分，求得分最小和最多

$1\leq N\leq 100$，$0\leq a_i\leq 20$。



**解决思路**：取每个区间的最大值

1、获取数据，取得前缀和

2、第一层for循环，用**长度**，从2到n

3、第二层for循环，用**起点**，

4、在第三层for循环前先确定终点，然后第三层循环把每一个**小区间**枚举对比

关键代码实现：

```C++
//第一层 长度 
for(int len=2;len<=n;len++){
    //第二层 起始点 
    for(int i=1;i<=n-len+1;i++){
        //终点 
        int j=i+len-1;
        //由于是最小值，先赋初值 
        dpmin[i][j] = 1e9;
        //第三层  中间每一个最值
        for(int k=i;k<j;k++){
            //状态转移 本身与内部，但内部在合成后 还需要加上本次消耗的体力 
            dpmax[i][j]=max(dpmax[i][j],dpmax[i][k]+dpmax[k+1][j]+sum[j]-sum[i-1]);
            dpmin[i][j]=min(dpmin[i][j],dpmin[i][k]+dpmin[k+1][j]+sum[j]-sum[i-1]);
        }
    }
}
```

但是由于是圆形，需要考虑越过数组终点与数组开头合并，所以用两倍数组，可以包含所有情况：

```C++
#include<iostream>
using namespace std;
const int N = 210;
long dpmin[N][N],dpmax[N][N],sum[N];
int n;

int main()
{
	cin >> n;
	sum[0] = 0;

	for (int i = 1; i <= n; i++){
		cin >> sum[i];
		sum[i + n] = sum[i];
		sum[i] += sum[i - 1];
	}
	for (int i = n + 1; i <= 2 * n; i++)
        sum[i] += sum[i - 1];
	
	long mins = 1e9,maxs = 0;

	for (int len = 2; len <= n ; len++){
		for (int i = 1; i <= 2 * n - len + 1; i++){
			int j = i + len - 1;
			dpmin[i][j] = 1e9;
			for (int k = i; k < j; k++){
				dpmin[i][j] = min(dpmin[i][j], dpmin[i][k] + dpmin[k + 1][j] + sum[j] - sum[i - 1]);
				dpmax[i][j] = max(dpmax[i][j], dpmax[i][k] + dpmax[k + 1][j] + sum[j] - sum[i - 1]);
			}
			if (len == k){
				mins = min(mins, dpmin[i][j]);
				maxs = max(maxs, dpmax[i][j]);
			}
		}
	}
	cout << mins << endl;
	cout << maxs << endl;

	return 0;
}
```

