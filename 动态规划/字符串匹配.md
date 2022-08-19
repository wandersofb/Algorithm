# 字符串匹配

由于刷 leetcode 的时候，碰到了两个比较头大的动态规划的题，恰巧是同一类型，特此记录一下。

### [10. 正则表达式匹配](https://leetcode.cn/problems/regular-expression-matching/)
> 给你一个字符串 `s` 和一个字符规律 `p`，请你来实现一个支持 `'.'` 和 `'*'` 的正则表达式匹配。
> * `'.'` 匹配任意单个字符
> * `'*'` 匹配零个或多个前面的那一个元素
> 
> 所谓匹配，是要涵盖 **整个** 字符串 `s` 的，而不是部分字符串。

动态规划老办法。
* 状态表示 $$f(i, j)$$ 表示 `s` 的 $$1 \sim i$$ 和 `p` 的 $$1 \sim j$$ 的匹配情况。
* 状态计算可分为两种情况:
  1. 当 `p[j] != '*'`，考虑 `p[j] == '.'` 或 `p[j] == s[i]` 以及 `p[j - 1] == s[i - 1]` 有关。
  2. 当 `p[j] == '*'`，要判断 `p[j - 1]` 会重复几次，重复零次要看 `f[i][j - 2]`，重复一次要看 `f[i - 1][j - 2] && p[j - 1] == s[i]`，重复两次要看 `f[i - 2][j - 1] && p[j - 1] == s[i] && p[j - 1] == s[i - 1]`，故可归为：
    $$
  f(i,j) = f(i, j - 2) || f(i - 1, j - 2) \&\& s[i] ||\cdots
  $$
  考虑到完全背包优化，上式可写成：
  $$
  f(i,j) = f(i, j - 2) || f(i - 1, j) \&\& s[i]
  $$

```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        int n = s.size(), m = p.size();
        vector<vector<bool>> f(n + 1, vector<bool>(m + 1, 0));
        s = ' ' + s, p = ' ' + p;
        f[0][0] = true;
        for (int i = 0; i <= n; i ++) {
            for (int j = 1; j <= m; j ++) {
                if (j + 1 < m && p[j + 1] == '*') continue;
                if (p[j] != '*') {
                    f[i][j] = (p[j] == '.' || p[j] == s[i]) && i && f[i - 1][j - 1];
                } else {
                    f[i][j] = f[i][j - 2] || i && f[i - 1][j] && (s[i] == p[j - 1] || p[j - 1] == '.');
                } 
            }
        }
        return f[n][m];
    }
};
```

### [44. 通配符匹配](https://leetcode.cn/problems/wildcard-matching/)
> 给定一个字符串 `s` 和一个字符模式 `p` ，实现一个支持 `'?'` 和 `'*'` 的通配符匹配。
> * `'?'` 可以匹配任何单个字符。
> * `'*'` 可以匹配任意字符串（包括空字符串）。
> 
> 两个字符串完全匹配才算匹配成功。
说明:
> * `s` 可能为空，且只包含从 `a-z` 的小写字母。
> * `p` 可能为空，且只包含从 `a-z` 的小写字母，以及字符 `?` 和 `*`。

* 状态表示 $$f(i, j)$$ 表示 `s` 的 $$1 \sim i$$ 和 `p` 的 $$1 \sim j$$ 的匹配情况。
* 状态计算和上面一样分两种情况：
  1. 当 `p[j] != '*'`，考虑 `p[j] == '?'` 或 `p[j] == s[i]` 以及 `f[i - 1][j - 1]`。
  2. 当 `p[j] == '*'`，其实和上题也一样，考虑 `p[j]` 要匹配多少个。当为零时要看 `f[i][j - 1]`，当为一时要看 `f[i - 1][j - 1]`，当为二时要看 `f[i - 2][j - 1]`。依旧按照背包思路优化，可写成：
   $$
   f(i, j) = f(i, j - 1) || f(i - 1, j)
   $$

```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        int n = s.size(), m = p.size();
        vector<vector<bool>> f(n + 1, vector<bool>(m + 1, false));
        s = ' ' + s, p = ' ' + p;
        f[0][0] = true;
        for (int i = 0; i <= n; i ++) {
            for (int j = 1; j <= m; j ++) {
                if (p[j] != '*') {
                    f[i][j] = (p[j] == s[i] || p[j] == '?') && i && f[i - 1][j - 1];
                } else {
                    f[i][j] = f[i][j - 1] || i && f[i - 1][j];
                }
            }
        }
        return f[n][m];
    }
};
```