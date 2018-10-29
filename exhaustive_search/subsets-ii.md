## Subsets II


### 描述

Given a collection of integers that might contain duplicates, `S`, return all possible subsets.

Note:

Elements in a subset must be in non-descending order.
The solution set must not contain duplicate subsets.
For example,
If `S = [1,2,2]`, a solution is:

```
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]
```


### 分析

这题有重复元素，但本质上，跟上一题很类似，上一题中元素没有重复，相当于每个元素只能选0或1次，这里扩充到了每个元素可以选0到若干次而已。


### 递归


#### 增量构造法

{% if book.java %}
```java
// Subsets II
// 增量构造法，版本1，时间复杂度O(2^n)，空间复杂度O(n)
public class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);  // 必须排序

        List<List<Integer>> result = new ArrayList<>();
        List<Integer> path = new ArrayList<>();

        dfs(nums, 0, path, result);
        return result;
    }

    private static void dfs(int[] nums, int start, List<Integer> path,
                            List<List<Integer>> result) {
        result.add(new ArrayList<Integer>(path));

        for (int i = start; i < nums.length; i++) {
            if (i != start && nums[i] == nums[i-1]) continue;
            path.add(nums[i]);
            dfs(nums, i + 1, path, result);
            path.remove(path.size() - 1);
        }
    }
}
```
{% endif %}

{% if book.java %}
```java
// Subsets II
// 增量构造法，版本2，时间复杂度O(2^n)，空间复杂度O(n)
public class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);  // 必须排序
        List<List<Integer>> result = new ArrayList<>();
        List<Integer> path = new ArrayList<>(); // 中间结果

        // 记录每个元素的出现次数
        HashMap<Integer, Integer> counterMap = new HashMap<>();
        for (int i : nums) {
            counterMap.put(i, counterMap.getOrDefault(i, 0) + 1);
        }
        // 将HashMap里的pair拷贝到一个数组里
        Pair[] counters = new Pair[counterMap.size()];
        int i = 0;
        for (Map.Entry<Integer, Integer> entry : counterMap.entrySet()) {
            counters[i++] = new Pair(entry.getKey(), entry.getValue());
        }
        Arrays.sort(counters);

        dfs(counters, 0, path, result);
        return result;
    }

    private static void dfs(Pair[] counters, int step, List<Integer> path,
                        List<List<Integer>> result) {
        if (step == counters.length) {
            result.add(new ArrayList<>(path));
            return;
        }

        for (int i = 0; i <= counters[step].value; i++) {
            for (int j = 0; j < i; ++j) {
                path.add(counters[step].key);
            }
            dfs(counters, step + 1, path, result);
            for (int j = 0; j < i; ++j) {
                path.remove(path.size() - 1);
            }
        }
    }

    static class Pair implements Comparable<Pair> {
        int key;
        int value;
        public Pair(int key, int value) {
            this.key = key;
            this.value = value;
        }
        @Override
        public int compareTo(Pair o) {
            if (this.key < o.key) return -1;
            else if (this.key > o.key) return 1;
            else {
                return this.value - o.value;
            }
        }
    }
}
```
{% endif %}


#### 位向量法

{% if book.java %}
```java
// Subsets II
// 位向量法，时间复杂度O(2^n)，空间复杂度O(n)
public class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);  // 必须排序
        List<List<Integer>> result = new ArrayList<>();
        // 记录每个元素的出现次数
        HashMap<Integer, Integer> counterMap = new HashMap<>();
        for (int i : nums) {
            counterMap.put(i, counterMap.getOrDefault(i, 0) + 1);
        }
        // 将HashMap里的pair拷贝到一个数组里
        Pair[] counters = new Pair[counterMap.size()];
        int i = 0;
        for (Map.Entry<Integer, Integer> entry : counterMap.entrySet()) {
            counters[i++] = new Pair(entry.getKey(), entry.getValue());
        }
        Arrays.sort(counters);

        // 每个元素选择了多少个
        HashMap<Integer, Integer> selected = new HashMap<>();
        for (Pair p : counters) {
            selected.put(p.key, 0 );
        }

        dfs(nums, counters, selected, 0, result);
        return result;
    }

    private static void dfs(int[] nums, Pair[] counters, HashMap<Integer, Integer> selected,
                            int step, List<List<Integer>> result) {
        if (step == counters.length) {
            ArrayList<Integer> subset = new ArrayList<>();
            for (Pair p : counters) {
                for (int i = 0; i < selected.get(p.key); ++i) {
                    subset.add(p.key);
                }
            }
            result.add(subset);
            return;
        }

        for (int i = 0; i <= counters[step].value; i++) {
            selected.put(counters[step].key, i);
            dfs(nums, counters, selected, step + 1, result);
        }
    }
    static class Pair implements Comparable<Pair> {
        int key;
        int value;
        public Pair(int key, int value) {
            this.key = key;
            this.value = value;
        }

        @Override
        public int compareTo(Pair o) {
            if (this.key < o.key) return -1;
            else if (this.key > o.key) return 1;
            else {
                return this.value - o.value;
            }
        }
    }
}
```
{% endif %}

### 迭代


#### 增量构造法

{% if book.java %}
```java
// Subsets II
// 增量构造法
// 时间复杂度O(2^n)，空间复杂度O(1)
public class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);  // 必须排序
        List<List<Integer>> result = new ArrayList<>();
        result.add(new ArrayList<Integer>());

        int previous_size = 0;
        for (int i = 0; i < nums.length; ++i) {
            final int size = result.size();
            for (int j = 0; j < size; ++j) {
                if (i == 0 || nums[i] != nums[i-1] || j >= previous_size) {
                    result.add(new ArrayList<>(result.get(j)));
                    result.get(result.size() - 1).add(nums[i]);
                }
            }
            previous_size = size;
        }
        return result;
    }
}
```
{% endif %}


#### 二进制法

{% if book.java %}
```java
// Subsets II
// 二进制法，时间复杂度O(2^n)，空间复杂度O(1)
public class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);  // 必须排序
        // 用 set 去重，不能用 unordered_set，因为输出要求有序
        LinkedHashSet<ArrayList<Integer>> result = new LinkedHashSet<>();
        final int n = nums.length;
        ArrayList<Integer> v = new ArrayList<>();

        for (int i = 0; i < 1 << n; ++i) {
            for (int j = 0; j < n; ++j) {
                if ((i & 1 << j) > 0)
                    v.add(nums[j]);
            }
            result.add(new ArrayList<>(v));
            v.clear();
        }
        List<List<Integer>> realResult = new ArrayList<>();
        for (ArrayList<Integer> list : result) {
            realResult.add(list);
        }
        return realResult;
    }
}
```
{% endif %}


### 相关题目

* [Subsets](subsets.md)
