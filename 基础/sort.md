# sort

* 排序是个老生常谈的问题了，本篇旨在收集所有的排序算法并把板子写一遍。
* 基于比较模型的算法的时间复杂度最低为 $$O(n \log n)$$。
* 当然也有优化为线性的算法，不过需要额外信息。

**快排**

快排的思想是基于分治的思想，是在元素中找一个元素当轴，把小于轴的都移到左边，把大于轴的都移到右边。如何移动？**双指**。其时间复杂度为 $$O(n \log n)$$。

```cpp
#include<iostream>
#include<algorithm>
using namespace std;

const int N = 1e5 + 10;
int p[N];

void quick_sort(int l, int r){
    if (l >= r) return;
    
    int t = p[l + r >> 1], i = l - 1, j = r + 1;   // t = p[ l + r + 1 >> 1]
    while (i < j){
        do i ++; while ( p[i] < t);
        do j --; while ( p[j] > t);
        if (i < j) swap(p[i], p[j]);
    }
    quick_sort(l, j), quick_sort(j + 1, r); // l, i - 1    i, r
}

int main(){
    int n; cin >> n;
    for (int i = 0; i < n; i ++) scanf("%d", &p[i]);
    
    quick_sort(0, n - 1);
    
    for (int i = 0; i < n; i ++) printf("%d ", p[i]);
}
```

**归并**

归并的思想也是分治和回溯，时间复杂度也是 $$O(n \log n)$$，不过相较于快排，其需要额外空间。

```cpp
#include<iostream>
#include<algorithm>
using namespace std;

const int N = 1e5 + 10;

int p[N], tmp[N];

void merge_sort(int l, int r){
    if (l >= r) return;
    int mid = l + r >> 1;
    merge_sort(l, mid), merge_sort(mid + 1, r);
    int x = l, y = mid + 1, t = 0;
    while (x < mid + 1 && y < r + 1){
        if (p[x] <= p[y]){
            tmp[t ++ ] = p[x];
            x ++;
        } 
        if (p[x] > p[y]){
            tmp[t ++] = p[y];
            y ++;
        }
    }
    while (x < mid + 1) tmp[t ++] = p[x ++];
    while (y < r + 1) tmp[t ++] = p[y ++];
    for(int i = l, j = 0; i < r + 1; i ++, j ++) p[i] = tmp[j];
}


int main(){
    int n; cin >> n;
    for (int i = 0; i < n; i ++) scanf("%d", &p[i]);
    
    merge_sort(0, n - 1);
    
    for (int i = 0; i < n; i ++) printf("%d ", p[i]);
}
```

**基数**

基数排序的思想是一位一位的排，先排最后一位，再以此往前。时间复杂度 $$O(n * k)$$。

图源[此处](https://leetcode.cn/problems/maximum-gap/solution/c-ji-shu-pai-xu-guan-fang-dai-ma-zhu-shi-by-lljj54/)

![](../.gitbook/assets/radix\_sort.gif)

```cpp
    void radixSort(vector<int> &nums) {
        int n = nums.size();
        // 复制数组
        vector<int> buf(n);
        // 基数
        int radix = 1;
        // 最大值
        int u = *max_element(nums.begin(), nums.end());
        int k = maxBit(u);
        while (k --) {
            vector<int> bucket(10);
            // 找到每个桶放多少个值
            for (int i = 0; i < n; i ++) {
                int k = (nums[i] / radix) % 10;
                bucket[k] ++;
            }
            // 索引
            for (int i = 1; i < 10; i ++) {
                bucket[i] += bucket[i - 1];
            }
            // 防值
            for (int i = n - 1; i >= 0; i --) {
                int k = (nums[i] / radix) % 10;
                buf[bucket[k] - 1] = nums[i];
                bucket[k] --;
            }
            copy(buf.begin(), buf.end(), nums.begin());
            radix *= 10;
        }
    } 
    int maxBit(int a) {
        int n = 0;
        while(a) {
            n ++;
            a /= 10;
        } 
        return n;
    }
```
