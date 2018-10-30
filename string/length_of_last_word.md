# Length of Last Word

Tags: String, Easy

## Question

- leetcode: [Length of Last Word](https://leetcode.com/problems/length-of-last-word/)
- lintcode: [Length of Last Word](http://www.lintcode.com/en/problem/length-of-last-word/)

### Problem Statement

Given a string _s_ consists of upper/lower-case alphabets and empty space
characters `' '`, return the length of last word in the string.

If the last word does not exist, return 0.

**Note:** A word is defined as a character sequence consists of non-space characters only.

For example,  
Given _s_ = `"Hello World"`,  
return `5`.

## 题解

关键点在于确定最后一个字符串之前的空格，此外还需要考虑末尾空格这一特殊情况，容易想到的是利用一前一后两个索引记录，最后相减即可。但其实可以巧妙地直接利用非空字符串长度表示。除了通常简单粗暴的方法，我们还可以尝试使用正则表达式这一利器对字符串进行处理。

### Java

```java
public class Solution {
    public int lengthOfLastWord(String s) {
        if (s == null || s.isEmpty()) return 0;

        int len = 0;
        for (int i = s.length() - 1; i >= 0; i--) {
            if (s.charAt(i) == ' ') {
                if (len > 0) return len;
            } else {
                len++;
            }
        }

        return len;
    }
}
```

### 源码分析

注意检查输入参数和索引即可，当前长度信息和当前索引字符是否为空格这两种信息可以结合使用避免硬标记。

### 复杂度分析

遍历一次，时间复杂度 $$O(n)$$，不复制源字符串，空间复杂度 $$O(1)$$.

### 分析

模拟。先从右到左找到第一个字母，然后从右到左找到第一个非字母，二者的距离就是最后一个word的长度。


### 代码

{% codesnippet "./code/length-of-last-word."+book.suffix, language=book.suffix %}{% endcodesnippet %}
