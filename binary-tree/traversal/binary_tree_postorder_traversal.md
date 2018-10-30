# Binary Tree Postorder Traversal

Tags: Tree, Stack, Hard

## Question

- leetcode: [Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)
- lintcode: [Binary Tree Postorder Traversal](http://www.lintcode.com/en/problem/binary-tree-postorder-traversal/)

### Problem Statement

Given a binary tree, return the _postorder_ traversal of its nodes' values.

For example:  
Given binary tree `{1,#,2,3}`,  

    
    
    
       1
        \
         2
        /
       3
    

return `[3,2,1]`.

**Note:** Recursive solution is trivial, could you do it iteratively?


## 题解1 - 递归

首先使用递归便于理解。

### Java - Divide and Conquer

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();
        if (root != null) {
            List<Integer> left = postorderTraversal(root.left);
            result.addAll(left);
            List<Integer> right = postorderTraversal(root.right);
            result.addAll(right);
            result.add(root.val);
        }

        return result;
    }
}
```

### Java - Traversal

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    private List<Integer> result = new ArrayList<Integer>();

    public List<Integer> postorderTraversal(TreeNode root) {
        if (root != null) {
            postorderTraversal(root.left);
            postorderTraversal(root.right);
            result.add(root.val);
        }
        return result;
    }
}
```

### 源码分析

递归版的太简单了，没啥好说的，注意入栈顺序。

### 复杂度分析

时间复杂度近似为 $$O(n)$$.

## 题解2 - 迭代

使用递归写后序遍历那是相当的简单，我们来个不使用递归的迭代版。整体思路仍然为「左右根」，那么怎么才能知道什么时候该访问根节点呢？问题即转化为如何保证左右子节点一定先被访问到？由于入栈之后左右节点已无法区分，因此需要区分左右子节点是否被访问过(加入到最终返回结果中)。除了有左右节点的情况，根节点也可能没有任何子节点，此时也可直接将其值加入到最终返回结果中。

### Java

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();
        Deque<TreeNode> stack = new ArrayDeque<TreeNode>();

        if (root != null) stack.push(root);
        TreeNode prev = null;
        while (!stack.isEmpty()) {
            TreeNode curr = stack.peek();
            boolean noChild = (curr.left == null && curr.right == null);
            boolean childVisited = false;
            if (prev != null && (curr.left == prev || curr.right == prev)) {
                childVisited = true;
            }
            if (noChild || childVisited) {
                curr = stack.pop();
                result.add(curr.val);
                prev = curr;
            } else {
                if (curr.right != null) stack.push(curr.right);
                if (curr.left != null) stack.push(curr.left);
            }
        }

        return result;
    }
}
```

### 源码分析

遍历顺序为『左右根』，判断根节点是否应该从栈中剔除有两种条件，一为无子节点，二为子节点已遍历过。判断子节点是否遍历过需要排除`prev == null` 的情况，因为 prev 初始化为 null. Java 中在 while 内部新建 curr 变量而不是复用 root 有一定性能提升，不知是不是 JVM 运行时优化导致的。

**将递归写成迭代的难点在于如何在迭代中体现递归本质及边界条件的确立，可使用简单示例和纸上画出栈调用图辅助分析。**

### 复杂度分析

最坏情况下栈内存储所有节点，空间复杂度近似为 $$O(n)$$, 每个节点遍历两次或以上，时间复杂度近似为 $$O(n)$$.

## 题解3 - 反转先序遍历

要想得到『左右根』的后序遍历结果，我们发现只需将『根右左』的结果转置即可，而先序遍历通常为『根左右』，故改变『左右』的顺序即可，所以如此一来后序遍历的非递归实现起来就非常简单了。

### Java

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();
        Deque<TreeNode> stack = new ArrayDeque<TreeNode>();
        if (root != null) stack.push(root);

        while (!stack.isEmpty()) {
            TreeNode curr = stack.pop();
            result.add(curr.val);
            if (curr.left != null) stack.push(curr.left);
            if (curr.right != null) stack.push(curr.right);
        }

        Collections.reverse(result);
        return result;
    }
}
```

### 源码分析

注意入栈的顺序和最后转置即可。

### 复杂度分析

同先序遍历。

### 分析

用栈或者Morris遍历。


### 栈

{% if book.java %}
```java
// Binary Tree Postorder Traversal
// 使用栈，时间复杂度O(n)，空间复杂度O(n)
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        ArrayList<Integer> result = new ArrayList<>();
        Stack<TreeNode> s = new Stack<>();
        /* p，正在访问的结点，q，刚刚访问过的结点*/
        TreeNode p = root;
        TreeNode q = null;

        do {
            while (p != null) { /* 往左下走*/
                s.push(p);
                p = p.left;
            }
            q = null;
            while (!s.empty()) {
                p = s.pop();
                /* 右孩子不存在或已被访问，访问之*/
                if (p.right == q) {
                    result.add(p.val);
                    q = p; /* 保存刚访问过的结点*/
                } else {
                    /* 当前结点不能访问，需第二次进栈*/
                    s.push(p);
                    /* 先处理右子树*/
                    p = p.right;
                    break;
                }
            }
        } while (!s.empty());

        return result;
    }
}
```
{% endif %}


### Morris后序遍历

{% codesnippet "./code/binary-tree-postorder-traversal-2."+book.suffix, language=book.suffix %}{% endcodesnippet %}


## Reference

- [[leetcode]Binary Tree Postorder Traversal @ Python - 南郭子綦](http://www.cnblogs.com/zuoyuan/p/3720846.html) - 解释清晰
- [更简单的非递归遍历二叉树的方法](http://zisong.me/post/suan-fa/geng-jian-dan-de-bian-li-er-cha-shu-de-fang-fa) - 比较新颖和简洁的实现
