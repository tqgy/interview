# Find Minimum in Rotated Sorted Array II

## Question

- leetcode: [Find Minimum in Rotated Sorted Array II | LeetCode OJ](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/)
- lintcode: [(160) Find Minimum in Rotated Sorted Array II](http://www.lintcode.com/en/problem/find-minimum-in-rotated-sorted-array-ii/)

### Problem Statement

Suppose a sorted array is rotated at some pivot unknown to you beforehand.

(i.e., 0 1 2 4 5 6 7 might become 4 5 6 7 0 1 2).

Find the minimum element.

The array may contain duplicates.

#### Example

Given [4,4,5,6,7,0,1,2] return 0

## 题解

由于此题输入可能有重复元素，因此在`num[mid] == num[end]`时无法使用二分的方法缩小start或者end的取值范围。此时只能使用递增start/递减end逐步缩小范围。


### Java

```java
public class Solution {
    /**
     * @param num: a rotated sorted array
     * @return: the minimum number in the array
     */
    public int findMin(int[] num) {
        if (num == null || num.length == 0) return Integer.MIN_VALUE;

        int lb = 0, ub = num.length - 1;
        // case1: num[0] < num[num.length - 1]
        // if (num[lb] < num[ub]) return num[lb];

        // case2: num[0] > num[num.length - 1] or num[0] < num[num.length - 1]
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (num[mid] < num[ub]) {
                ub = mid;
            } else if (num[mid] > num[ub]){
                lb = mid;
            } else {
                ub--;
            }
        }

        return Math.min(num[lb], num[ub]);
    }
}
```

### 源码分析

注意`num[mid] > num[ub]`时应递减 ub 或者递增 lb.

### 复杂度分析

最坏情况下 $$O(n)$$, 平均情况下 $$O(\log n)$$.


### 分析

同 [Find Minimum in Rotated Sorted Array](find-minimum-in-rotated-sorted-array.md) 类似，要判断“断层”在左边还是右边。

* 若`A[mid] < A[right]`，则区间`[mid,right]`一定递增，断层一定在左边
* 若`A[mid] > A[right]`，则区间`[left,mid]`一定递增，断层一定在右边
* 若`A[mid] == A[right]` 确定不了，这个时候，断层既可能在左边，也可能在右边，所以我们不能扔掉一半，不过这时，我们可以`--right`扔掉一个

本题还有另一种思路，

* 若`A[left] < A[mid]`，则区间`[left,mid]`一定递增，断层一定在右边
* 若`A[left] > A[mid]`，则区间`[mid,right]`一定递增，断层一定在左边
* 若`A[left] == A[mid]` 确定不了，这个时候，断层既可能在左边，也可能在右边，所以我们不能扔掉一半，不过这时，我们可以`++left`扔掉一

注意，第三种情况，我们认为可以`++left`扔掉一个，这个做法是不对的，因为数组被分成两段后，两段分别是递增的，`left`这个元素有可能是全局最小值，不能贸然扔掉。而在前一种思路中，`end`可以扔掉，因为`end`在右边，它的左边必然有小于或等于它的元素，所以可以放心`--end`。


### 代码

{% if book.java %}
{% codesnippet "./code/find-minimum-in-rotated-sorted-array-ii."+book.suffix, language=book.suffix %}{% endcodesnippet %}
{% endif %}