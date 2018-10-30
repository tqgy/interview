# Longest Palindromic Substring

Tags: String, Medium

## Question

- leetcode: [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)
- lintcode: [Longest Palindromic Substring](http://www.lintcode.com/en/problem/longest-palindromic-substring/)

### Problem Statement

Given a string **s**, find the longest palindromic substring in **s**. You may
assume that the maximum length of **s** is 1000.

**Example:**
    
    
    
    **Input:** "babad"
    
    **Output:** "bab"
    
    **Note:** "aba" is also a valid answer.
    

**Example:**
    

    **Input:** "cbbd"
    
    **Output:** "bb"

## 题解1 - 穷竭搜索

最简单的方案，穷举所有可能的子串，判断子串是否为回文，使用一变量记录最大回文长度，若新的回文超过之前的最大回文长度则更新标记变量并记录当前回文的起止索引，最后返回最长回文子串。


### Java

```java
public class Solution {
    public String longestPalindrome(String s) {
        if (s == null || s.isEmpty()) return "";
        
        final int sLen = s.length();
        int longest = 0, left = 0;
        for (int i = 0; i < sLen; i++) {
            for (int j = i; j < sLen; j++) {
                if (j - i + 1 > longest && isPalindrome(s, i, j)) {
                    longest = j - i + 1;
                    left = i;
                }
            }
        }

        return s.substring(left, left + longest);
    }

    private boolean isPalindrome(String s, int left, int right) {
        for (int i = left, j = right; i <= j; i++, j--) {
            if (s.charAt(i) != s.charAt(j)) return false;
        }
        return true;
    }
}
```

### 源码分析

使用 left 作为子串的起止索引，longest 作为当前最长长度，用于最后构造返回结果，避免中间构造字符串以减少开销。两重循环中需要注意的是第二重循环的起止值及判断回文中的索引起止值。

### 复杂度分析

穷举所有的子串，$$O(C_n^2) = O(n^2)$$, 每次判断字符串是否为回文，复杂度为 $$O(n)$$, 故总的时间复杂度为 $$O(n^3)$$, 大数据集下可能 TLE. 仅在最后返回取子串，空间复杂度为 $$O(1)$$.
P.S. 目前仅 Java 对回文判断优化过。

## 题解2

题解1 中的思路是从子串出发判断回文串进而取最长，可以发现其中有许多重复的计算，如果我们从回文串本身出发进行求解，即从子串中间向左向右判断是否符合要求，由于回文串必定是某一子串，故只需从字符串的某一索引展开，分奇偶长度判断，时间复杂度可降为 $$O(n^2)$$.

### Java

```java
public class Solution {
    public String longestPalindrome(String s) {
        if (s == null || s.isEmpty()) return "";
        
        final int sLen = s.length();
        int longest = 0, left = 0;
        for (int i = 0; i < sLen; i++) {
            // odd
            int leftIndex = leftPalindromeIndex(s, i, i);
            int palindromeLen = 2 * (i - leftIndex) + 1;
            if (palindromeLen > longest) {
                left = leftIndex;
                longest = palindromeLen;
            }
            // even
            leftIndex = leftPalindromeIndex(s, i, i + 1);
            palindromeLen = 2 * (i - leftIndex + 1);
            if (palindromeLen > longest) {
                left = leftIndex;
                longest = palindromeLen;
            }
        }

        return s.substring(left, left + longest);
    }

    private int leftPalindromeIndex(String s, int left, int right) {
        for (; left >= 0 && right < s.length(); left--, right++) {
            if (s.charAt(left) != s.charAt(right)) break;
        }
        return left + 1;
    }
}
```

### 源码分析

假定扫描的每个字母是回文的中间位置（需要处理奇偶两种情况），从该位置向两头搜索寻找最大回文长度。

### 复杂度分析

时间复杂度降到 $$O(n^2)$$.

## 题解3

另外还有一个O（n）的解法，具体参考下面的链接
http://articles.leetcode.com/2011/11/longest-palindromic-substring-part-ii.html

### 分析

最长回文子串，非常经典的题。

思路一：暴力枚举，以每个元素为中间元素，同时从左右出发，复杂度`O(n^2)`。

思路二：记忆化搜索，复杂度`O(n^2)`。设`f[i][j]` 表示[i,j]之间的最长回文子串，递推方程如下：

```
f[i][j] = if (i == j) S[i]
          if (S[i] == S[j] && f[i+1][j-1] == S[i+1][j-1]) S[i][j]
          else max(f[i+1][j-1], f[i][j-1], f[i+1][j])
```

思路三：动规，复杂度`O(n^2)`。设状态为`f(i,j)`，表示区间[i,j]是否为回文串，则状态转移方程为

$$
f(i,j)=\begin{cases}
true & ,i=j\\
S[i]=S[j] & , j = i + 1 \\
S[i]=S[j] \text{ and } f(i+1, j-1) & , j > i + 1
\end{cases}
$$

思路四：Manacher’s Algorithm, 复杂度`O(n)`。详细解释见 <http://leetcode.com/2011/11/longest-palindromic-substring-part-ii.html>。


### 备忘录法

{% if book.java %}
```java
// Longest Palindromic Substring
// 备忘录法，会超时
// 时间复杂度O(n^2)，空间复杂度O(n^2)
public class Solution {
    private final HashMap<Pair, String> cache = new HashMap<>();
    
    public String longestPalindrome(final String s) {
        cache.clear();
        return cachedLongestPalindrome(s, 0, s.length() - 1);
    }

    String longestPalindrome(final String s, int i, int j) {
        final int length = j - i + 1;
        if (length < 2) return s.substring(i, j + 1);

        final String s1 = cachedLongestPalindrome(s, i + 1, j - 1);

        if (s1.length() == length - 2 && s.charAt(i + 1) == s.charAt(j - 1))
            return s.substring(i, j + 1);

        final String s2 = cachedLongestPalindrome(s, i + 1, j);
        final String s3 = cachedLongestPalindrome(s, i, j - 1);

        // return max(s1, s2, s3)
        if (s1.length() > s2.length()) return s1.length() > s3.length() ? s1 : s3;
        else return s2.length() > s3.length() ? s2 : s3;
    }

    String cachedLongestPalindrome(final String s, int i, int j) {
        final Pair key = new Pair(i, j);
        
        if (cache.containsKey(key)) {
            return cache.get(key);
        } else {
            final String result = longestPalindrome(s, i, j);
            cache.put(key, result);
            return result;
        }
    }
    
    // immutable
    static class Pair {
        private int x;
        private int y;

        public Pair(int x, int y) {
            this.x = x;
            this.y = y;
        }

        @Override
        public int hashCode() {
            return x * 31 + y;
        }

        @Override
        public boolean equals(Object other) {
            if (this == other) return true;
            if (this.hashCode() != other.hashCode()) return false;
            if (!(other instanceof Pair)) return false;

            final Pair o = (Pair) other;
            return this.x == o.x && this.y == o.y;
        }
    }
}
```
{% endif %}

### 动规

{% codesnippet "./code/longest-palindromic-substring-2."+book.suffix, language=book.suffix %}{% endcodesnippet %}


### Manacher’s Algorithm

{% codesnippet "./code/longest-palindromic-substring-3."+book.suffix, language=book.suffix %}{% endcodesnippet %}

## Reference

- [Longest Palindromic Substring Part I | LeetCode](http://articles.leetcode.com/2011/11/longest-palindromic-substring-part-i.html)
- [Longest Palindromic Substring Part II | LeetCode](http://articles.leetcode.com/2011/11/longest-palindromic-substring-part-ii.html)
