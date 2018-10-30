# Search in Rotated Sorted Array II

## Question

- leetcode: [Search in Rotated Sorted Array II | LeetCode OJ](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/)
- lintcode: [(63) Search in Rotated Sorted Array II](http://www.lintcode.com/en/problem/search-in-rotated-sorted-array-ii/)

### Problem Statement

Follow up for "Search in Rotated Sorted Array":
What if _duplicates_ are allowed?

Would this affect the run-time complexity? How and why?

Write a function to determine if a given target is in the array.

## 题解

仔细分析此题和之前一题的不同之处，前一题我们利用`A[start] < A[mid]`这一关键信息，而在此题中由于有重复元素的存在，在`A[start] == A[mid]`时无法确定有序数组，此时只能依次递增start/递减end以缩小搜索范围，时间复杂度最差变为O(n)。

### Java

```java
public class Solution {
    /**
     * param A : an integer ratated sorted array and duplicates are allowed
     * param target :  an integer to be search
     * return : a boolean
     */
    public boolean search(int[] A, int target) {
        if (A == null || A.length == 0) return false;

        int lb = 0, ub = A.length - 1;
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (A[mid] == target) return true;

            if (A[mid] > A[lb]) {
                // case1: numbers between lb and mid are sorted
                if (A[lb] <= target && target <= A[mid]) {
                    ub = mid;
                } else {
                    lb = mid;
                }
            } else if (A[mid] < A[lb]) {
                // case2: numbers between mid and ub are sorted
                if (A[mid] <= target && target <= A[ub]) {
                    lb = mid;
                } else {
                    ub = mid;
                }
            } else {
                // case3: A[mid] == target
                lb++;
            }
        }

        if (target == A[lb] || target == A[ub]) {
            return true;
        }
        return false;
    }
}
```

### 源码分析

在`A[start] == A[mid]`时递增start序号即可。

### 复杂度分析

最差情况下 $$O(n)$$, 平均情况下 $$O(\log n)$$.


### 分析

允许重复元素，则上一题中如果`A[left] <= A[mid]`,那么`[left,mid]`为递增序列的假设就不能成立了，比如`[1,3,1,1,1]`。

既然`A[left] <= A[mid]`不能确定递增，那就把它拆分成两个条件：

* 若`A[left] < A[mid]`，则区间`[left,mid]`一定递增
* 若`A[left] == A[mid]` 确定不了，那就`left++`，往下看一步即可。


### 代码

```java
// Search in Rotated Sorted Array II
// Time Complexity: O(n)，Space Complexity: O(1)
public class Solution {
    public boolean search(int[] nums, int target) {
        int first = 0, last = nums.length;
        while (first != last) {
            final int mid = first  + (last - first) / 2;
            if (nums[mid] == target)
                return true;
            if (nums[first] < nums[mid]) {
                if (nums[first] <= target && target < nums[mid])
                    last = mid;
                else
                    first = mid + 1;
            } else if (nums[first] > nums[mid]) {
                if (nums[mid] < target && target <= nums[last-1])
                    first = mid + 1;
                else
                    last = mid;
            } else
                //skip duplicate one
                first++;
        }
        return false;
    }
};
```