# Permutation Index II

## Question

- lintcode: [(198) Permutation Index II](http://www.lintcode.com/en/problem/permutation-index-ii/)

### Problem Statement

Given a permutation which may contain repeated numbers, find its index in all
the permutations of these numbers, which are ordered in lexicographical order.
The index begins at 1.

#### Example

Given the permutation `[1, 4, 2, 2]`, return `3`.

## 题解

题 [Permutation Index](http://algorithm.yuanbin.me/zh-hans/exhaustive_search/permutation_index.html) 的扩展，这里需要考虑重复元素，有无重复元素最大的区别在于原来的`1!, 2!, 3!...`等需要除以重复元素个数的阶乘，颇有点高中排列组合题的味道。记录重复元素个数同样需要动态更新，引入哈希表这个万能的工具较为方便。

### Java

```java
public class Solution {
    /**
     * @param A an integer array
     * @return a long integer
     */
    public long permutationIndexII(int[] A) {
        if (A == null || A.length == 0) return 0L;

        Map<Integer, Integer> hashmap = new HashMap<Integer, Integer>();
        long index = 1, fact = 1, multiFact = 1;
        for (int i = A.length - 1; i >= 0; i--) {
            // collect its repeated times and update multiFact
            if (hashmap.containsKey(A[i])) {
                hashmap.put(A[i], hashmap.get(A[i]) + 1);
                multiFact *= hashmap.get(A[i]);
            } else {
                hashmap.put(A[i], 1);
            }
            // get rank every turns
            int rank = 0;
            for (int j = i + 1; j < A.length; j++) {
                if (A[i] > A[j]) rank++;
            }
            // do divide by multiFact
            index += rank * fact / multiFact;
            fact *= (A.length - i);
        }

        return index;
    }
}
```

### 源码分析

在计算重复元素个数的阶乘时需要注意更新`multiFact`的值即可，不必每次都去计算哈希表中的值。对元素`A[i]`需要加入哈希表 - `hash.put(A[i], 1);`，设想一下`2, 2, 1, 1`的计算即可知。

### 复杂度分析

双重 for 循环，时间复杂度为 $$O(n^2)$$, 使用了哈希表，空间复杂度为 $$O(n)$$.
