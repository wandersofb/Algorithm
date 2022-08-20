# 线性筛



#### 线性筛

线性筛是一种时间复杂度为 $$O(n)$$ 的算法，优于埃氏筛法的时间复杂度 $$O(n \log \log n)$$ 。在 $$10^7$$ 的数据量，二者运算时间会有接近一半的差距。

```cpp
#include<iostream>
#include<algorithm>
using namespace std;
const int N = 1e6;
int p[N], cnt;
bool tou[N];

void prime(int n){
    for (int i = 2; i <= n; i ++){
        if (!tou[i]) p[cnt++] = i;  //如果i是质数也就是说没被touch过
        for (int j = 0; p[j] <= n / i; j ++){
            tou[p[j] * i] = true;    
            if (i % p[j] == 0) break;
        }
    } 
}

int main(){
    int n; cin >> n;
    prime(n);
    cout << cnt;
}
```

**时间复杂度**

**线性筛为什么是线性的?** 其中第一重循环的时间为 $$n$$ ，第二重循环的作用为用最小质因子筛掉所有的合数，也就是说每一个数我只筛一次，均摊到第一重循环的时间消耗为 $$1$$ , 那么总共的时间复杂度为 $$O(n)$$。

* 线性筛代码分析 **为什么第二重循环的作用是用最小的质因子筛掉所有的合数？**

```cpp
for (int j = 0; p[j] <= n / i; i ++){
    tou[p[j] * i] = true;    
    if (i % p[j] == 0) break;
}
```

* y总的讲解说的很清楚&#x20;

![](../.gitbook/assets/LinerSieve\_main.png)

* 个人分析 首先可以分析我们想筛掉的数为`p[j] * i`。关键点是分清`p[j]`和`i`以及`p[j] * i`的质因数关系。 `p[j]`为我们已经找到的质数,`p[j]`就是`p[j] * i`的质因子。**如何保证为最小质因子？** 后一句代码 `i % p[j] == 0`的含义是当`p[j]`又是`i`的最小质因子时，我们break走。**为什么当`p[j]`同时是`i`和`p[j] * i`的最小质因子break那?** 因为一旦`p[j]`大于`i`的最小质因子，很明显`p[j] * i`的最小质因子就是`i`的最小质因子了，我们想要的就不是`p[j]`了。
* 举个例子：`p[j] = 2`和`i = 4`那么筛掉8，此时应该break走。但是让`p[j] = 3`，`p[j] * i = 12`，最小质因子为`i`的最小质因子2，很明显不应该筛。

#### 总结

线性筛初次理解很难受，需要多次理解。其作用还有很多，不限于筛质数，等以后再做探讨。

#### 附录

埃氏筛法代码

```cpp
#include<iostream>
#include<algorithm>
using namespace std;
const int N = 1e6;
int p[N], cnt;
bool tou[N];

void prime(int n){
    for (int i = 2; i <= n; i ++){
        if(!tou[i]){
            p[cnt ++] = i;  
            for (int j = i + i; j <= n ; j += i) //筛掉质数的倍数
                tou[j] = true; 
        } 
         
    } 
}

int main(){
    int n; cin >> n;
    prime(n);
    cout << cnt;
}
```
