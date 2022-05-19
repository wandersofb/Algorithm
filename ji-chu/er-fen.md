# 二分

经典对数级别查找方法。二分有两个模板，下面针对性的分析一下这两个模板。

* 将区间划分为 `[l, r]` 划分为 `[l, mid]` 和 `[mid + 1, r]` 时（下取整 mid `l + r >> 1`）。

```
int bsearch_1(int l, int r)
{
    while (l < r)
    {
        int mid = l + r >> 1;
        if (check(mid)) r = mid;
        else l = mid + 1;
    }
    return l;
}
```

* 将区间划分为 `[l, r]` 划分为 `[l, mid - 1]` 和 `[mid, r]` 时（上取整 mid `l + r + 1 >> 1`）。

```
int bsearch_2(int l, int r)
{
    while (l < r)
    {
        int mid = l + r + 1 >> 1;
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    return l;
}
```

* _如何判断使用那个模板？_ 根据 check 函数的结果来判断是谁取 mid。`l = mid` 那么上取整，`r = mid` 那么下取整。谁等于 mid 向那边取整。
