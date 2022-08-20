# BFS

BFS 也是经典板子了。BFS 自带最短路性质，可以用来求某些需要最短路的题。用到的数据结构有队列来保存状态，可能会用到哈希表来去重。

**走迷宫**

> 给定一个 $$n×m$$ 的二维整数数组，用来表示一个迷宫，数组中只包含 $$0$$ 或 $$1$$，其中 $$0$$ 表示可以走的路，$$1$$ 表示不可通过的墙壁。最初，有一个人位于左上角 $$(1,1)$$ 处，已知该人每次可以向上、下、左、右任意一个方向移动一个位置。请问，该人从左上角移动至右下角 $$(n,m)$$ 处，至少需要移动多少次。数据保证 $$(1,1)$$ 处和 $$(n,m)$$ 处的数字为 $$0$$，且一定至少存在一条通路。

**输入格式**

> 5 5 0 1 0 0 0 0 1 0 1 0 0 0 0 0 0 0 1 1 1 0 0 0 0 1 0

```cpp
#include<iostream>
#include<queue>
#include<cstring>

using namespace std;
const int N = 105;
int p[N][N], d[N][N];
int n, m;
queue<pair<int,int>> que;

int bfs(){
    memset(d, -1, sizeof d);
    d[0][0] = 0;
    int a[4] = {-1, 0, 1, 0}, b[4] = {0, -1, 0, 1};
    while (!que.empty()) {
        auto t = que.front();
        que.pop();
        for (int i = 0; i < 4; i++){
            int x = t.first + a[i], y = t.second + b[i];
            if (x >= 0 && x < n && y >= 0 && y < m && d[x][y] == -1&& p[x][y] == 0){
                que.push({x, y});
                d[x][y] = d[t.first][t.second] + 1;
            }
        }
    }
    return d[n-1][m-1];
}

int main(){
    scanf("%d%d", &n, &m);
    for (int i = 0; i < n; i ++) for (int j = 0; j < m; j ++) scanf("%d", &p[i][j]);
    que.push(make_pair(0,0));
    cout << bfs();
}
```

**八数码**

经典问题，拼图问题。重点是在对公共变量修改后要返回状态。

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
#include <queue>
#include <unordered_set>
using namespace std;

queue<pair<pair<int, int>, string>> que;
unordered_set<string> seen;
string ggg = "12345678x";

int bfs(){
    int cx[4] = {-1, 0, 1, 0}, cy[4] = {0, 1, 0, -1};
    
    while (que.size()){
        auto a = que.front();
        que.pop();
        int x = a.first.first % 3, y = a.first.first / 3;
        int res = a.first.second;
        string s = a.second;
        seen.insert(s);
        if (s == ggg) return res;
        
        for (int i = 0; i < 4; i ++){
            int dx = x + cx[i], dy = y + cy[i];
            if (dx >= 0 && dx < 3 && dy >= 0 && dy < 3){
                int b = dy * 3 + dx;
                swap(s[a.first.first], s[b]);
                if (seen.find(s) == seen.end()){
                    res ++;
                    que.push({{b, res}, s});
                    res --;
                }
                swap(s[a.first.first], s[b]);
            }
        }
    }
    return -1;
}

int main(){
    string s; 
    getline(cin, s);
    string c;
    for (int i = 0; i < s.size(); i ++){
        if (s[i] != ' ') c.push_back(s[i]);
    }
    
    que.push({{c.find('x'), 0}, c});
    cout << bfs();
}
```

#### 总结

BFS 算是很简单的算法了，就是代码有点长，无权最短路可以用，队列保存，对公共变量修改后要再改回来。
