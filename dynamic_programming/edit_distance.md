# Edit Distance

- tags: [DP_Two_Sequence]

## Question

- leetcode: [Edit Distance | LeetCode OJ](https://leetcode.com/problems/edit-distance/)
- lintcode: [(119) Edit Distance](http://www.lintcode.com/en/problem/edit-distance/)

```
Given two words word1 and word2, find the minimum number of steps required 
to convert word1 to word2. (each operation is counted as 1 step.)

You have the following 3 operations permitted on a word:

Insert a character
Delete a character
Replace a character
Example
Given word1 = "mart" and word2 = "karma", return 3.
```

## 题解1 - 双序列动态规划

两个字符串比较，求最值，直接看似乎并不能直接找出解决方案，这时往往需要使用动态规划的思想寻找递推关系。使用双序列动态规划的通用做法，不妨定义`f[i][j]`为字符串1的前`i`个字符和字符串2的前`j`个字符的编辑距离，那么接下来寻找其递推关系。增删操作互为逆操作，即增或者删产生的步数都是一样的。故初始化时容易知道`f[0][j] = j, f[i][0] = i`, 接下来探讨`f[i][j]` 和`f[i - 1][j - 1]`的关系，和 LCS 问题类似，我们分两种情况讨论，即`word1[i] == word2[j]` 与否，第一种相等的情况有：

1. `i == j`, 且有`word1[i] == word2[j]`, 则由`f[i - 1][j - 1] -> f[i][j]` 不增加任何操作，有`f[i][j] = f[i - 1][j - 1]`.
2. `i != j`, 由于字符数不等，肯定需要增/删一个字符，但是增删 word1 还是 word2 是不知道的，故可取其中编辑距离的较小值，即`f[i][j] = 1 + min{f[i - 1][j], f[i][j - 1]}`.

第二种不等的情况有：

1. `i == j`, 有`f[i][j] = 1 + f[i - 1][j - 1]`.
2. `i != j`, 由于字符数不等，肯定需要增/删一个字符，但是增删 word1 还是 word2 是不知道的，故可取其中编辑距离的较小值，即`f[i][j] = 1 + min{f[i - 1][j], f[i][j - 1]}`.

最后返回`f[len(word1)][len(word2)]`


### Java

```java
public class Solution {
    public int minDistance(String word1, String word2) {
        int len1 = 0, len2 = 0;
        if (word1 != null && word2 != null) {
            len1 = word1.length();
            len2 = word2.length();
        }
        if (word1 == null || word2 == null) {
            return Math.max(len1, len2);
        }
        
        int[][] f = new int[1 + len1][1 + len2];
        for (int i = 0; i <= len1; i++) {
            f[i][0] = i;
        }
        for (int i = 0; i <= len2; i++) {
            f[0][i] = i;
        }
        
        for (int i = 1; i <= len1; i++) {
            for (int j = 1; j <= len2; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    f[i][j] = Math.min(f[i - 1][j - 1], 1 + f[i - 1][j]);
                    f[i][j] = Math.min(f[i][j], 1 + f[i][j - 1]);
                } else {
                    f[i][j] = Math.min(f[i - 1][j - 1], f[i - 1][j]);
                    f[i][j] = 1 + Math.min(f[i][j], f[i][j - 1]);
                }
            }
        }
        
        return f[len1][len2];
    }
}
```

### 源码解析

1. 边界处理
2. 初始化二维矩阵(Python 中初始化时 list 中 len2 在前，len1 在后)
3. i, j 从1开始计数，比较 word1 和 word2 时注意下标
4. 返回`f[len1][len2]`

### 复杂度分析

两重 for 循环，时间复杂度为 $$O(len1 \cdot len2)$$. 使用二维矩阵，空间复杂度为 $$O(len1 \cdot len2)$$.

### 分析

设状态为`f[i][j]`，表示`A[0,i]`和`B[0,j]`之间的最小编辑距离。设`A[0,i]`的形式是`str1c`，`B[0,j]`的形式是`str2d`，

1. 如果`c==d`，则`f[i][j]=f[i-1][j-1]`；
1. 如果`c!=d`，

    1. 如果将c替换成d，则`f[i][j]=f[i-1][j-1]+1`；
    1. 如果在c后面添加一个d，则`f[i][j]=f[i][j-1]+1`；
    1. 如果将c删除，则`f[i][j]=f[i-1][j]+1`；


### 动规

```java
// Edit Distance
// 二维动规，时间复杂度O(n*m)，空间复杂度O(n*m)
public class Solution {
    public int minDistance(String word1, String word2) {
        final int n = word1.length();
        final int m = word2.length();
        // 长度为n的字符串，有n+1个隔板
        int[][] f = new int[n+1][m+1];
        for (int i = 0; i <= n; i++)
            f[i][0] = i;
        for (int j = 0; j <= m; j++)
            f[0][j] = j;

        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1))
                    f[i][j] = f[i - 1][j - 1];
                else {
                    int mn = Math.min(f[i - 1][j], f[i][j - 1]);
                    f[i][j] = 1 + Math.min(f[i - 1][j - 1], mn);
                }
            }
        }
        return f[n][m];
    }
}
```


### 动规+滚动数组

```java
// Edit Distance
// 二维动规+滚动数组
// 时间复杂度O(n*m)，空间复杂度O(n)
public class Solution {
    public int minDistance(String word1, String word2) {
        if (word1.length() < word2.length())
            return minDistance(word2, word1);

        int[] f = new int[word2.length() + 1];
        int upper_left = 0; // 额外用一个变量记录f[i-1][j-1]

        for (int i = 0; i <= word2.length(); ++i)
            f[i] = i;

        for (int i = 1; i <= word1.length(); ++i) {
            upper_left = f[0];
            f[0] = i;

            for (int j = 1; j <= word2.length(); ++j) {
                int upper = f[j];

                if (word1.charAt(i - 1) == word2.charAt(j - 1))
                    f[j] = upper_left;
                else
                    f[j] = 1 + Math.min(upper_left, Math.min(f[j], f[j - 1]));

                upper_left = upper;
            }
        }

        return f[word2.length()];
    }
}
```
