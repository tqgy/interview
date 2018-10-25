# Unique Binary Search Trees II

## Question

- leetcode: [Unique Binary Search Trees II | LeetCode OJ](https://leetcode.com/problems/unique-binary-search-trees-ii/)
- lintcode: [(164) Unique Binary Search Trees II](http://www.lintcode.com/en/problem/unique-binary-search-trees-ii/)

```
Given n, generate all structurally unique BST's
(binary search trees) that store values 1...n.

Example
Given n = 3, your program should return all 5 unique BST's shown below.

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

## 题解

题 [Unique Binary Search Trees](http://algorithm.yuanbin.me/zh-hans/math_and_bit_manipulation/unique_binary_search_trees.html) 的升级版，这道题要求的不是二叉搜索树的数目，而是要构建这样的树。分析方法仍然是可以借鉴的，核心思想为利用『二叉搜索树』的定义，如果以 i 为根节点，那么其左子树由[1, i - 1]构成，右子树由[i + 1, n] 构成。要构建包含1到n的二叉搜索树，只需遍历1到n中的数作为根节点，以`i`为界将数列分为左右两部分，小于`i`的数作为左子树，大于`i`的数作为右子树，使用两重循环将左右子树所有可能的组合链接到以`i`为根节点的节点上。

容易看出，以上求解的思路非常适合用递归来处理，接下来便是设计递归的终止步、输入参数和返回结果了。由以上分析可以看出递归严重依赖数的区间和`i`，那要不要将`i`也作为输入参数的一部分呢？首先可以肯定的是必须使用『数的区间』这两个输入参数，又因为`i`是随着『数的区间』这两个参数的，故不应该将其加入到输入参数中。分析方便，不妨设『数的区间』两个输入参数分别为`start`和`end`.

接下来谈谈终止步的确定，由于根据`i`拆分左右子树的过程中，递归调用的方法中入口参数会缩小，且存在`start <= i <= end`, 故终止步为`start > end`. 那要不要对`start == end`返回呢？保险起见可以先写上，后面根据情况再做删改。


### Java

```java
/**
 * Definition of TreeNode:
 * public class TreeNode {
 *     public int val;
 *     public TreeNode left, right;
 *     public TreeNode(int val) {
 *         this.val = val;
 *         this.left = this.right = null;
 *     }
 * }
 */
public class Solution {
    /**
     * @paramn n: An integer
     * @return: A list of root
     */
    public List<TreeNode> generateTrees(int n) {
        return helper(1, n);
    }

    private List<TreeNode> helper(int start, int end) {
        List<TreeNode> result = new ArrayList<TreeNode>();
        if (start > end) {
            result.add(null);
            return result;
        }

        for (int i = start; i <= end; i++) {
            // generate left and right sub tree
            List<TreeNode> leftTree = helper(start, i - 1);
            List<TreeNode> rightTree = helper(i + 1, end);
            // link left and right sub tree to root(i)
            for (TreeNode lnode: leftTree) {
                for (TreeNode rnode: rightTree) {
                    TreeNode root = new TreeNode(i);
                    root.left = lnode;
                    root.right = rnode;
                    result.add(root);
                }
            }
        }

        return result;
    }
}
```

### 源码分析

1. 异常处理，返回None/NULL/null.
2. 遍历start->end, 递归得到左子树和右子树。
3. 两重`for`循环将左右子树的所有可能组合添加至最终返回结果。

注意 DFS 辅助方法`helper`中左右子树及返回根节点的顺序。

### 复杂度分析

递归调用，一个合理的数组区间将生成新的左右子树，时间复杂度为指数级别，使用的临时空间最后都被加入到最终结果，空间复杂度(堆)近似为 $$O(1)$$, 栈上的空间较大。

## Reference

- [Code Ganker: Unique Binary Search Trees II -- LeetCode](http://codeganker.blogspot.com/2014/04/unique-binary-search-trees-ii-leetcode.html)
- [水中的鱼: [LeetCode] Unique Binary Search Trees II, Solution](http://fisherlei.blogspot.com/2013/03/leetcode-unique-binary-search-trees-ii.html)
- [Accepted Iterative Java solution. - Leetcode Discuss](https://leetcode.com/discuss/22821/accepted-iterative-java-solution)
- [Unique Binary Search Trees II 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/unique-binary-search-trees-ii/)
