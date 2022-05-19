# 约数

约数就是因数。主要记录一下约数个数、约数之和以及最大公约数的计算方法。

**如何求约数**

* 试除法是一个很好的办法。这个算法的大致意思是让小于 n 的数都和 n 相除，如果余数为零可以判断为合数。
* 有了大致思路还不行，还可以优化。合数都是成对出现的，对吧。我们用 n 除以 i，余数为零，那么商也是另一个约数。

```
#include<iostream>
#include<algorithm>
#include<vector>
using namespace std;

int main(){
    int n; cin >> n;
    while (n -- ){
        int m; cin >> m;
        vector<int> p;
        for (int i = 1; i <= m / i; i ++){
            if (m % i == 0){
                p.push_back(i);
                if (m / i != i) p.push_back(m / i);
            }
        }
        sort(p.begin(), p.end());
        for (int i = 0; i < p.size(); i ++)
            cout << p[i] << ' ';
        cout << endl;
    }
}
```

**时间复杂度分析**

最外层时间为 $$n$$，内层循环是 $$\sqrt {m}$$。还有一个排序 $$n \log n$$，但是这个排序的时间是远小于 $$\sqrt {m}$$。为什么那？排序的 $$n$$ 也就是每个数的合数的个数，从 $$1 \sim n$$ 大概有 $$n\ln n$$ 个合数，平均到每个数大概有 $$\ln n$$ 个合数（我们要计算的是个数），那么排序的实现复杂度为 $$\ln n \log {\ln n}$$，小于 $$\sqrt {m}$$。所以总的时间复杂度为 $$O(n\sqrt m)$$。

#### 约数个数

给定一个正整数怎么求约数的个数，此时需要一个定理，伟大的欧几里得数学家曾发现的。

**算术基本定理**

任何一个正整数且不是质数的数 $$N$$ 都可以分解为 $$N = p_1^{a_1} * p_2^{a_2} * \cdots * p_n^{a_n}$$ ，其中任意一个 $$p$$ 都是质数且 $$p_1 \lt p_2 \lt \cdots \lt p_n$$ 。这是 $$N$$ 的标准分解式。

**原理分析**

任何一个 $$N$$ 可以分解为 $$p_1^{a_1} * p_2^{a_2} * \cdots * p_n^{a_n}$$，而任何一个 $$N$$ 的合数都可以表示为$$p_1^{b_1} * p_2^{b_2} * \cdots * p_n^{b_n}$$，其中 $$0 <= b_i <= a_i$$。那么 $$N$$ 的合数的个数不就是一个组合数 $$\prod_{i=1}^{n}(1+a_i)$$,我们按照算术基本定理计算就可。

```
#include<iostream>
#include<algorithm>
#include<unordered_map>

using namespace std;
const int N = 1e9 + 7;

int main(){
    int n; cin >> n;
    long long res = 1;
    unordered_map<int, int> hs;
    while (n -- ){
        int m; cin >> m;
        for (int i = 2; i <= m / i; i ++){ //枚举到 i*i <= m
            if (m % i == 0){
                while (m % i == 0){
                    hs[i] ++;
                    m /= i;
                }
            } 
        }
        if (m > 1) hs[m]++; // 只存在一个大于根号m的数是m的分解质数
    }
    for (auto prime : hs) res = res * (prime.second + 1) % N;
    cout << res;
}
```

**时间复杂度分析**

最外层时间 $$n$$，里层 for 循环时间为 $$\sqrt m$$, 最里层 while 循环均摊到 for 循环的时间为 $$1$$，所以总的时间复杂度 $$O(n * \sqrt m)$$。

#### 约数之和

给定一个正整数，求他的约数的和。

**原理分析**

由算术基本定理可得，一个正整数可以分解为 $$N = p_1^{a_1} * p_2^{a_2} * \cdots * p_n^{a_n}$$，而任何一个 $$N$$ 的合数都可以表示为 $$p_1^{b_1} * p_2^{b_2} * \cdots * p_n^{b_n}$$，其中 $$0 <= b_i <= a_i$$。那么约数之和可以表示为 $$(p_1^1 + p_1^2 + \cdots + p_1^{a_1}) * \cdots * (p_n^1 + p_n^2 + \cdots + p_n^{a_n})$$，可以简洁的表示为 $$\prod_{i = 1}^n {\sum_{j = 1}^{a_i} p_i^j}$$

```
#include<iostream>
#include<algorithm>
#include<unordered_map>
using namespace std;
const int N = 1e9 + 7;
long long horner(int p, int x){
    long long res = 1;
    for (int i = 0 ; i < x; i ++){
        res = (res * p + 1) % N;
    }
    return res;
}

int main(){
    int n ; cin >> n;
    unordered_map<int, int> primes;
    while (n -- ){
        int m ; cin >> m;
        for (int i = 2; i <= m / i; i ++){
            if (m % i == 0){
                while (m % i == 0){
                    primes[i] ++;
                    m /= i;
                }
            }
        }
        if (m > 1) primes[m] ++;
    }
    long long res = 1;
    for (auto prime : primes){
        res = res * horner(prime.first, prime.second) % N; 
    }
    cout << res;
}
```

值得注意的是，在计算单独的质因数的合数时，也就是horner函数使用的是秦九韶算法，并非等比数列求和。原因在于可以会溢出，使用秦九韶算法可以每次计算时mod一次，不会溢出。此处补充一个代数系统下的公式 $$(a * b) mod N = (a \ mod N * b \ mod N) mod N$$。

**时间复杂度分析**

分解质因数的时间复杂度同约数个数为 $$O(n \sqrt m)$$，至于求解之和的过程，比较难计算，在此先忽略了。（哈哈）

#### 最大公约数

最大公约数就比较简单了，小学都学过，辗转相除法计算，就不多说了。

```
#include<iostream>
#include<algorithm>
using namespace std;

int gcd(int a ,int b){
    return a == 0 ? b : gcd( b % a, a);
}

int main(){
    int n; cin >> n;
    while (n -- ){
        int a, b; cin >> a >> b;
        cout << gcd(a, b) << endl;
    }
}
```

**时间复杂度分析**

递归函数的时间复杂度，一般用 **master theorem** 来分析，可讲的就多了，新开另一个来说。但这里又引入了 mod 运算，主定理也解决不了，可就难的多了。这里贴一个大佬的[分析](https://blog.csdn.net/xiamentingtao/article/details/44702869)。

#### 总结

约数这一小结不难，理解算术基本定理如何解决问题的就行了。
