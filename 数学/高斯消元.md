# 高斯消元

学过线代的都知道，把增广矩阵转化为行阶梯型矩阵，然后判断解的情况（无解、无穷解、唯一解），即可。但高斯消元代码涉及到二维比较难理解。高斯消元可以用来求线性多元方程组。

**线性多元方程组**

例如该方程组

$$
\begin{cases} a_{11}x_1 + &a_{12}x_2 + \cdots + &a_{1n}x_n = &b_1\\ a_{21}x_1 + &a_{22}x_2 + \cdots + &a_{2n}x_n = &b_2\\ \vdots &\vdots &\vdots &\vdots\\ a_{n1}x_1 + &a_{n2}x_2 + \cdots + &a_{nn}x_n = &b_2\\ \end{cases}
$$

其增广矩阵为

$$
\left[ \begin{array} {c c c c| c} a_{11} & a_{12} & \cdots & a_{1n} & b_{1}\\ a_{22} & a_{22} & \cdots & a_{2n} & b_{2}\\ \vdots & \vdots & \vdots & \vdots & \vdots\\ a_{n1} & a_{n2} & \cdots & a_{nn} & b_{n}\\ \end{array} \right]
$$

* 对增广矩阵进行初等行变换转化为行阶梯型矩阵
  * 对列进行枚举
  * 找到当前列的绝对值最大的那一行
  * 把此行交换到上面（并非第一行，而是还未确定）的行
  * 将该行第一个数置为1，其他数随之变化。
  * 将下面所有行的当前列的值变为零，其他列随着变化。

行阶梯型矩阵

$$
\begin{bmatrix} a_{11} & a_{12} & \cdots & a_{1n} & b_1\\ & a_{22} & \cdots & a_{2n} & b_2\\ & & & \vdots & \vdots\\ & & & a_{nn} & b_n \end{bmatrix}
$$

```cpp
#include<iostream>
#include<algorithm>
#include<cmath>
using namespace std;
const int N =  110;
const double eps = 1e-8;
double p[N][N];

int gauss(int n){
    int c, r;
    for (c = 0, r = 0; c < n; c ++){
        int t = r;
        for (int i = r; i < n; i ++)
            if (fabs(p[i][c]) > fabs(p[t][c]))    
                t = i;
            
        if (fabs(p[t][c]) < eps) continue;
                
        for (int i = c; i <= n; i ++)
            swap(p[r][i], p[t][i]);
        for (int i = n; i >= c; i --)
            p[r][i] /= p[r][c];
        for (int i = r + 1; i < n; i ++)
            if (fabs(p[i][c]) > eps)
                for (int j = n; j >= c; j --)
                    p[i][j] -= p[r][j] * p[i][c];
                    
        r ++;
    }
    
    if (r < n){
        for (int i = r; i < n; i ++)
            if (fabs(p[i][n]) > eps)
                return 2;
        return 1;
    }
    
    for (int i = n - 1; i >= 0; i --)
        for (int j = i + 1; j < n; j ++)
            p[i][n] -= p[i][j] * p[j][n];
        
    return 0;
}


int main(){
    int n ; cin >> n;
    for (int i = 0; i < n; i ++)
        for (int j = 0; j <= n; j ++)
            cin >> p[i][j];
    int t = gauss(n);
    if (t == 0)
        for (int i = 0; i < n; i ++){
            if (fabs(p[i][n]) < eps) p[i][n] = 0;
            printf("%.2lf\n", p[i][n]);
        }
    else if (t == 1) puts("Infinite group solutions");
    else puts("No solution");
    
}
```

**时间复杂度**

观察 for 循环可知，最高循环为三层，其时间复杂度最坏为 $$O(n^3)$$。

#### 异或线性方程组
