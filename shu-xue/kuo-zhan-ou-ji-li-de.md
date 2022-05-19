# 扩展欧几里得

**贝祖等式**

给定两个整数 $$a,b$$，必存在整数 $$x,y$$ 使得 $$ax + by = c$$ 且 $$gcd(a,b)|c$$。

**算法分析**

因为

$$
gcd(a,b) = gcd(b,a\%b)
$$

可得

$$
bx^{'} + (a - \lfloor a / b \rfloor * b)y^{'} = gcd(b, a\%b)
$$

继而

$$
ay^{'} + b(x^{'} - \lfloor a / b \rfloor * y^{'}) = gcd(b,a\%b) = gcd(a,b)
$$

故而

$$
x = y^{'},y = x^{'} - \lfloor a / b \rfloor * y^{'}
$$

```
#include<iostream>
#include<algorithm>
using namespace std;

void exgcd(int a, int b, int &x, int &y){
    if (!b){
        x = 1, y = 0;
        return;
    }
    exgcd(b, a % b, y, x);
    y -= a / b * x;
    return;
}

int main(){
    int n; cin >> n;
    while (n -- ){
        int a, b, x, y; cin >> a >> b;
        exgcd(a, b, x, y);
        printf("%d %d\n", x, y);
    }
}
```

**时间复杂度分析**

* 这题的时间复杂度和欧几里得算法一样。欧几里得算法时间复杂度 $$O(\log b)$$，总时间复杂度 $$O(n \log b)$$。

**线性同余方程**

> 给定 $$n$$ 组数据 $$a_i,b_i,m_i$$，对于每组数求出一个 $$x_i$$，使其满足 $$a_i*x_i\equiv b_i\pmod m$$，如果无解则输出 `impossible`。

由

$$
a_i*x_i\equiv b_i\pmod m
$$

可得 $$\exists y \in Z$$

$$
ax = my + b \Rightarrow ax + my^{'} = b
$$

由之前的贝祖等式可知，只要 $$gcd(a,m)|b$$，我们就可以利用扩展欧几里得算法求解线性同余方程。

```
#include<iostream>
#include<algorithm>

using namespace std;
void exgcd(int a, int b, int &x, int &y){
    if (!b){
        x = 1, y = 0;
        return;
    }
    exgcd(b, a % b, y, x);
    y -= a / b * x;
}


int main(){
    int n; cin >> n;
    while (n -- ){
        int a, b, m; cin >> a >> b >> m;
        int x, y;
        exgcd(a, m, x, y);
        int gcd = a * x + m * y;
        if (b % gcd == 0)
            cout << (long long)b / gcd * x % m << endl; 
        else 
            cout << "impossible" << endl;
    }
}
```
