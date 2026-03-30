---
创建时间: 2025-05-06T20:20:00
归类: []
tags:
  - codeforces
---
## A

### 题意

给一些数，把这些数分成两组，每组不能为空，要求两组gcd不同

### 思路

把最大值分在一组，然后其他值分在另一组

判断分组可行与否

### 代码

```cpp
void solve()
{
    int n;
    cin >> n;
    vector<int> a(n);
    i32 mx = 0;
    for (int &i : a)
    {
        cin >> i;
        mx = max(mx, i);
    }
    int g = 0;
    for (int i = 0; i < n; i++)
    {
        if (a[i] != mx)
        {
            g = __gcd(g, a[i]);
        }
    }
    if (g == 0)
        cout << "No\n";
    else
    {
        cout << "Yes\n";
        for (int i = 0; i < n; i++)
        {
            if (a[i] == mx)cout << 1 << " ";
            else cout << 2 << ' ';
        }
        cout << endl;
    }
}
```

## B

### mean

博弈，每个盒子取苹果，要求在取完后，保证max-min<=k，即每个盒子中的最大值和最小值的差值不高于k

### way

1. 先看最大值是不是原本就比最小值大，如果大k+1，那么取完后正好差k
2. 如果有多个最大值，在大k+1时就直接输了

如果是第1种情况，继续讨论，此时只需要看奇数偶数

### code

```cpp
void solve()
{
    int n, k;
    cin >> n >> k;
    vector<i64> a(n);
    for (int i = 0; i < n; i++)
    {
        cin >> a[i];
    }
    i64 sum = 0;
    for (int i = 0; i < n; i++)
        sum += a[i];

    i64 mx = *max_element(all(a)), mn = *min_element(all(a));
    i32 mxcnt = 0, mncnt = 0;
    for (int i = 0; i < n; i++)
    {
        if (a[i] == mx)mxcnt++;
        else if (a[i] == mn)mncnt++;
    }
	// 超过最大值，则直接判
    if (mx - mn > k + 1)
    {
        cout << "Jerry\n";
        return;
    }
    // 正好是k+1，看mxcnt
    if (mx - mn == k + 1)
    {
        if (mxcnt == 1)
        {
            if ((sum - 1) % 2 == 0)cout << "Tom\n";
            else cout << "Jerry\n";
        }
        else cout << "Jerry\n";
    }
    else // 反之看奇数偶数，-1是把最大值取了一个再看，不影响
    {
        if ((sum - 1) % 2 == 0)cout << "Tom\n";
        else cout << "Jerry\n";
    }
}
```

## C

### mean

给一个01串，一个数组，其中$s[i]=0$表示对应数组的i位置你不知道，默认为0

要求你用$\pm1e18$内的数填充不知道的位置，使得数组的所有子数组的和的最大值等于k

### way

猜测全都置-INF，然后任取一个index进行二分查找，总能找到

### code

```cpp
string s;
vector<i64> a;
i64 n, k;

i64 check(i64 x, i32 index)
{
    a[index] = x;
    i64 mx = -1e17;
    i64 cur = 0;
    for (int i = 0; i < n; i++)
    {
        cur = max(cur + a[i], a[i]);
        mx = max(mx, cur);
    }
    if (mx == k) return 1;
    else if (mx > k) return 2;
    else return 0;
}

void solve()
{
    cin >> n >> k;
    cin >> s;
    a.resize(n);
    for (i64 &i : a)
        cin >> i;

    for (int i = 0; i < n; i++)
        if (s[i] == '0')
            a[i] = -1e13;

    i64 mx = 0;
    i64 cur = 0;
    for (int i = 0; i < n; i++)
    {
        cur = max(cur + a[i], a[i]);
        mx = max(mx, cur);
    }

    i32 index = s.find('0'); // 找到第一个0的位置
    if (mx > k || (mx != k && index == -1))
    {
        cout << "No\n";
        return;
    }

    if (index != -1)
    {
        i64 l = -1e18, r = 1e18; // 二分
        i64 pre = -1111;
        while (1)
        {
            i64 mid = (l + r) / 2;
            i64 res = check(mid, index);
            if (res == 1)break;
            else if (res == 2)r = mid - 1;
            else l = mid + 1;
            if (mid == pre)break; // 不存在的情况，只是出于严谨，实际上不需要
            pre = mid;
        }
    }

    cout << "Yes\n";
    for (int i = 0; i < n; i++)
    {
        cout << a[i] << ' ';
    }
    cout << endl;
}

```

！注意数据大小，一开始竟然因为k取了i32而反复wa8！