# Best Time to Buy and Sell Stock IV

## Question

- leetcode: [Best Time to Buy and Sell Stock IV | LeetCode OJ](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/)
- lintcode: [(393) Best Time to Buy and Sell Stock IV](http://www.lintcode.com/en/problem/best-time-to-buy-and-sell-stock-iv/)

```
Say you have an array for
which the ith element is the price of a given stock on day i.

Design an algorithm to find the maximum profit.
You may complete at most k transactions.

Example
Given prices = [4,4,6,1,1,4,2,5], and k = 2, return 6.

Note
You may not engage in multiple transactions at the same time
(i.e., you must sell the stock before you buy again).

Challenge
O(nk) time.
```

## 题解1

卖股票系列中最难的一道，较易实现的方法为使用动态规划，动规的实现又分为大约3大类方法，这里先介绍一种最为朴素的方法，过不了大量数据，会 TLE.

最多允许 k 次交易，由于一次增加收益的交易至少需要两天，故当 k >= n/2时，此题退化为卖股票的第二道题，即允许任意多次交易。当 k < n/2 时，使用动规来求解，动规的几个要素如下：

f[i][j] 代表第 i 天为止交易 k 次获得的最大收益，那么将问题分解为前 x 天交易 k-1 次，第 x+1 天至第 i 天交易一次两个子问题，于是动态方程如下：

```
f[i][j] = max(f[x][j - 1] + profit(x + 1, i))
```

简便起见，初始化二维矩阵为0，下标尽可能从1开始，便于理解。


### Java

```java
class Solution {
    /**
     * @param k: An integer
     * @param prices: Given an integer array
     * @return: Maximum profit
     */
    public int maxProfit(int k, int[] prices) {
        if (prices == null || prices.length <= 1 || k <= 0) return 0;

        int n = prices.length;
        if (k >= n / 2) {
            int profit_max = 0;
            for (int i = 1; i < n; i++) {
                if (prices[i] - prices[i - 1] > 0) {
                    profit_max += prices[i] - prices[i - 1];
                }
            }
            return profit_max;
        }

        int[][] f = new int[n + 1][k + 1];
        for (int j = 1; j <= k; j++) {
            for (int i = 1; i <= n; i++) {
                for (int x = 0; x <= i; x++) {
                    f[i][j] = Math.max(f[i][j], f[x][j - 1] + profit(prices, x + 1, i));
                }
            }
        }

        return f[n][k];
    }

    private int profit(int[] prices, int l, int u) {
        if (l >= u) return 0;

        int valley = Integer.MAX_VALUE;
        int profit_max = 0;
        for (int i = l - 1; i < u; i++) {
            profit_max = Math.max(profit_max, prices[i] - valley);
            valley = Math.min(valley, prices[i]);
        }
        return profit_max;
    }
};
```

### 源码分析

注意 Python 中的多维数组初始化方式，不可简单使用`[[0] * k] * n]`, 具体原因是因为 Python 中的对象引用方式。可以优化的地方是 profit 方法及最内存循环。

### 复杂度分析

三重循环，时间复杂度近似为 $$O(n^2 \cdot k)$$, 使用了 f 二维数组，空间复杂度为 $$O(n \cdot k)$$.

### 分析

设两个状态，`global[i][j]` 表示i天前最多可以进行j次交易的最大利润，`local[i][j]`表示i天前最多可以进行j次交易，且在第i天进行了第j次交易的最大利润。状态转移方程如下：

```
local[i][j] = max(global[i-1][j-1] + max(diff,0), local[i-1][j]+diff)
global[i][j] = max(local[i][j], global[i-1][j])
```

关于`global`的状态转移方程比较简单，不断地和已经计算出的local进行比较，把大的保存在global中。

关于`local`的状态转移方程，取下面二者中较大的一个：

* 全局前`i-1`天进行了`j-1`次交易，然后然后加上今天的交易产生的利润（如果赚钱就交易，不赚钱就不交易，什么也不发生，利润是0）
* 局部前`i-1`天进行了`j`次交易，然后加上今天的差价（`local[i-1][j]`是第`i-1`天卖出的交易，它加上diff后变成第`i`天卖出，并不会增加交易次数。无论`diff`是正还是负都要加上，否则就不满足`local[i][j]`必须在最后一天卖出的条件了）

注意，当`k`大于数组的大小时，上述算法将变得低效，此时可以改为不限交易次数的方式解决，即等价于 "Best Time to Buy and Sell Stock II"。


### 解法1

{% codesnippet "./code/best-time-to-buy-and-sell-stock-iv-1."+book.suffix, language=book.suffix %}{% endcodesnippet %}

### 解法2 最长m段子段和

## Reference

- [[LeetCode] Best Time to Buy and Sell Stock I II III IV | 梁佳宾的网络日志](http://liangjiabin.com/blog/2015/04/leetcode-best-time-to-buy-and-sell-stock.html)
- [Best Time to Buy and Sell Stock IV 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/best-time-to-buy-and-sell-stock-iv/)
- [leetcode-Best Time to Buy and Sell Stock 系列 // 陈辉的技术博客](http://www.devhui.com/2015/02/23/Best-Time-to-Buy-and-Sell-Stock/)
- [[LeetCode]Best Time to Buy and Sell Stock IV | 书影博客](http://bookshadow.com/weblog/2015/02/18/leetcode-best-time-to-buy-and-sell-stock-iv/)
