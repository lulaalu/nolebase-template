---
创建时间: 2025-05-12T11:06:00
归类:
  - "[[笔记/算法/数据结构与算法/模拟|模拟]]"
题目链接: https://codeforces.com/contest/2102
tags:
  - cnblogs
  - codeforces
---
# A. Dinner Time

## mean

构造长度为 $n$ 的且和为 $m$ 的数组，要求其中所有长度为 $p$ 的子数组的和为 $q$

## way

如果$n\%p==0$，即 $n$ 是 $k$ 个长度为 $p$ 的子数组组成

那么必须满足 $m=k\times q$ 

如果 $n\%p\neq 0$ 

- 粗略版：

说明那么除了 $k$ 个子数组，还有多余的不到 $p$ 个元素可以自由分配，一定可以构造出符合 $m$ 的情况 

- 细节版：

首先可以肯定是一个长度为$p$的周期性结构

假定余了 $r$ 个数

根据前 $k$ 个长度为 $p$ 的子数组，可知最后 $r$ 个数的和是$m-kq$

那么周期数组的前 $p-r$ 个数的和就是$q+kq-m$

这样才满足子数组和为$q$，总和为 $m$

那只要总和满足了，具体放什么数无所谓

---

用一组数举例子，比如，m=1，n=14，p=5，q=1000

那么前10个数总和就是2000，最后四个数的和是-1999，那第15个数就是2999，因为这样才能让m=1

所以前四个数只要和是-1999，可以随便构造，比如0,100,200,-1699,2999，或者-1999,0,0,0,2999

如果这个例子中n=13，那么第11到13位的和一定是-1999，所以第14,15位，也就是周期中的第4,5位和是1000-(-1999)=2999，比如分别是1000,1999，可以随意，那就是-1999,0,0,1000,1999。


## code

```cpp
i64 n, m, p, q;
cin >> n >> m >> p >> q;
if (n % p == 0)
{
	if ((n / p) * q == m)cout << "YES\n";
	else cout << "NO\n";
}
else cout << "YES\n";
```

# B. The Picky Cat

## mean

对于一个数组，判断其中的一个数可否在若干次处理后成为数组的中位数

处理方式：
- 取 $a[i]=-a[i]$ 

## way

分别以奇偶长度的数组举例

- 奇数

$[1,2,3,4,5,6,7]$ 其中1到4是可以的

- 偶数

$[1,2,3,4,5,6]$ 其中1到4是可以的

## code

```cpp
i64 n;
cin >> n;
vector<i64> a(n);
i64 sta = -1;
rep(i, 0, n)
{
	cin >> a[i];
	a[i] = _abs64(a[i]);
	if (i == 0)
		sta = a[i];
}
sort(all(a));
i64 index = find(all(a), sta) - a.begin();
index++;
if (index > (n + 1) / 2 + (n % 2 == 0 ? 1 : 0))
	cout << "NO" << endl;
else cout << "YES" << endl;
```

# C. Mex in the Grid

## mean

把 $0$ 到 $n^{2}-1$ 这些数字放置到 $n\times n$ 的矩阵内

要求所有子矩阵的 $MEX$ 的和最大

- $MEX$ 指未出现的非负整数的最小值
	- $MEX([0,1,2])=3,MEX([1,2])=0$

## way

大致思路是尽量让小的数字出现在尽可能多的子数组中

最好还是让0,1,2这些从小到大的数字尽可能多的出现

首先可以让0123出现在矩阵的正中间

然后观察样例猜出是从中心往外卷出去的

$$\begin{matrix}
6 & 7 & 8 & 9 \\
5 & 0 & 1 & 10\\
4 & 3 & 2 & 11\\
15& 14& 13& 12
\end{matrix} $$

从0到1到2到3是一个圆圈卷出去

观察一下怎么走
- 右下左上，每次走 $step$ 步方向变换
- $step$从1开始，每两次+1
- 右1下1左2上2右3下3左4上4右5下5左6上6
- 直到 $step$ 到达 $n-1$ ，最后再走一次 $n-1$ 完成填充

## code

```cpp
i64 g[N][N];

i64 dx[4] = {0, 1, 0, -1};
i64 dy[4] = {1, 0, -1, 0};

void solve()
{
    i64 n;
    cin >> n;
    i32 dir = 0;
    i64 x = (n + 1) / 2, y = (n + 1) / 2;
    g[x][y] = 0;
    i64 cnt = 1;
    for (i64 step = 1; step < n; step++)
    {
        for (i64 i = 0; i < 2; i++)
        {
            for (i64 j = 0; j < step; j++)
            {
                x += dx[dir], y += dy[dir];
                g[x][y] = cnt++;
            }
            dir = (dir + 1) % 4;
        }
    }
	// 再走最后一次，step为n-1
    for (i64 j = 0; j < n - 1; j++)
    {
        x += dx[dir], y += dy[dir];
        g[x][y] = cnt++;
    }
    dir = (dir + 1) % 4;

    for (i64 i = 1; i <= n; i++)
    {
        for (i64 j = 1; j <= n; j++)
            cout << g[i][j] << " ";
        cout << endl;
    }
}

```

其实我是先写了一个check装置，蒙了几种情况，确认这种方法是最优的才继续

```cpp
i64 n;
cin >> n;
vector<vector<i64>> g(n, vector<i64>(n));
rep(i, 0, n)rep(j, 0, n)
{
	cin >> g[i][j];
}

i64 ans = 0;
// 子数组的高宽
for (i64 w = 1; w <= n; w++)
{
	for (i64 h = 1; h <= n; h++)
	{
		//子数组的开始i和j
		for (i64 stai = 0; stai + h - 1 < n; stai++)
		{
			for (i64 staj = 0; staj + w - 1 < n; staj++)
			{
				set<i64> st;
				for (i64 i = stai; i <= stai + h - 1; i++)
				{
					for (i64 j = staj; j <= staj + w - 1; j++)
					{
						st.insert(g[i][j]);
					}
				}
				i64 k;
				for (i64 num = 0; num < 1e9; num++)
				{
					if (st.count(num) == 0)
					{
						k = num;
						break;
					}
				}
				ans += k;
			}
		}
	}
}
cout << ans << endl;
```
# D. Quartet Swapping

## mean

给一个数组，可以按规则交换若干次，求最小“字典序”

规则就是交换13和24位置，即$[4,3,2,1]\to[2,1,4,3]$ 

## way

从字典序最小考虑，先把最小的往前放，可以记录每个数字的位置，然后从对应位置换起

但是如果先把1往前挪，如$[5,4,3,1,\dots,2,\dots]$

会变成$[3,1,5,4,\dots,2,\dots]$

但是 $2$ 是有机会到第一个位置的，这样交换过去又会影响 $1$ 的位置

所以需要修改方案，此时观察到规则限制使得交换位置只能是奇数位与奇数位，偶数位与偶数位

所以方案如下：
1. 先把奇偶 $index$ 对应的数字记录下来
2. 排序后从奇开始处理奇数位的最小数字，即把最小数字不断往前挪
3. 挪到的 $pos$ 记录下来，表示 $1$ 到 $pos$ 都是有序的
4. 奇数的最小数，偶数的最小数，奇数的次小数等$\dots$ 依次处理

而挪动时有两种方式
- $[1,2,3,*]\to[3,*,1,2]$
- $[1,2,*,4]\to[*,4,1,2]$ 

先后无所谓，只是如果$*$在最后，不能用第 $2$ 种

还有一点：挪动不要一个一个挪，会 $tle$
## code

```cpp
CL N = 2e5 + 10;
i64 a[N];
i64 index[N];
i64 oddNum[N];  //len = (n + 1) / 2;
i64 evenNum[N]; //len = n / 2;
i64 n;
i64 finalPos = -1;

static void deal(i64 x)
{
    i64 idx = index[x];
    if (idx <= finalPos)
        return;
    i64 tmpFinalPos = idx;
    while (1)
    {
        bool case1swap = false;
        bool case2swap = false;
        for (i64 k = (idx - finalPos) / 2 + 10; k > 0 && case1swap == false; k--)
        {
            if (idx - 2 * k >= 1 && idx + 1 <= n && idx - 2 * k > finalPos)
            {
                index[a[idx]] = idx - 2 * k;
                index[a[idx - 2 * k]] = idx;
                index[a[idx + 1]] = idx - 2 * k + 1;
                index[a[idx - 2 * k + 1]] = idx + 1;
                swap(a[idx], a[idx - 2 * k]);
                swap(a[idx + 1], a[idx - 2 * k + 1]);
                
                case1swap = true;
                idx -= 2 * k;
                tmpFinalPos = idx;
            }
        }
        if (!case1swap)
        {
            for (i64 k = (idx - finalPos) / 4 + 10; k > 0 && case2swap == false; k--)
            {
                if (idx - 4 * k + 1 >= 1 && idx - 4 * k + 1 > finalPos)
                {
                    index[a[idx]] = idx - 4 * k + 2;
                    index[a[idx - 4 * k + 2]] = idx;
                    index[a[idx - 4 * k + 1]] = idx - 1;
                    index[a[idx - 1]] = idx - 4 * k + 1;
                    swap(a[idx], a[idx - 4 * k + 2]);
                    swap(a[idx - 1], a[idx - 4 * k + 1]);
                    
                    case2swap = 1;
                    idx = idx - 4 * k + 2;
                    tmpFinalPos = idx - 1;
                }
            }
        }
        if (!case1swap && !case2swap)
            break;
    }
    finalPos = max(finalPos, tmpFinalPos);
}

void solve()
{
    finalPos = -1;
    cin >> n;
    rep(i, 1, n + 1)
    {
        cin >> a[i];
        index[a[i]] = i;
        if (i % 2 == 1)oddNum[i / 2] = a[i];
        else evenNum[i / 2 - 1] = a[i];
    }
    sort(oddNum, oddNum + (n + 1) / 2);
    sort(evenNum, evenNum + n / 2);

    for (i64 i = 0; i < n / 2; i++)
    {
        deal(oddNum[i]);
        deal(evenNum[i]);
    }
    if (n % 2 == 1) deal(oddNum[n / 2]);
    for (i64 i = 1; i <= n; i++)cout << a[i] << " ";
    cout << endl;
}
```
