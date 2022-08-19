# 快速幂

* 快速幂顾名思义就是快速的求出一个数的幂。通常我们求幂，无非相乘多次，但快速幂会将求幂算法从线性级别降到对数级别。
* 可以把指数的看成二进制，有1的位相乘即可。

```cpp
#include<iostream>
#include<algorithm>
using namespace std;
typedef long long LL;
int qpow(int a, int b, int p){
    int res = 1;
    while (b){
        if (b & 1) res = res * (LL)a % p;
        a = a * (LL)a % p ;
        b >>= 1;
    }
    return res;
}

int main(){
    int n; cin >> n;
    while (n -- ){
        int a, b, p; cin >> a >> b >> p;
        cout << qpow(a, b, p) << endl;        
    }
}
```

**时间复杂度分析**

就如同我们之前分析，while 循环会循环 b 的位数。b 的位数是 $$\log b$$，我们的时间复杂度为 $$O(n \log b)$$。

**快速幂求逆元**

> 若整数 $$b$$，$$m$$ 互质，并且对于任意的整数 $$a$$，如果满足 $$b|a$$，则存在一个整数 $$x$$，使得 $$a/b \equiv a*x\pmod m$$，则称 x 为 b 的模 m 乘法逆元，记为$$b^{-1}\pmod m$$。b 存在乘法逆元的充要条件是 b 与模数 m 互质。当模数 m 为质数时，$$b^{m−2}$$ 即为 b 的乘法逆元。

* 需要说明的是 $$m$$ 为质数，当然不是质数也可求，只不过不用快速幂来求。稍微证明一下上式，需要用到费马小定理。$$a/b \equiv a*x\pmod m$$ 左右同乘 $$b$$ 可得 $$a \equiv a*x*b\pmod m$$ 也就是 $$1 \equiv x*b\pmod m$$。由费马小定理可知当 $$m$$ 为质数时

$$
b^{m - 1} \equiv 1 \pmod m
$$

我们拆出一个 $$b$$ 可得 $$b * b^{m - 2} \equiv 1 \pmod m$$。二者对比可发现 $$x = b^{m - 2}$$

* 看懂上面的话，我们就把求逆元的问题转化为一个求幂的问题。

```cpp
#include<iostream>
#include<algorithm>
using namespace std;
typedef long long LL;
int qpow(int a, int b, int p){
    int res = 1;
    while (b){
        if (b & 1) res = res * (LL)a % p;
        a = a * (LL)a % p ;
        b >>= 1;
    }
    return res;
}

int main(){
    int n; cin >> n;
    while (n -- ){
        int a, p; cin >> a >> p;
        if (a % p != 0)  // 由于p是质数，除了p的倍数不与其互质，其他都互质
            cout << qpow(a, p - 2,p) << endl; 
        else cout << "impossible" << endl;
    }
}
```
