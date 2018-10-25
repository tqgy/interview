## Palindrome Partitioning


### 描述

Given a string s, partition s such that every substring of the partition is a palindrome.

Return all possible palindrome partitioning of s.

For example, given `s = "aab"`,
Return

```
  [
    ["aa","b"],
    ["a","a","b"]
  ]
```

### 分析

在每一步都可以判断中间结果是否为合法结果，用回溯法。

一个长度为n的字符串，有`n-1`个地方可以砍断，每个地方可断可不断，因此复杂度为$$O(2^{n-1})$$


### 深搜1

{% if book.java %}
```java
// Palindrome Partitioning
// 时间复杂度O(2^n)，空间复杂度O(n)
public class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> result = new ArrayList<>();
        List<String> path = new ArrayList<>(); // 一个partition方案
        dfs(s, path, result, 0, 1);
        return result;
    }

    // prev 表示前一个隔板, start 表示当前隔板
    private static void dfs(String s, List<String> path,
                            List<List<String>> result, int prev, int start) {
        if (start == s.length()) { // 最后一个隔板
            if (isPalindrome(s, prev, start - 1)) { // 必须使用
                path.add(s.substring(prev, start));
                result.add(new ArrayList<>(path));
                path.remove(path.size() - 1);
            }
            return;
        }
        // 不断开
        dfs(s, path, result, prev, start + 1);
        // 如果[prev, start-1] 是回文，则可以断开，也可以不断开（上一行已经做了）
        if (isPalindrome(s, prev, start - 1)) {
            // 断开
            path.add(s.substring(prev, start));
            dfs(s, path, result, start, start + 1);
            path.remove(path.size() - 1);
        }
    }

    private static boolean isPalindrome(String s, int start, int end) {
        while (start < end && s.charAt(start) == s.charAt(end)) {
            ++start;
            --end;
        }
        return start >= end;
    }
}
```
{% endif %}

### 深搜2

另一种写法，更加简洁。这种写法也在 Combination Sum, Combination Sum II 中出现过。

{% if book.java %}
```java
// Palindrome Partitioning
// 时间复杂度O(2^n)，空间复杂度O(n)
public class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> result = new ArrayList<>();
        List<String> path = new ArrayList<>();  // 一个partition方案
        dfs(s, path, result, 0);
        return result;
    }
    // 搜索必须以s[start]开头的partition方案
    private static void dfs(String s, List<String> path,
                            List<List<String>> result, int start) {
        if (start == s.length()) {
            result.add(new ArrayList<>(path));
            return;
        }
        for (int i = start; i < s.length(); i++) {
            if (isPalindrome(s, start, i)) { // 从i位置砍一刀
                path.add(s.substring(start, i+1));
                dfs(s, path, result, i + 1);  // 继续往下砍
                path.remove(path.size() - 1); // 撤销上上行
            }
        }
    }
    private static boolean isPalindrome(String s, int start, int end) {
        while (start < end && s.charAt(start) == s.charAt(end)) {
            ++start;
            --end;
        }
        return start >= end;
    }
}
```
{% endif %}


### 动规

{% if book.java %}
```java
// Palindrome Partitioning
// 动规，时间复杂度O(n^2)，空间复杂度O(1)
public class Solution {
    public List<List<String>> partition(String s) {
        final int n = s.length();
        boolean[][] p = new boolean[n][n]; // whether s[i,j] is palindrome
        for (int i = n - 1; i >= 0; --i)
            for (int j = i; j < n; ++j)
                p[i][j] = s.charAt(i) == s.charAt(j) &&
                        ((j - i < 2) || p[i + 1][j - 1]);

        List<List<String>>[] subPalins = new ArrayList[n]; // sub palindromes of s[0,i]
        for (int i = 0; i < n; ++i) subPalins[i] = new ArrayList<>();
        for (int i = n - 1; i >= 0; --i) {
            for (int j = i; j < n; ++j)
                if (p[i][j]) {
                    String palindrome = s.substring(i, j+1);
                    if (j + 1 < n) {
                        for (List<String> v : subPalins[j + 1]) {
                            ArrayList<String> tmp = new ArrayList<>(v);
                            tmp.add(0, palindrome);
                            subPalins[i].add(tmp);
                        }
                    } else {
                        ArrayList<String> tmp = new ArrayList<>();
                        tmp.add(palindrome);
                        subPalins[i].add(tmp);
                    }
                }
        }
        return subPalins[0];
    }
}
```
{% endif %}

### 相关题目

* [Palindrome Partitioning II](palindrome-partitioning-ii.md)
