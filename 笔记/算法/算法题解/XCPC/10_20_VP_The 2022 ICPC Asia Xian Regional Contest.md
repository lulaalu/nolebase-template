---
归类:
  - "[[XCPC]]"
---
[The 2022 ICPC Asia Xian Regional Contest](https://codeforces.com/gym/104077)
# 签到题题解 CFJ

## J. Strange Sum

易证最多只能选两个，从大到小排序后 $\max(0, a_1) + \max(0, a_2)$ 即为所求。

```CPP
void solve(){
    cin>>n;
    vector<ll>a(n+1);
    for(int i=1;i<=n;i++)cin>>a[i];
    sort(a.begin()+1,a.end());
    ll ans=a[n]+a[n-1];
    ans=max(ans,a[n]);
    if(ans<0)cout<<0;
    else cout<<ans;
}
```
## C. Clone Ranran

先出题再克隆一定不优，所以枚举克隆几次，再出题，时间复杂度 $\log n$。

```CPP
ll f[32];
void pre(){
    f[0] = 1;
    for(int i=1;i<32;i++){
        f[i]=f[i-1]*2;
    }
}

void solve(){
    ll a,b,c;
    cin >> a >> b >> c;
    ll ans = 1e18;
    for(int i=0;i<32;i++){
        ll cnt = f[i];
        ll tans = ((c+cnt-1)/cnt)*b+i*a;
        ans = min(ans,tans);
    }
    cout << ans << '\n';
}
```
## F Hotel

主要注意一下同一个房间的同性别的也可以住俩房间

```CPP
string s[1010];
void solve(){
    int n,c1,c2;
    cin >> n >> c1 >> c2;
    for(int i=0;i<n;i++){
        cin >> s[i];
    }    
    if(c1 * 2 <= c2){
        cout << 3 * n * c1;
        return;
    }
    int ans = 0;
    for(int i=0;i<n;i++){
        if(s[i][0]==s[i][1] || s[i][0]==s[i][2] || s[i][1] == s[i][2]){
            ans += min(c1,c2)+c2;
        }else{
            ans += 3*min(c1,c2);
        }
    }
    cout<<ans;
}
```
# G. Perfect Word

## 题意

给若干串，要求构造一个串，这个串的所有子串都在给的串中出现
## 思路

我感觉有点巧妙

先按长度排好序，每次枚举给的串 $s$，如果$s[0\dots n-2],s[1\dots n-1]$ 都出现了，那么把$s$ 加入 $set$ 中

注意，如果 $s[0\dots n-2]$ 在 $set$ 中出现了，说明对应的-1长度的两个子串也出现

也就是说对 $s$ 长度-1的串的判定实际上是对s所有子串都判定了

给定的所有串长度是 $1e5$ ，甚至不需要字符串哈希之类的，直接就装得下
## 代码

```CPP
#define ll long long
#define inf INT_MAX
#define INF LONG_LONG_MAX
#define endl '\n'

const int N = 1e5 + 5;

string s[N];
set<string>hsh; // 一开始写哈希的，写完调了一下被队友发现根本不用多此一举

void solve(){
    int n;
    cin >> n;
    for(int i=0;i<n;i++){
        cin >> s[i];
    }

    sort(s,s+n,[](string &s1,string &s2){
        return s1.length() < s2.length();
    });

    int ans = 1;
    for(int i = 0; i < n; i++){
        int len = s[i].length();
        if(len == 1)hsh.insert(s[i]);
        else{
            if(hsh.find(s[i].substr(0,len-1)) != hsh.end() && hsh.find(s[i].substr(1,len-1)) != hsh.end()){
                ans = s[i].length();
                hsh.insert(s[i]);
            }
        }
    }
    cout << ans << endl;
}
```
# L. Tree
## 题意

给一颗树，分成若干个点集，要求每个点集内任意两个点满足以下两个要求之一

- 对于所有 $u, v\in S$ ， $u\neq v$,  要么u在v的子树中，要么v在u的子树内 
- 对于所有 $u, v\in S$ ， $u\neq v$, 同时满足uv不在相互的子树中
## 思路

分析一下就是，要么点集内所有的点组成一条链，要么是点集内没有任何两个点的LCA同样在点集内

从一开始的考虑要么是叶子结点的数量，要么是层数

到把链缩成点后的要么是叶子结点要么是层数

到最后的，每次把所有叶子结点划分为一个点集，然后从图中删除

大致是这样，具体实现还得看队友啊
## 代码

```CPP
#define ll long long
const int N = 1000005; 
int f[N], in[N]; 

void solve(){
    int n; 
    cin>>n; 
    for(int i = 1;i<=n;++i)
        in[i] = 0;
    for(int i = 2;i<=n;++i){
        cin>>f[i]; 
        ++in[f[i]]; 
    }
    vector<int> vec1, vec2, *cur = &vec1, *next = &vec2; 
    for(int i = 1;i<=n;++i){
        if(!in[i])
        cur->push_back(i); 
    }
    int ans = cur->size(); 
    int layer = 0; 
    do{
        ++layer; 
        for(auto iter:*cur){
            --in[f[iter]]; 
            if(!in[f[iter]] && f[iter])
                next->push_back(f[iter]); 
        }
        swap(cur, next);  
        ans = min(ans,(int)(layer+cur->size())); 
        next->clear(); 
    }while(!cur->empty()); 
    cout<<ans<<endl; 

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
# E. Find Maximum

## 题意

$f(x) = \begin{cases} 1 & (x = 0) \\ f(\frac{x}{3}) + 1 & (x > 0\land x\bmod3 = 0) \\ f(x - 1) + 1 & (x > 0\land x\bmod 3\neq 0) \end{cases}$

计算$\max_{x = l} ^ r f(x)$.

$1\leq l\leq r\leq 10 ^ {18}$
## 思路

发现 f(n) 是 n 在三进制下的数码和加上数位个数

那么取R的三进制，然后枚举每一位，当前位-1，当前位后面的全变为2

相当于考虑尽可能多的2，最高就80位

队友太给力啦，最后一小时调出来了
## 代码

```CPP
#define ll long long
ll l,r;
ll ksm(ll x,ll y){
    ll res=1;
    while (y>0)
    {
        if(y&1)res=res*x;
        x=x*x;
        y=y/2;
    }
    return res;
}
ll f(ll x){
    if(x == 0)return 1;
    if(x % 3 == 0)return f(x/3)+1;
    return f(x-1)+1;
}
vector<ll> three(ll x){
    vector<ll>y;
    while (x>0)
    {
        y.push_back(x%3);
        x/=3;
    }
    return y;
}
ll ten(vector<ll> x){
    ll y=0;
    for(int i=0;i<x.size();i++){
        y+=ksm(3,i)*x[i];
    }
    return y;
}
void solve(){
    cin>>l>>r;
    vector<ll>rr=three(r);
    ll ans=0;
    ans=max(f(l),f(r));
    for(int i=rr.size()-1;i>=0;i--){
        vector<ll>temp=rr;
        if(rr[i]==1&&i!=rr.size()-1){
            temp[i]=0;
            for(int j=i-1;j>=0;j--){
                temp[j]=2;
            }
            if(ten(temp)>=l){
                ans=max(ans,f(ten(temp)));
                break;   
            }
        }
        else if(rr[i]==2){
            temp[i]=1;
            for(int j=i-1;j>=0;j--){
                temp[j]=2;
            }       
            if(ten(temp)>=l){
                ans=max(ans,f(ten(temp)));
                break;   
            }   
        }
    }
    for(int i=0;i<rr.size()-1;i++)rr[i]=2;
    rr[rr.size()-1]=0;
    if(ten(rr)>=l)ans=max(ans,f(ten(rr)));
    cout<<ans<<'\n';
}
```

# 总结

这场的，铜牌题，还是看思维多一些，而且题的梯度安排比较接近，做的很舒适