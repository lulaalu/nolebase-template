- 哈希字符串

```cpp
#define IOS ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
const int N = 1e6 + 10;
const int p = 21839;
const int MOD = 1e9 + 7;
string s;
int n;
int hf[N]; // p^N
void prepare(int n)
{
    hf[0] = 1;
    for (int i = 1; i <= n; i++) hf[i] = (hf[i - 1] * p) % MOD;
}

bool check(int sta, int hpre)
{
    int hinit = hpre;
    for (int i = sta + 1; i < n - 1; i++)
    {
        hpre = ((hpre - s[i - sta - 1] * hf[sta]) % MOD + MOD) % MOD;
        hpre = (hpre * p % MOD) + s[i];
        if (hpre == hinit) return true;
    }
    return false;
}

void solveHash()
{
    cin >> s;
    n = s.length();
    prepare(n);
    int ans = -1;
    int hpre = 0, haft = 0;
    for (int i = 0; i < n; i++) 
    {
        hpre = (hpre * p + s[i]) % MOD;
        haft = (haft + (hf[i] * s[n - i - 1]) % MOD) % MOD;
        if (hpre == haft) if (check(i, hpre)) ans = i + 1;
    }
    if (ans != -1) cout << s.substr(0, ans);
    else cout << "Just a legend";
}
```

- Z函数（扩展KMP）

```cpp
const int N = 1e6 + 10;
int z[N];
int e[N];
string s, t;
void zArray(string &s, int n)
{
    z[0] = n;
    for (int i = 1, c = 1, r = 1, len; i < n; i++) {
        len = r > i ? min(r - i, z[i - c]) : 0;
        while (i + len < n && s[i + len] == s[len]) len++;
        if (i + len > r)
        {
            r = i + len;
            c = i;
        }
        z[i] = len;
    }
}

void solveZ()
{
    cin >> s;
    t = s;
    int n = s.length(), m = n;
    zArray(t, m);
    int ans = -1;

    set<int> match;
    // match中存储能与前缀匹配的后缀长度
    // 如果在match有比某个z[i]小的值x，说明这个x是答案
    for (int i = n - 1; i >= 1; i--) {
        if (match.size() > 0) {
            auto it = match.upper_bound(z[i]);
            // *it是大于z[i]的
            // 所以it往前的会小于等于z[i]
            if (it != match.begin()) {
                it--;
                ans = max(*it, ans);
            }
        }
        if (i + z[i] == n) match.insert(z[i]);
    }

    if (ans != -1) cout << s.substr(0, ans);
    else cout << "Just a legend";
    
}
```

- KMP

```cpp
const int N = 1e6 + 10;
int nxt[N];
string t;
void buildNext(string &t)
{
    nxt[0] = 0;
    int i = 1;
    int nlen = 0;
    int tlen = t.length();
    while (i < tlen) {
        if (t[nlen] == t[i]) {
            nlen++;
            nxt[i] = nlen;
            i++;
        }
        else {
            if (nlen == 0){
                nxt[i] = 0;
                i++;
            }
            else nlen = nxt[nlen - 1];
        }
    }
}

bool vis[N];
void solveKMP()
{
    cin >> t;
    buildNext(t);
    int n = t.length();
    fill_n(vis, n + 5, false);
    for (int i = 1; i < n - 1; i++) vis[nxt[i]] = 1;
    
    int x = nxt[n - 1];
    while (!vis[x] && x != 0) x = nxt[x - 1];
    
    if (x != 0)cout << t.substr(0, x);
    else cout << "Just a legend";
}
```

- e数组

```cpp
// a[i...] 与 b[0...]的最长公共前缀
void eArray(string &a, string &b, int n, int m) {
    for (int i = 0, c = 0, r = 0, len; i < n; i++) {
        // r没包住i，设置0
        // 如果包住了，那么i到右边界和关键点c的扩充长度
        len = r > i ? min(r - i, z[i - c]) : 0;
        // len现在是最小的长度，现在再看看能不能扩的更远
        while (i + len < n && len < m && a[i + len] == b[len]) len++;
        if (i + len > r) {
            r = i + len;
            c = i;
        }
        e[i] = len;
    }
}
```

- STL去重

`unique` 将arr中相邻的相同元素放在末尾

```CPP
vector<int>arr={...};
arr.erase(unique(arr.begin(),arr.end()),arr.end());
```

- 随机数生成

```CPP
unsigned seed1 = std::chrono::system_clock::now().time_since_epoch().count();
std::mt19937_64 g(seed1);// 数据范围 ull
```

- 连续数字的逆元递推

```CPP
inv[1] = 1;
rep(i, 2, n + 1) inv[i] = (p - inv[p%i]*(p/i) % p); // 从左往右线性递推
```

- 求 $i/j$ 向上取整：$(i+j-1)/j$  

- 数字开方向下取整（适用于数字过大但是在 $\_\_int128\_t$ 范围内）
```CPP
int sqr(int n) {
    int x = n;
    int y = (x + 1) / 2;
    while (y < x) {
        x = y;
        y = (x + n / x) / 2;
    }
    return x;
}
```