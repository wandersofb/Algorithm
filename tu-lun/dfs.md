# DFS

* 深度优先搜索，虽然很熟悉了，但还是写一下模板，以后回忆的时候更方便一点
* 在我有限的理解下，深度优先搜索简单来说，就是多重递归，更新状态，减枝，保存结果。
* 关键点时在于抽象题目的到深度优先搜索的状态表示。

**全排列**

经典问题，求 $$n$$ 的全排列，按照字典序输出。

```
#include<iostream>
#include<algorithm>
#include<vector>
using namespace std;

void dfs(int n, vector<vector<int>> &res, vector<int> order, int state){
    if (order.size() == n){
        res.push_back(order);
        return;
    }
    
    for (int i = 1; i <= n; i ++){
        if (!(state >> i & 1)){       // 二进制表示状态并减枝
            order.push_back(i);
            dfs(n, res, order, state + (1 << i));
            order.pop_back();
        }
    }
}

int main(){
    int n; cin >> n;
    vector<vector<int>> res;     // 结果集
    vector<int> order;           // 序列
    dfs(n, res, order, 0);
    
    for (int i = 0; i < res.size(); i ++){
        for (int j = 0; j < n; j ++){
            cout << res[i][j] << ' ';
        }
        cout << endl;
    }
}
```

**时间复杂度分析**

画出递归树可以分析得$$n + n(n - 1) + \cdots + n(n - 1)*\cdots * 2 * 1$$，$$O(n * n!)$$

**八皇后**

八皇后也是经典问题。关键是搞懂条件判断如何写就很好办了。

```
#include<iostream>
#include<algorithm>
#include<vector>
using namespace std;
const int N = 10;
vector<pair<int, int>> p;

void dfs(int n, int r){
    if (r == n + 1){
        for (auto t: p){
            int x = t.second;
            for (int i = 1; i <= n; i ++){
                if (x == i) cout << 'Q';
                else cout << '.';
            }
            cout << endl;
        }
        cout << endl;
        return;
    }
    
    for (int i = 1; i <= n; i ++){
        int flag = 1, choice = 1;
        for (auto t: p){
            int x = t.second, y = t.first;
            if (x == i || x + r - y % n == i || x - (r - y) % n == i){
                flag = 0;
                break;
            }
        }
        if (flag){
            p.push_back({r, i});                
            dfs(n, ++ r);
            p.pop_back();
            r --;
        }
    }
}

int main(){
    int n; cin >> n;
    dfs(n, 1);
}
```

**时间复杂度**

递归树 $$n + n*(n-k) + n *(n - k_1) * (n - k_2) * \cdots * (n-k_x)$$，最坏时间复杂度为 $$O(n!)$$。

#### 总结

其实 DFS 主要问题在条件判断和剪枝上。
