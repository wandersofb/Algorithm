# Master Theorem

#### 前言

* 在介绍主定理之前，需要了解一下关于表示时间复杂度的几种形式。
* $$O(big-O)$$ 表示最坏时间复杂度，为上界。
* $$\Omega(big-Omega)$$ 表示最优时间复杂度，为下界。
* $$\Theta(big-Theta)$$ 表示确界，当上界和下届相同时，可以用这个来表示时间复杂度。

#### 主定理

主定理主要解决递归式的时间复杂度计算。但并不能解决所有的递归式。主要适用如下递归式：

$$
T(n) = aT({n \over b}) + f(n)
$$

* $$n$$ 是问题规模。
* $$a$$ 为递归的子问题。
* $${n \over b}$$ 为每个子问题的规模。
* $$f(n)$$ 为递归以外的计算量。

**三种 case**

* 主定理的结果主要跟 $$f(n)$$ 有关。
* 当 $$f(n) = O(n^{(\log_ba) - \epsilon})$$ 其中 $$\epsilon > 0$$ （相当于 $$\log_ba > f(n)$$)，则有 $$T(n) = \Theta(n^{log_ba})$$。
* 当 $$f(n) = \Theta(n^{\log_ba})$$，则有$$T(n) = \Theta(n^{\log_ba}\log n)$$。
* 当 $$f(n) = \Omega(n^{(\log_ba) + \epsilon})$$ 其中 $$\epsilon > 0$$ （相当于$$\log_ba < f(n)$$），则有 $$T(n) = \Theta(f(n))$$

**简化版本**

如果 $$T(n) = aT({n \over b}) + O(n^d)$$（其中 $$a > 0, b > 1, d \ge 0$$），则有

$$
T(n) = \begin{cases} O(n^d) &if\ d > \log_ba \\ O(n^d\log n) &if\ d = \log_ba\\ O(n^{log_ba}) &if\ d < \log_ba \end{cases}
$$

#### 总结

计算递归时间复杂度的话，一般两种方法，画递归树手算和用主定理，具体情况具体分析。
