# 组合数

* 组合数是从 $$n$$ 中取 $$k$$ 个数。它的表示方式 $$C_n^k$$ 和 $$n \choose k$$。
* 阶乘公式：$$C_n^k = {n! \over k!(n-k)!} = {n(n-1)(n-2) \cdots (n-k+1) \over k!}$$
* 递归公式：$$C_n^k = C_{n-1}^{k - 1} + C_{n - 1}^{k}$$
  * 递归公式可以看作新来的一个苹果，我要么选它要么不选它。
* _Lucas_ 定理：$$C_n^k \equiv C_{n\%p}^{k\%p}*C_{n/p}^{k/p} \pmod p$$

**几种 case**

* 当 $$1\le n \le 10000, 1 \le b\le a \le 2000$$ 时，用递归公式预处理。

```cpp
#include<iostream>
#include<algorithm>

using namespace std;

const int N = 2010, mod = 1e9 + 7;
int p[N][N];

void init(){
    for (int i = 0; i < N; i ++)
        for (int j = 0; j <= i; j ++){
            if (!j) p[i][j] = 1;
            else p[i][j] = (p[i - 1][j] + p[i - 1][j - 1]) % mod;
        }
}

int main(){
    int n; cin >> n;
    init();
    while (n -- ){
        int a, b; cin >> a >> b;
        cout << p[a][b] << endl;
    }
}
```

* 当 $$1\le n \le 10000, 1 \le b \le a \le 10^5$$ 时，直接预处理 $$a$$ 和 $$b$$ 的阶乘和逆元阶乘，其公式为：

$$
C_n^k = {n! \over k!(n-k)!} = n!{k!}^{-1}{(n-k)}^{-1}
$$

，其中逆元用快速幂可求。

```cpp
#include<iostream>
#include<algorithm>
using namespace std;
typedef long long LL;

const int N = 100010, mod = 1e9 + 7;

int fact[N], infact[N];

int qpow(int a, int b, int mod){
    int res = 1;
    while (b){
        if (b & 1) res = (LL)res * a % mod;
        a = (LL)a * a % mod;
        b >>= 1;        
    }
    return res;
}

int main(){
    fact[0] = infact[0] = 1;
    for (int i = 1; i < N; i ++){
        fact[i] = (LL)fact[i - 1] * i % mod;
        infact[i] = (LL)infact[i - 1] * qpow(i, mod - 2, mod) % mod;
    }
    int n; cin >> n;
    while (n -- ){
        int a, b; cin >> a >> b;
        cout << ((LL)fact[a] * infact[b] % mod) * infact[a - b] % mod << endl;
    }
}
```

* 当 $$1≤n≤20,1≤b≤a≤10^{18},1≤p≤10^5$$，由于 $$b$$ 和 $$a$$ 过大，在预处理这俩的阶乘就不合适了，而且 $$p$$ 也不一定是质数，所以无法用费马小定理和快速幂求逆元 ，应使用 _lucas_ 定理和朴素公式。求组合数公式：

$$
C_n^k = {n! \over k!(n-k)!} = {n*(n-1)*(n-2)*\cdots * (n-k+1) \over k!}
$$

```cpp
#include<iostream>
#include<algorithm>
using namespace std;
typedef long long LL;

int qpow(int a, int b, int mod){
    int res = 1;
    while(b){
        if (b & 1) res = (LL)res * a % mod;
        a = (LL)a * a % mod;
        b >>= 1;
    }
    return res;
}

int C(LL a, LL b, int p){
    if (b > a ) return 0;
    int res = 1;
    for (int i = 1, j = a; i <= b; j --, i ++){
        res = (LL)res * j % p;
        res = (LL)res * qpow(i, p - 2, p) % p;
    }
    return res;
}

int lucas(LL a, LL b, int p){
    if (a < p && b < p) return C(a, b, p);
    return (LL)C(a % p, b % p, p) * lucas(a / p, b / p, p) % p;
}

int main(){
    int n; cin >> n;
    
    while (n -- ){
        LL a, b;
        int p; 
        cin >> a >> b >> p;
        cout << lucas(a, b, p) << endl;
    }
}
```

* 当 $$1≤b≤a≤5000$$，且不再 mod 谁，也就是说要求一个很大的数，应使用高精度来存储。再观察一下此式：

$$
C_n^k = {n! \over k!(n-k)!}
$$

若对分子和分母分解质因子，对质因子处理，最后相乘即可。需要使用线性筛分解质因子。但分解完质因子，如何求其幂，有个公式是这样的(:( 怎么公式折磨多))：

$$
v_p(n!) = \lfloor {n \over p} \rfloor + \lfloor {n \over p^2} \rfloor + \cdots + \lfloor {n \over p^k} \rfloor \ \ \ \ \ (n < p^k)
$$

这个最好在纸上画一遍。举个例子，假如求 $$6! = 1*2*3*4*5*6$$ 中 $$2$$ 的次数，大致意思是，除以 $$2^1$$，可以对 $$2,4,6$$ 都去除一次 $$2$$，则幂为 $$3$$，除以 $$2^2$$，可以对 $$4$$ 去除一次 $$2^2$$，则幂为 $$1$$，相加为 $$4$$。

```cpp
#include<iostream>
#include<algorithm>
#include<vector>
using namespace std;
const int N = 5010;
int primes[N], cnt;
int sum[N];
bool tou[N];

void get_primes(int a){
    for (int i = 2; i <= a; i ++){
        if (!tou[i]) primes[cnt++] = i;
        for (int j = 0; primes[j] <= a / i; j ++){
            tou[primes[j] * i] = true;
            if (i % primes[j] == 0) break;
        }
    }
}

int get(int n, int p){
    int res = 0;
    while (n){
        res += n / p;
        n /= p;
    }
    return res;
}

vector<int> mul(vector<int> a, int b){
    vector<int> c;
    int t = 0;
    for (int i = 0; i < a.size(); i ++){
        t += a[i] * b;
        c.push_back(t % 10);
        t /= 10;
    }
    while (t){
        c.push_back(t % 10);
        t /= 10;
    }
    return c;
}

int main(){
    int a, b; cin >> a >> b;
    get_primes(a);    
    
    for (int i = 0; i < cnt; i ++){
        int p = primes[i];
        sum[i] = get(a, p) - get(a - b, p) - get(b, p);
    }        
    vector<int> res;
    res.push_back(1);
    for (int i = 0; i < cnt; i ++){
        for (int j = 0; j < sum[i]; j ++){
            res = mul(res, primes[i]);
        }
    }
    for (int i = res.size() - 1; i >= 0; i --) printf("%d", res[i]);
    puts(" ");
    return 0;
}
```

#### 组合数应用

卡特兰数的应用挺多的。其表达形式：

$$
C_n = C_{2n}^n - C_{2n}^{n-1} = {c_{2n}^n \over (n + 1)}
$$

它的意思是说，$$c_{2n}^n$$ 是全部可能情况，而 $$c_{2n}^{n-1}$$ 是不合法情况。二者做差就是合法情况。

**满足条件的01序列**

> 给定 $$n$$ 个 $$0$$ 和 $$n$$ 个 $$1$$，它们将按照某种顺序排成长度为 $$2n$$ 的序列，求它们能排列成的所有序列中，能够满足任意前缀序列中 $$0$$ 的个数都不少于 $$1$$ 的个数的序列有多少个。输出的答案对 $$10^9+7$$ 取模。

![](../.gitbook/assets/13653\_c0dae9e295-2020032210584895.png)

```cpp
#include<iostream>
#include<algorithm>
typedef long long LL;

using namespace std;
const int mod = 1e9 + 7;

int qpow(int a, int b, int mod){
    int res = 1;
    while (b){
        if (b & 1) res = (LL)res * a % mod;
        a = (LL)a * a % mod;
        b >>= 1;
    }
    return res;
}

int main(){
    int n; cin >> n;
    int a = 2 * n, b = n;
    int res = 1; 
    for (int i = a; i >= a - b + 1; i -- ) res = (LL)res * i % mod;
    
    for (int i = 1; i <= b; i ++) res = (LL)res * qpow(i, mod - 2, mod) % mod;
    
    res = (LL)res * qpow(b + 1, mod - 2, mod) % mod;
    cout << res;
    
}
```

#### 总结

组合数还是挺重要的，掌握基础的组合数是如何计算的以及卡特兰数的应用还是必须的。
