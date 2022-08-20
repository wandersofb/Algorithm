# 区间DP

最近碰到几个区间 DP 问题，特此记录一下。

### [AcWing: 282. 石子合并](https://www.acwing.com/problem/content/284/)

题就不赘述了，就是给一堆石子，只能相邻的合并，每次合并之和是所要消耗的代价，问最优情况。一道经典区间 DP 问题。

区间 DP 的思路并不难，但是第一个很难想，就是步骤是枚举区间再枚举分界点，左右是最优的，合并左右就行了。

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 310;
int p[N];
int f[N][N];

int main() {
    int n; cin >> n;
    for (int i = 1; i <= n; i ++) {
        cin >> p[i], p[i] += p[i - 1];
    }
    for (int len = 2; len <= n; len ++) {
        for (int i = 1; i + len - 1 <= n; i ++) {
            int j = i + len - 1;
            f[i][j] = 1e8;
            for (int k = i; k <= j; k ++) {
                f[i][j] = min(f[i][j], f[i][k] + f[k + 1][j] + p[j] - p[i - 1]);
            }
        } 
    }
    cout << f[1][n] << endl;
    return 0;
}
```

### [LeetCode: 312. 戳气球](https://leetcode.cn/problems/burst-balloons/)

题目是给一堆气球，让我们戳破气球，戳一次消耗的价值是左气球 * 被戳气球 * 右气球，求最优。

`f[i][j]` 的定义是在 `i + 1` 和 `j - 1` 的区间下所戳破气球的最优值。

```cpp
class Solution {
public:
    int maxCoins(vector<int>& nums) {
        int n = nums.size();
        vector<int> a(n + 2, 1);
        for (int i = 1; i <= n; i ++) a[i] = nums[i - 1];
        vector<vector<int>> f(n + 2, vector<int>(n + 2, 0));
        for (int len = 3; len <= n + 2; len ++) {
            for (int i = 0; len + i - 1 <= n + 1; i ++) {
                int j = len + i - 1;
                for (int k = i + 1; k < j; k ++) {
                    f[i][j] = max(f[i][j], f[i][k] + f[k][j] + a[k] * a[i] * a[j]);
                }
            }
        }
        return f[0][n + 1];
    }
};
```

### [LeetCode: 375. 猜数字大小 II](https://leetcode.cn/problems/guess-number-higher-or-lower-ii/)

`f[i][j]` 是 `i` 到 `j` 的数字中所有目标值的所有种选法的最优值。

```cpp
class Solution {
public:
    int getMoneyAmount(int n) {
        vector<vector<int>> f(n + 2, vector<int>(n + 2));
        for (int len = 2; len <= n; len ++) {
            for (int i = 1; i + len - 1 <= n; i ++) {
                int j = i + len - 1;
                f[i][j] = 1e8;
                for (int k = i; k <= j; k ++) {
                    f[i][j] = min(f[i][j], max(f[i][k - 1], f[k + 1][j]) + k);
                }
            }
        }
        return f[1][n];
    }
};
```