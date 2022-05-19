# 前缀和  & 差分

前缀和 和 差分 一般是解题时预处理方便计算的思想。先记录一维的。

### 前缀和

#### 一维

```cpp
cfor (int i = 1; i < N; i++) {
    // 前缀和数组的第 i 项 = 原数组的 0 到 i-1 项的和 + 原数组的第 i 项。
    B[i] = B[i - 1] + A[i];
  }
```

### 差分

#### 一维

```cpp
// 对区间操作
void insert(int l, int r, int c){
    s[l] += c;
    s[r + 1] -= c;
}

// 初始化
for (int i = 1; i <= n; i++) insert(i, i, p[i]);

// 操作
while (m --){
    int l, r, c;
    scanf("%d%d%d", &l,&r,&c);
    insert(l,r,c);
}
```
