# 欧拉函数

#### 欧拉函数

**互质**

在介绍欧拉函数之前，要先看一个概念-互质。互质是公约数只有 1 的的两个数。如果 $$a \equiv 1 \pmod b$$，那么 a 和 b 互质。 在约数那章已经说过了，算术基本定理为 $$N = p_1^{a_1}p_2^{a_2}\cdots p_n^{a_n}$$。好的，在知道了这两个概念后就可以介绍欧拉函数了。

**欧拉函数定义**

> $$1 \sim N$$ 中与 $$N$$ 互质的数的个数被称为欧拉函数，记为 $$\phi(N)$$。可表示为

$$
\phi(N) = N * {p_1 - 1 \over p_1} * {p_2 - 1\over p_2} * \cdots * {p_m - 1\over p_m}
$$

**如何求欧拉函数**

简单的分解质因数可以做到求解这个函数。也可以用线性筛的方法求解从 $$1 \sim N$$ 每个数的欧拉函数。

**分解质因数求欧拉函数**

分析这个函数，求出 $$N$$ 所有质数即可求出它的欧拉函数。

```
#include<iostream>
#include<algorithm>
#include<vector>
using namespace std;

int eulor(int m){
    int res = m;
    for (int i = 2; i <= m / i; i ++){
        if (m % i == 0){
            while (m % i == 0)
                m /= i;
            res = res / i * (i - 1); // 先/后×，避免乘法溢出
        }
    }
    if ( m > 1) res = res / m * (m - 1);
    return res;
}

int main(){
    int n; cin >> n;
    while (n -- ){
        int m; cin >> m;
        cout << eulor(m) << endl;
    }
}
```

**时间复杂度分析**

和分解质因数一样，eulor 函数的时间复杂度为 $$O(\sqrt m)$$，总时间复杂度 $$O(n \sqrt m)$$。

**线性筛求欧拉函数**

线性筛不是求单个数的欧拉函数，它可以求出 $$1 \sim n$$ 中所有数的欧拉函数。 下面我们分析一下欧拉函数在线性增长的情况。

* 如果 $$i$$ 是质数，那么由公式可以看出从$$1 \sim i$$ 中为它互质的数 $$i-1$$。
* 如果 `i % p[j] != 0`，$$i$$ 是 $$i * p[j]$$ 的质因子，那么

$$
\\ \phi(p[j] * i) = p[j] * i * {q_1 - 1\over q_1} * \cdots * {q_n - 1\over q_n} * {p[j] - 1 \over p[j]}
$$

* 如果 `i % p[j] == 0`，$$i$$ 不是 $$i * p[j]$$ 的质因子，那么

$$
\phi(p[j] * i) = p[j] * i * {q_1 - 1\over q_1} * \cdots * {q_n - 1\over q_n}
$$

```
#include<iostream>
#include<algorithm>
using namespace std;
const int N = 1e6 + 10;
int p[N], phi[N], cnt;
bool tou[N];

void eulor(int n){
    phi[1] = 1;
    for (int i = 2; i <= n; i ++){
        if (!tou[i]) {
            p[cnt++] = i;
            phi[i] = i - 1;
        }
        for (int j = 0; p[j] <= n / i; j ++){
            tou[p[j] * i] = true;
            if (i % p[j] == 0){
                phi[i * p[j]] = p[j] * phi[i];        
                break;
            }
            phi[i * p[j]] = (p[j] - 1) * phi[i];
        }
    }  
}

int main(){
    int n; cin >> n;
    eulor(n);
    long long res = 0;
    for (int i = 1; i <= n; i ++)
        res += phi[i];
    cout << res;
}
```

**时间复杂度分析**

同线性筛一样 $$O(n)$$。

**欧拉函数总结**

欧拉函数是求互质的数的个数，至于具体的使用场景，等以后发现了补充。
