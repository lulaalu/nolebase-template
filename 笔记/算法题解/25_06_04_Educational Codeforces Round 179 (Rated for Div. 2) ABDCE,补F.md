---
创建时间: 2025-06-04T20:06:00
---
比赛时间：2025-06-03

- ABCDEF全带个贪心标签，我感觉我都是感觉出来的，可能不能讲的太清晰，见谅见谅
# A. Energy Crystals

## mean

三个数，每个数都要大于等于任一另一数的一半向下取整，如2,3,5
## way

观察得到是下面的情况

$[0,0,1]\to[0,1,1]\to[1,1,3]\to[1,3,3]\to[3,3,7]\to[3,7,7]\to[7,7,15]$

3->7->15，$a[i]=2*a[i-1]+1$，获取i，然后要乘2

## code

```cpp
vector<i64> v;
signed main()
{
    i32 _ = 1;
    i64 k = 1;
    for (int i = 0; k <= 1e9; i++)
    {
        v.push_back(k);
        k = k * 2 + 1;
    }
    cin >> _;
    i64 n;
    while (_--)
    {
        cin >> n;
        i64 index = lower_bound(all(v), n) - v.begin();
        cout << 2 * index + 3 << endl;
    }
    return 0;
}
```

# B. Fibonacci Cubes

## mean

一组正方体，边长按斐波那契数列的顺序

能否放入长宽高给定的盒子里

## way

斐波那契，假定有n个方块，那第1到n-1个都可以平铺在第n个上面，所以先判断最短的两条边能不能放得下最大的方块

由于第n-1个方块如果要铺在第n个上，盒子高度必须满足，所以看$f[n]+f[n-1]$是否大于高度

## code

```cpp
vector<i64> f;
array<int, 3> a;
string s;
void solve()
{
    s = "";
    i64 n, m;
    cin >> n >> m;
    for (int i = 0; i < m; i++)
    {
        cin >> a[0] >> a[1] >> a[2];
        sort(a.begin(), a.end());
        if (f[n] + f[n - 1] <= a[2] && f[n] <= a[0] && f[n] <= a[1])
            s += '1';
        else
            s += '0';
    }
    cout << s << endl;
}

signed main()
{
    IOSIO;
    i32 _ = 1;
    f.push_back(0);
    f.push_back(1);
    f.push_back(2);
    for (int i = 3; i <= 2e5; i++)
    {
        f.push_back(f[i - 1] + f[i - 2]);
    }
    cin >> _;
    while (_--)
        solve();
    return 0;
}
```

# C. Equal Values

## way

实际上就是选定一个数值相等的子数组，然后数组的左右进行操作

唯一要注意的就是最后要处理一下，我是在input的基础上，在最后加一个大数

## code

```cpp
void solve()
{
    i64 n;
    i64 ans = 1e15;
    cin >> n;
    vector<i64> a(n);
    for (auto &i : a)
        cin >> i;
    a.push_back(1e10);
    i64 p = a[0];
    i64 index = 0;
    for (int i = 1; i < n + 1; i++)
    {
        if (a[i] != p)
        {
            i64 tans = p * index + p * (n - i);
            ans = min(ans, tans);
            p = a[i];
            index = i;
        }
    }
    if (ans == 1e15)
        ans = 0;
    cout << ans << endl;
}
```

# D. Creating a Schedule

## mean

每个人（小组）每天上六节课，上课下课时间一致

要求分配教室，让换教室时走的总距离最大

## way

符合直觉的，先排序，然后第一和最后一间教室配对，比如样例$[479 ,385,290, 293, 384, 383, 297, 478, 291, 382]$

只看楼层就是$[2,2,2,2,3,3,4,4]$ 

那六个人就分别是

$2\to 4$
$2\to 4$
$4\to 2$
$4\to 2$
$2\to 3$
$2\to 3$

然后循环就可以$2\to 4\to 2\to 4 \to 2\to 4$

我也想过分别从六个人分别从2,2,3,3,4,4开始，然后转圈，下节课都去到后面那个人的教师，但是写了个样例就hack了

## code

```cpp
vector<i64> a;
array<i64, 2> ans[N];
void solve()
{
    i64 n, m;
    cin >> n >> m;
    a.resize(m);
    for (i64 &i : a)
        cin >> i;
    sort(all(a));
    i64 index = 0;
    // 有一个2->4,同时也有4->2
    for (int i = 0; i < n / 2; i++)
    {
        ans[i][0] = a[index];
        ans[i][1] = a[m - 1 - index];
        if (index + 1 < m)
            index++;
    }

    for (int i = 0; i < n / 2; i++)
    {
        for (int j = 0; j < 3; j++)
            cout << ans[i][0] << " " << ans[i][1];
        cout << endl;
        for (int j = 0; j < 3; j++)
            cout << ans[i][1] << " " << ans[i][0];
        cout << endl;
    }
	// 级数情况补充一下就行
    if (n % 2 == 1)
    {
        ans[n / 2 + 1][0] = a[index];
        ans[n / 2 + 1][1] = a[m - 1 - index];
        i64 i1 = n / 2 + 1;
        for (int j = 0; j < 3; j++)
            cout << ans[i1][1] << " " << ans[i1][0];
        cout << endl;
    }
}
```

# E. Changing the String

## way

首先可以知道有一些操作是不用处理的，就是$a\to b$或$a\to c$

然后操作难度在于，难分辨$b\to c$要不要操作，因为可能存在：$b\to c\to a$

至于$b\to a$和$c\to a$，那是肯定要操作的

我先说明，还需要记录两个数据：

1. $bca[0]$表示$b\to c$的次数，$bca[1]$表示$c\to a$
2. $cba$类似

我先记录以上四个数据，再遍历s依次操作

接下来我在代码中说明原因

## code

```cpp
string s;
array<i64, 2> cba, bca;
i64 ba, ca;
```

```cpp
// 初始化
ba = ca = 0;
cba.fill(0);
bca.fill(0);
i64 n, m;
cin >> n >> m;
cin >> s;

for (int i = 0; i < m; i++)
{
	char ch1, ch2;
	cin >> ch1 >> ch2;
	if (ch1 == 'b' && ch2 == 'a')
	{
		// 相比于b->a,一定是c->a更加优
		// 但是c->b->a的前提是c->b,b->a按顺序出现
		// 所以先保证c->b出现了，再记录b->a
		// 否则就只记录b->a
		if (cba[0] >= cba[1] + 1)
			cba[1]++;
		else
			ba++;
	}
	else if (ch1 == 'c' && ch2 == 'a')
	{
		// 这里的理由跟c->b->a类似
		if (bca[0] >= bca[1] + 1)
			bca[1]++;
		else
			ca++;
	}
	else if (ch1 == 'c' && ch2 == 'b')
	{
		cba[0]++;
	}
	else if (ch1 == 'b' && ch2 == 'c')
	{
		bca[0]++;
	}
}
```

然后遍历s依次更改

```cpp
for (char &ch : s)
{
	// 如果是b，可以->a或bca
	if (ch == 'b')
	{
		// 优先b->a
		if (ba)
		{
			ba--;
			ch = 'a';
		}
		else
		{
			// 现在想要让b->a可以选择bca或cba中的ba
			// 优先选择cba
			// 选择cba中的ba，保留bca，让c更容易到达a
			// 如果选择bca，那么后面遇到c想要到a，就要经过cba，但是cba有c->b和b->a两条路径，不容易达成
			if (cba[1])
			{
				cba[1]--;
				ch = 'a';
			}
			else if (bca[0] && bca[1])
			{
				bca[0]--;
				bca[1]--;
				ch = 'a';
			}
		}
	}
	else if (ch == 'c')
	{
		if (ca)
		{
			ca--;
			ch = 'a';
		}
		else
		{
			// c->a先尝试bca，理由类似
			// 如果c->a先把cba用了，那b->a难度就会变大
			// 如果都不行，那么再尝试c->b
			if (bca[1])
			{
				bca[1]--;
				ch = 'a';
			}
			else if (cba[0] && cba[1])
			{
				cba[0]--;
				cba[1]--;
				ch = 'a';
			}
			else if (cba[0])
			{
				cba[0]--;
				ch = 'b';
			}
		}
	}
}
```

实话说看灵感，我比赛时没有想那么多

不理解的再看看其他大佬的题解吧

# F. Puzzle

## mean

给定周长和面积的比，用50000以内的拼图拼出目标

## way

首先看见是比例，先gcd处理成最简

这种拼图题目，手写几个

![](https://img2023.cnblogs.com/blog/3424847/202506/3424847-20250604233010332-859921428.png)

在尽量靠近正方形的情况下，边是最小的

以13为例，越靠近条形，边越大：

![](https://img2023.cnblogs.com/blog/3424847/202506/3424847-20250604233009606-1187995527.png)

边side是$4\times 4$，除了边固定，中间剩余可移动的方块数量是$cnt=13-4-4+1=6$

观察出每移动一个拼图，边会多2，这样就可以得到边的范围

那么S=13时，由side可知$C_{min}=2\times(side[0]+side[1])=16$

$C_{max}=C_{min}+2\times cnt=16+2\times 6=28$ 

然后把1到50000块拼图都处理出来，二分查找一下符合的周长和面积之比

代码是好理解的，我感觉不像其他倒数第二题那么抽象
## code

```cpp
array<i64, 2> range[N];
array<i64, 2> side[N];
double eps = 1e-9;

i64 c, s;

/*
i越大，c0/s越小，c1/s基本不变，c1 = 2s+2
s/c0越大，
我用的是s/c,而不是c/s因为一开始直接用整数进行除，后面改成double了也没改回去
*/
bool check(i64 x)
{
    return double(x) / range[x][0] - double(s) / c < eps;
}

void deal(i64 &c, i64 &s)
{
    i64 g = __gcd(c, s);
    c /= g;
    s /= g;
    // 特殊的，如果c为奇数是不可能的
    if (c % 2 == 1)
    {
        c *= 2;
        s *= 2;
    }
}

void solve()
{
    cin >> c >> s;
    deal(c, s);
    // range[s][1] 实际上等于 2*s+2
    if (2 * s + 2 < c)
    {
        cout << "-1" << endl;
        return;
    }
    i64 l = 1, r = 50000;
    // 找到的满足p/s条件的最小mid，也就是最小i
    i64 ans = -1;
    while (l <= r)
    {
        i64 mid = (l + r) / 2;
        if (mid == ans) break;
        if (check(mid)) ans = mid, l = mid + 1;
        else r = mid - 1;
    }
    // ans目前只是比例上满足条件，要让s也满足，由于比例有单调性，让放大ans变为s的倍数即可
    if (ans % s != 0) ans = (ans / s + 1) * s;

    c *= (ans / s);
    s = ans;

    cout << s << endl;

    // 处理两条边线
    for (int i = 0; i < side[s][0]; i++) cout << i << ' ' << 0 << endl;
    for (int i = 1; i < side[s][1]; i++) cout << 0 << ' ' << i << endl;

    // 处理需要移动到边线，作为延长的点
    // (周长-底线)/2 ，del表示除了两条边，中间需要移动的点的数量
    i64 del = (c - range[s][0]) / 2;
    for (int i = 0; i < del; i++)
        cout << side[s][0] + i << ' ' << 0 << endl;

    // 处理中间的点
    i64 cnt = s - del - side[s][0] - side[s][1] + 1;
    for (int i = 1; i < side[s][0] && cnt; i++)
    {
        for (int j = 1; j < side[s][1] && cnt; j++)
        {
            cnt--;
            cout << i << ' ' << j << endl;
        }
    }
}

signed main()
{
    i32 _ = 1;
    i64 a = 1; // 正方形的边长
    for (i64 i = 1; i < N; i++)
    {
        // 结合excel图像进行理解
        if (a * a == i)
        {
            range[i][0] = 2 * (a + a);
            range[i][1] = 2 * (a + a) + 2 * (a - 1) * (a - 1);
            side[i][0] = a;
            side[i][1] = a;
            a++;
        }
        else
        {
            if (a * (a - 1) >= i)
            {
                range[i][0] = 2 * (a + (a - 1));
                range[i][1] = 2 * (a + (a - 1)) + 2 * (i - 2 * (a - 1));
                side[i][0] = a - 1;
                side[i][1] = a;
            }
            else
            {
                range[i][0] = 2 * (a + a);
                range[i][1] = 2 * (a + a) + 2 * (i - a - a + 1);
                side[i][0] = a;
                side[i][1] = a;
            }
        }
    }

    cin >> _;
    while (_--) solve();

    return 0;
}

```