# 贪心

此篇是记录关于贪心算法的总结。

### 贪心

思想就不用说了，局部最优解嘛\~，直接看题。

#### 区间选点

> 给定 $$N$$ 个闭区间 $$[a_i,b_i]$$，请你在数轴上选择尽量少的点，使得每个区间内至少包含一个选出的点。 输出选择的点的最小数量。 位于区间端点上的点也算作区间内。

题意是给定一系列区间，选取尽可能少的点来覆盖所有的区间。

* 怎么说那？先排序，以区间右端点从小到大排序。
* 分析一下，我们怎么选点。
  * 针对第一个区间我们为了收益最高，肯定选右端点。
  * 然后对于后续的区间，只要我选取的点还在你的区间内，不选。如果不在你的区间内，那就继续选取右端点。

这可以完美覆盖所有区间，并且找到最少的选取点数。

```cpp
#include<iostream>
#include<algorithm>
using namespace std;

const int  N = 1e5 + 5;

struct range{
    int l, r;
    bool operator< (const range &a) const{
        return r < a.r;
    }
}Range[N];

int main(){
    int n; cin >> n;
    for (int i = 0; i < n; i ++){
        cin >> Range[i].l >> Range[i].r;
    }    
    sort(Range, Range + n);

    int cnt = 0, ed = -2e9; // 记录最后选取的点
    for (int i = 0; i < n; i ++)
        if (Range[i].l > ed){
            cnt ++;
            ed = Range[i].r;  // 如果左端点大于我选取的点
        }

    cout << cnt << endl;
}
```

#### 最大不相交区间数量

> 给定 $$N$$ 个闭区间 $$[a_i,b_i]$$，请你在数轴上选择若干区间，使得选中的区间之间互不相交（包括端点）。 输出可选取区间的最大数量。

本题的代码完全相等于上题。上题说的是选取最少的点来覆盖所有区间，那么这些点所代表的就是最大不相交的区间数量。

#### 区间分组

> 给定 $$N$$ 个闭区间 $$[a_i,b_i]$$，请你将这些区间分成若干组，使得每组内部的区间两两之间（包括端点）没有交集，并使得组数尽可能小。 输出最小组数。

本题可以抽象成**活动安排**问题，假设有 $$n$$ 个活动其时间要求为一个区间来安排教室，求最小的教室数量。

* 先排序，以左端点从小到大排序。
* 再给区间分配组。组记录区间的右端点。
  * 当组数为空时，直接分配一个组。
  * 当我要选取的区间的左端点小于组的最小值，那么需要给他分配新的组。
  * 当我要选取的区间的左端点大于组的最小值，那么直接放到这个组就行了。

观察分析过程，我们维护的组其实是一个堆，我们只取其中的最值，并且在算法过程中堆要不断的调整自己的最值。

```cpp
#include<iostream>
#include<algorithm>
#include<queue>
using namespace std;

const int N = 1e5 + 10;

struct range{
    int l, r;
    bool operator< (const range &a) const {
        return l < a.l;
    }
}Range[N];

int main(){
    int n; cin >> n;

    for (int i = 0; i < n; i ++) cin >> Range[i].l >> Range[i].r;

    sort(Range, Range + n);
    priority_queue<int, vector<int>, greater<int>> heap;     
    for (int i = 0; i < n; i ++){
        if (heap.empty() || heap.top() >= Range[i].l) heap.push(Range[i].r);
        else{
            heap.pop();
            heap.push(Range[i].r);
        }
    }

    cout << heap.size() << endl;

}
```

#### 区间覆盖

> 给定 $$N$$ 个闭区间 $$[a_i,b_i]$$ 以及一个线段区间 $$[s,t]$$，请你选择尽量少的区间，将指定线段区间完全覆盖。 输出最少区间数，如果无法完全覆盖则输出 −1。

题意是选取尽可能少的区间来覆盖我们的目标区间。

* 排序。以右端点从小到大排序。
* 选取。考虑覆盖左端点的区间们，我们一定是选取右端点最大的那个区间，这样收益最大。

```cpp
#include<iostream>
#include<algorithm>
using namespace std;

const int N = 1e5 + 10;

struct range{
    int l, r;
    bool operator< (const range &a) const {
        return l < a.l;
    }
}Range[N];

int main(){
    int al, ar; cin >> al >> ar;
    int n; cin >> n;

    for (int i = 0; i < n; i ++) cin >> Range[i].l >> Range[i].r;

    sort(Range, Range + n);

    int res = 0;
    bool suc = false;
    for (int i = 0; i < n; i ++){
         int j = i, r = -2e9;
         while (j < n && Range[j].l <= al){
             r = max(r, Range[j].r);
             j ++;
         }
         if (r < al)
            break;
         al = r;
         res ++;
         if (ar <= al){
             suc = true;
             break;
         }
         i = j - 1;
    }

    if (suc) cout << res << endl;
    else cout << -1 << endl;

}
```
