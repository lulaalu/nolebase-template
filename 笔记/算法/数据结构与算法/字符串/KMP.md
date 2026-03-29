---
归类:
  - "[[字符串]]"
考点:
  - "[[next数组]]"
---
[[KMP]]算法相关内容。

[优秀讲解](https://www.bilibili.com/video/BV1AY4y157yL/?spm_id_from=333.337.search-card.all.click&vd_source=10b3ee1feea99fbdd76960a61a32f71e)

next数组存储可以跳过的内容 

![](assets/Pasted%20image%2020241011223511.png)

如上图，第五个字符无法匹配，那么前四个可以，next[4]表明可以跳过两个字符

![](assets/Pasted%20image%2020241011223606.png)
next数组的本质就是找子串中相同前后缀的长度

[板题](https://www.luogu.com.cn/problem/P3375)

```CPP
#include <bits/stdc++.h>
using namespace std;

#define endl "\n"
#define IOS ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
#define rep(i, j, k) for (int i = (j); i < (k); i++)
#define all(x) x.begin(), x.end()
#define pii pair<int, int>
const int N = 1e6 + 10;

// next数组表示可以跳过的字符
int nt[N];
string s, t;
void build() {
    nt[0] = 0;
    int i = 1;
    int nlen = 0;
    int tlen = t.length();
    while (i < tlen) {
        if (t[nlen] == t[i]) { // 如果当前前缀长度和i相等，说明前后缀加1
            nlen++;
            nt[i] = nlen;
            i++;
        } else {             // 如果不相等 查表看看有没有更短的前后缀
            if (nlen == 0) { // 如果依然没有，那么就继续,当前nt为0
                nt[i] = 0;
                i++;
            } else
                nlen = nt[nlen - 1];
        }
    }
}

vector<int> ans;

void kmp() {
    int i = 0; // 主串中的指针
    int j = 0; // 子串中的指针
    while (i < s.length()) {
        if (s[i] == t[j]) { // 如果匹配上了
            i++;
            j++;
        } else if (j > 0) { // j看看能不能不完全回退
            j = nt[j - 1];
        } else { // j回退到0，还是不能匹配上，那么i++
            i++;
        }
        if (j == t.length()) { // 匹配成功，为了题意要求，还需继续匹配
            ans.push_back(i - j);
            i = i - j + 1;
            j = 0;
        }
    }
}

int main() {
    IOS;
    cin >> s >> t;
    build();
    kmp();

    for (int i : ans) cout << i + 1 << endl;
    for (int i = 0; i < t.length(); i++) cout << nt[i] << ' ';
    return 0;
}
```

相关内容：
- [[字符串匹配]]
- [[AC自动机]]
