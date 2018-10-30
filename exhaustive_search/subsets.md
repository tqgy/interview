# Subsets

Tags: Array, Backtracking, Bit Manipulation, Medium

## Question

- leetcode: [Subsets](https://leetcode.com/problems/subsets/)
- lintcode: [Subsets](http://www.lintcode.com/en/problem/subsets/)

### Problem Statement

Given a set of distinct integers, _nums_, return all possible subsets.

**Note:** The solution set must not contain duplicate subsets.

For example,  
If **_nums_** = `[1,2,3]`, a solution is:
    
    [
      [3],
      [1],
      [2],
      [1,2,3],
      [1,3],
      [2,3],
      [1,2],
      []
    ]

## 题解

子集类问题类似Combination，以输入数组`[1, 2, 3]`分析，根据题意，最终返回结果中子集类的元素应该按照升序排列，故首先需要对原数组进行排序。题目的第二点要求是子集不能重复，至此原题即转化为数学中的组合问题。我们首先尝试使用 DFS 进行求解，大致步骤如下：

1. `[1] -> [1, 2] -> [1, 2, 3]`
2. `[2] -> [2, 3]`
3. `[3]`

将上述过程转化为代码即为对数组遍历，每一轮都保存之前的结果并将其依次加入到最终返回结果中。



### 递归


#### 增量构造法

每个元素，都有两种选择，选或者不选。

```java
// Subsets
// 增量构造法，深搜，时间复杂度O(2^n)，空间复杂度O(n)
public class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        Arrays.sort(nums); // 输出要求有序
        List<List<Integer>> result = new ArrayList<>();
        List<Integer> path = new ArrayList<>();
        subsets(nums, path, 0, result);
        return result;
    }

    private static void subsets(int[] nums, List<Integer> path, int step,
                                List<List<Integer>> result) {
        if (step == nums.length) {
            result.add(new ArrayList<Integer>(path));
            return;
        }
        // 不选nums[step]
        subsets(nums, path, step + 1, result);
        // 选nums[step]
        path.add(nums[step]);
        subsets(nums, path, step + 1, result);
        path.remove(path.size() - 1);
    }
}
```

#### 位向量法

开一个位向量`bool selected[n]`，每个元素可以选或者不选。

```java
// Subsets
// 位向量法，深搜，时间复杂度O(2^n)，空间复杂度O(n)
public class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        Arrays.sort(nums);  // 输出要求有序

        List<List<Integer>> result = new ArrayList<>();
        boolean[] selected = new boolean[nums.length];
        subsets(nums, selected, 0, result);
        return result;
    }

    private static void subsets(int[] nums, boolean[] selected, int step,
                        List<List<Integer>> result) {
        if (step == nums.length) {
            ArrayList<Integer> subset = new ArrayList<>();
            for (int i = 0; i < nums.length; i++) {
                if (selected[i]) subset.add(nums[i]);
            }
            result.add(subset);
            return;
        }
        // 不选S[step]
        selected[step] = false;
        subsets(nums, selected, step + 1, result);
        // 选S[step]
        selected[step] = true;
        subsets(nums, selected, step + 1, result);
    }
}
```


### 迭代

#### 增量构造法

```java
// Subsets
// 迭代版，时间复杂度O(2^n)，空间复杂度O(1)
public class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        Arrays.sort(nums); // 输出要求有序
        List<List<Integer>> result = new ArrayList<>();
        result.add(new ArrayList<>()); // empty subset
        for (int elem : nums) {
            final int n = result.size();
            for (int i = 0; i < n; ++i) { // copy itself
                result.add(new ArrayList<>(result.get(i)));
            }
            for (int i = n; i < result.size(); ++i) {
                result.get(i).add(elem);
            }
        }
        return result;
    }
}
```

#### 二进制法

本方法的前提是：集合的元素不超过int位数。用一个int整数表示位向量，第`i`位为1，则表示选择`S[i]`，为0则不选择。例如 `S={A,B,C,D}`，则`0110=6`表示子集 `{B,C}`。

这种方法最巧妙。因为它不仅能生成子集，还能方便的表示集合的并、交、差等集合运算。设两个集合的位向量分别为$$B_1$$和$$B_2$$，则$$B_1\cup B_2, B_1 \cap B_2, B_1 \triangle B_2$$分别对应集合的并、交、对称差。

二进制法，也可以看做是位向量法，只不过更加优化。
```java
// Subsets
// 二进制法，时间复杂度O(2^n)，空间复杂度O(1)
public class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        Arrays.sort(nums); // 输出要求有序
        List<List<Integer>> result = new ArrayList<>();
        final int n = nums.length;
        ArrayList<Integer> v = new ArrayList<>();

        for (int i = 0; i < 1 << n; i++) {
            for (int j = 0; j < n; j++) {
                if ((i & 1 << j) > 0) v.add(nums[j]);
            }
            result.add(new ArrayList<>(v));
            v.clear();
        }
        return result;
    }
}
```

### Recursive

### Java

```java
public class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        List<Integer> list = new ArrayList<Integer>();
        if (nums == null || nums.length == 0) {
            return result;
        }

        Arrays.sort(nums);
        dfs(nums, 0, list, result);

        return result;
    }

    private void dfs(int[] nums, int pos, List<Integer> list,
                     List<List<Integer>> ret) {

        // add temp result first
        ret.add(new ArrayList<Integer>(list));

        for (int i = pos; i < nums.length; i++) {
            list.add(nums[i]);
            dfs(nums, i + 1, list, ret);
            list.remove(list.size() - 1);
        }
    }
}
```

### 源码分析

Java 和 Python 的代码中在将临时list 添加到最终结果时新生成了对象，(Python 使用`[] + `), 否则最终返回结果将随着`list` 的变化而变化。

**Notice: backTrack(num, i + 1, list, ret);中的『i + 1』不可误写为『pos + 1』，因为`pos`用于每次大的循环，`i`用于内循环，第一次写subsets的时候在这坑了很久... :(**

回溯法可用图示和函数运行的堆栈图来理解，强烈建议**使用图形和递归的思想**分析，以数组`[1, 2, 3]`进行分析。下图所示为`list`及`result`动态变化的过程，箭头向下表示`list.add`及`result.add`操作，箭头向上表示`list.remove`操作。

![Subsets运行递归调用图](../images/subsets.jpg)

### 复杂度分析

对原有数组排序，时间复杂度近似为 $$O(n \log n)$$. 状态数为所有可能的组合数 $$O(2^n)$$, 生成每个状态所需的时间复杂度近似为 $$O(1)$$, 如`[1] -> [1, 2]`, 故总的时间复杂度近似为 $$O(2^n)$$.

使用了临时空间`list`保存中间结果，`list` 最大长度为数组长度，故空间复杂度近似为 $$O(n)$$.

## Reference

- [九章算法 - subsets模板](http://www.jiuzhang.com/solutions/subsets/)
- [LeetCode: Subsets 解题报告 - Yu's Garden - 博客园](http://www.cnblogs.com/yuzhangcmu/p/4211815.html)
