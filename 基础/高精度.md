# 高精度

高精度是计算极大数的基础算法，四个算法为：加、减 、乘和除。

#### 加法

按照竖式加法来计算。

```cpp
#include<iostream>
#include<algorithm>
#include<vector>
using namespace std;

vector<int> add(vector<int> a, vector<int> b){
    if (b.size() > a.size()) return add(b, a);
    vector<int> c;
    int t = 0;  // 进位
    for (int i = 0; i < a.size(); i ++){
        t += a[i];
        if (i < b.size()) t += b[i];
        c.push_back(t % 10);
        t /= 10;
    }
    if (t) c.push_back(t);
    return c;
}

int main(){
    string s1, s2;
    vector<int> a, b;
    cin >> s1 >> s2;
    
    for (int i = s1.size() - 1; i >= 0; i --) a.push_back(s1[i] - '0');
    for (int i = s2.size() - 1; i >= 0; i --) b.push_back(s2[i] - '0');
    
    auto c = add(a, b);
    
    for (int i = c.size() - 1; i >= 0; i --) cout << c[i];
    return 0;
}
```

#### 减法

按照竖式减法计算。减法有几个 tricky 点，当减法的时候，我们要比较大小，用大的减小的，还有减法会令结果出现前导零，需要排除。

```cpp
#include<iostream>
#include<vector>
using namespace std;

vector<int> a, b;

bool cmp(vector<int> &a, vector<int> &b){
    if (a.size() != b.size()) return a.size() > b.size();
    
    for (int i = a.size() - 1; i >= 0; i --)
        if (a[i] != b[i])
            return a[i] > b[i];
    return true;
}

vector<int> sub(vector<int> &a, vector<int> &b){
    
    vector<int> c;
    int t = 0;  // 借位
    for (int i = 0; i < a.size(); i ++){
        t = a[i] - t; // 前去上一位的借位
        if (i < b.size()) t -= b[i]; 
        c.push_back((t + 10) % 10);
        if (t < 0) t = 1;
        else t = 0;
    }
    while (c.size() > 1 && c.back() == 0) c.pop_back();
    return c;
}

int main(){
    string s1, s2; cin >> s1 >> s2;
    for (int i = s1.size() - 1; i >= 0; i --) a.push_back(s1[i] - '0');
    for (int i = s2.size() - 1; i >= 0; i --) b.push_back(s2[i] - '0');
    
    vector<int> c;
    
    if (cmp(a, b)) c = sub(a, b);
    else c = sub(b, a), cout << '-';
    
    for (int i = c.size() - 1; i >= 0; i --) cout << c[i];
    
    cout << endl;
}
```

#### 高精度乘法

**高精度-单精度**

$$
1≤A的长度≤100000, 0≤B≤10000
$$

```cpp
#include<iostream>
#include<algorithm>
using namespace std;

vector<int> mul(vector<int> &a, int b){
    vector<int> c;
    int t = 0;
    for(int i = 0; i < a.size() || t; i ++){
        if (i < a.size()) t += a[i] * b; 
        c.push_back(t % 10);
        t /= 10;
    }
    
    while (c.size() > 1 && c.back() == 0) c.pop_back();
    return c;
}

int main(){
    int b;
    string s1; cin >> s1 >> b;
    vector<int> a;    
    for (int i = s1.size() - 1; i >= 0; i --) a.push_back(s1[i] - '0');
        
    auto c = mul(a, b);
    for (int i = c.size() - 1; i >= 0; i --) cout << c[i];
    cout << endl;
    return 0;
}
```

#### 高精度除法

$$
1≤A的长度≤100000, 1≤B≤10000, B 一定不为 0
$$

```cpp
#include<iostream>
#include<algorithm>
#include<vector>
using namespace std;

vector<int> div(vector<int> A, int b, int &r){
    vector<int> c;
    r = 0;
    for (int i = A.size() - 1; i >= 0; i --){
        r = r * 10 + A[i];
        c.push_back(r / b);
        r %= b;
    }
    reverse(c.begin(), c.end());
    while (c.size() > 1 && c.back() == 0) c.pop_back();
    return c;
}

int main(){
    string a; cin >> a;
    int b; cin >> b;
    vector<int> A;
    for (int i = a.size() - 1; i >= 0; i -- ) A.push_back(a[i] - '0');
    
    int r;
    auto c = div(A, b, r);
    
    for (int i = c.size() - 1; i >= 0; i -- ) cout << c[i];
    
    cout << endl << r << endl;
    
    return 0;
}
```
