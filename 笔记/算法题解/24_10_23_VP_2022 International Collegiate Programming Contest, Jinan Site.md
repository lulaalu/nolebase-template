---
归类:
  - "[[笔记/算法/算法题解/XCPC/XCPC]]"
---
[2022 International Collegiate Programming Contest, Jinan Site](https://codeforces.com/gym/104076)
# K. Stack Sort

## 题意

给一个从1到n的排序，按顺序把这些数压入m个栈

全部入栈后，每次选择一个栈，把数字全部弹出再换下一个

要求弹出顺序是升序的
## 思路

发现要把 $x$ 和 $x-1$ 压入同一个栈，这样出栈才能按顺序

可以从 $n$ 开始，每次找-1的数字， $x-1$ 的位置要在 $x$ 的后面，否则就加一个栈
## 代码

```CPP
const int N = 5e5 + 10;
int a[N];
int pos[N];
 
void solve(){
    int n;
    cin >> n;
    for(int i = 1; i <= n; i++){
        cin >> a[i];
        pos[a[i]] = i;
    }
 
    int ans = 1;
    int pre = pos[n];
    for(int i=n - 1;i>=1;i--){
        if(pos[i] > pre){
            pre = pos[i];
        }else{
            ans++;
            pre = pos[i];
        }
    }
    cout << ans << '\n';
}
```
# M. Best Carry Player

## 思路

发现无论什么顺序都不影响进位，类似高精度模拟一下
## 代码

队友上强度啦，考验手速

```CPP
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define inf INT_MAX
#define INF LONG_LONG_MAX
int n,ans;
string add(string s1,string s2){
    vector<int>a1;
    vector<int>a2;
    for(int i=0;i<s1.size();i++)a1.push_back(s1[i]-'0');
    for(int i=0;i<s2.size();i++)a2.push_back(s2[i]-'0');
    vector<int>res;
    int temp=0;
    for(int i=0;i<max(a1.size(),a2.size());i++){
        if(i<min(a1.size(),a2.size())){
            if(a1[i]+a2[i]+temp>=10){
                res.push_back((a1[i]+a2[i]+temp)%10);
                temp=(a1[i]+a2[i]+temp)/10;
                ans++;
            }
            else{
                res.push_back((a1[i]+a2[i]+temp)%10);
                temp=(a1[i]+a2[i]+temp)/10;
            }
        }
        else{
            if(a1.size()>a2.size()){
                if(temp+a1[i]>=10){
                    res.push_back((a1[i]+temp)%10);
                    temp=(a1[i]+temp)/10;
                    ans++;
                }
                else{
                    res.push_back((a1[i]+temp)%10);
                    temp=(a1[i]+temp)/10;
                }
            }
            else{
                if(temp+a2[i]>=10){
                    res.push_back((a2[i]+temp)%10);
                    temp=(a2[i]+temp)/10;
                    ans++;
                }
                else{
                    res.push_back((a2[i]+temp)%10);
                    temp=(a2[i]+temp)/10;
                }
            }
        }
    }
    if(temp!=0)res.push_back(temp);
    while (res[res.size()-1]==0&&!res.empty())res.erase(res.begin()+res.size()-1);
    if(res.empty())res.push_back(0);
    string s="";
    for(int i=0;i<res.size();i++)s.push_back(char(res[i]+'0'));
    return s;    
}
void solve(){
    ans=0;
    cin>>n;
    vector<string>s(n+1);
    for(int i=1;i<=n;i++){
        cin>>s[i];
        reverse(s[i].begin(),s[i].end());
    }
    string ss=s[1];
    for(int i=2;i<=n;i++)ss=add(ss,s[i]);
    cout<<ans<<'\n';
}
int t=1;
int main(){
    ios::sync_with_stdio(0),cin.tie(0);
    cin>>t;
    while(t--){
        solve();
    }
    return 0;
}
```
# E. Identical Parity

## 题意

用1到n的数字构造一个序列，要求所有长度为k的子数组的奇偶性都相等
## 思路

k为偶数肯定不用说

一开始手玩的时候感觉，k为奇数时的子数组可能是 $\frac{k+1}{2}$ 个奇数，$\frac{k-1}{2}$ 个偶数

然后构造个 $10101$ 的序列，感觉是n在k到2k之间并且为奇数

再后面队友用一种安排方式，就是 $1,k+1,2k+1$ 这部分安排为奇数，后续奇偶轮流安排，如果安排的下去就 $Yes$

两种方式都 $wa$ 了之后，写了个对拍，输出后发现前面的 $n/k$ 个子数组有固定的顺序

那么最后缀着的几个数只能用剩余的数字摆放，如果剩余的数字超过理论的数字或者小于 $0$ 就 $NO$

 用10表示奇偶

一个长度为 $k$ 的子数组的奇偶数的数量是 $k_0=\frac{k-1}{2},k_1=\frac{k+1}{2}$

长度为 $n$ 的数组拥有的奇数和偶数数量是 $cnt_0=\frac{n-1}{2},cnt_1=\frac{k+1}{2}$

完整的长度为 $k$ 的子数组数量是 $d=n/k$ 

剩余可用的数的数量是 $rest_0=cnt_0-k_0d,rest_1=cnt_1-k_1d$

要满足剩余数大于等于0，且符合 $k_0,k_1$ 
## 代码

```CPP
void solve(){
    int n, k;
    cin >> n >> k;
    if(k % 2 == 0){
        cout<<"Yes\n";
        return;
    }
    if(n < 2 * k){ // 这个是暴力出来的
        cout << "Yes\n";
        return;
    }
    int k1 = (k + 1) / 2,k0 = (k - 1) / 2;
    int cnt1 = n / 2 + (n % 2), cnt0 = n / 2;
    int d = n / k;
    int rest1 = cnt1 - k1 * d, rest0 = cnt0 - k0 * d;
    if(rest1 >= 0 && rest0 >= 0 && rest1 <= k1 && rest0 <= k0){
        cout << "Yes\n";
    }else{
        cout << "No\n";
    }
}
```

# A. Tower

## 题意

有n座塔，高度不同

针对一座塔，有以下三项操作

1. 塔高+1
2. 塔高-1
3. 塔高/2，向下取整

先移走m座塔后，要用最少的操作让所有塔一样高，求操作次数
## 思路

首先应该对所有数进行若干次除2操作，再选取中位数进行加减1操作

所以中位数一定是数组中的数进行若干次除2的结果

那么可以算出所有的最终的中位数，算出对应的所有答案然后取最小值
## 代码

```CPP
#define endl "\n"
#define IOS ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
#define all(x) x.begin(), x.end()
#define ll long long

void solve() {
    int n, m;
    set<int> mid; // 所有的a和a/2的集合
    cin >> n >> m;
    vector<int> a(n);

    for (int &i : a) {
        cin >> i;
        int tmp = i;
        while (tmp) {
            mid.insert(tmp);
            tmp /= 2;
        }
    }

    ll ans = 4e18;

    auto get = [&](int x, vector<int> &a) {
        vector<int> vcnt;
        for (int i : a) {
            int cnt = 0;
            if (i > x) {
                while (i / 2 > x) {
                    cnt++;
                    i /= 2;
                }
                cnt += min(i - x, 1 + x - i / 2);
            } else {
                cnt = x - i;
            }
            vcnt.push_back(cnt);
        }
        
        ll ans = 0;
        sort(all(vcnt));
        for (int i = 0; i < n - m; i++) {
            ans += vcnt[i];
        }
        return ans;
    };

    for (int num : mid) {
        ans = min(ans, get(num, a));
    }
    cout << ans << ' ';
}
```