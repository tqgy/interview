# Search a 2D Matrix II

## Question

- leetcode: [Search a 2D Matrix II | LeetCode OJ](https://leetcode.com/problems/search-a-2d-matrix-ii/)
- lintcode: [(38) Search a 2D Matrix II](http://lintcode.com/en/problem/search-a-2d-matrix-ii/)

### Problem Statement

Write an efficient algorithm that searches for a value in an m x n matrix, return the occurrence of it.

This matrix has the following properties:

* Integers in each row are sorted from left to right.
* Integers in each column are sorted from up to bottom.
* No duplicate integers in each row or column.

#### Example

Consider the following matrix:

    [1, 3, 5, 7],
    [2, 4, 7, 8],
    [3, 5, 9, 10]

Given target = **3**, return **2**.

#### Challenge

O(m+n) time and O(1) extra space

## 题解 - 自右上而左下

1. 复杂度要求——O(m+n) time and O(1) extra space，同时输入只满足自顶向下和自左向右的升序，行与行之间不再有递增关系，与上题有较大区别。时间复杂度为线性要求，因此可从元素排列特点出发，从一端走向另一端无论如何都需要m+n步，因此可分析对角线元素。
2. 首先分析如果从左上角开始搜索，由于元素升序为自左向右和自上而下，因此如果target大于当前搜索元素时还有两个方向需要搜索，不太合适。
3. 如果从右上角开始搜索，由于左边的元素一定不大于当前元素，而下面的元素一定不小于当前元素，因此每次比较时均可排除一列或者一行元素（大于当前元素则排除当前行，小于当前元素则排除当前列，由矩阵特点可知），可达到题目要求的复杂度。

**在遇到之前没有遇到过的复杂题目时，可先使用简单的数据进行测试去帮助发现规律。**

### Java

```java
public class Solution {
    /**
     * @param matrix: A list of lists of integers
     * @param: A number you want to search in the matrix
     * @return: An integer indicate the occurrence of target in the given matrix
     */
    public int searchMatrix(int[][] matrix, int target) {
        int occurrence = 0;
        if (matrix == null || matrix[0] == null) {
            return occurrence;
        }

        int row = 0, col = matrix[0].length - 1;
        while (row >= 0 && row < matrix.length && col >= 0 && col < matrix[0].length) {
            if (matrix[row][col] == target) {
                occurrence++;
                col--;
            } else if (matrix[row][col] > target) {
                col--;
            } else {
                row++;
            }
        }

        return occurrence;
    }
}
```

### 源码分析

1. 首先对输入做异常处理，不仅要考虑到matrix为空串，还要考虑到matrix[0]也为空串。
2. 注意循环终止条件。
3. 在找出`target`后应继续向左搜索其他可能相等的元素，下方比当前元素大，故排除此列。

**严格来讲每次取二维矩阵元素前都应该进行 null 检测。**

### 复杂度分析

由于每行每列遍历一次，故时间复杂度为 $$O(m + n)$$.


### 分析

已经排好了序，用二分查找。


### 重新实现 lower_bound 和 upper_bound

{% codesnippet "./code/search-for-a-range."+book.suffix, language=book.suffix %}{% endcodesnippet %}

## Reference

[Searching a 2D Sorted Matrix Part II | LeetCode](http://articles.leetcode.com/2010/10/searching-2d-sorted-matrix-part-ii.html)
