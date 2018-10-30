# Binary Tree Inorder Traversal

Tags: Tree, Hash Table, Stack, Medium

## Question

- leetcode: [Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)
- lintcode: [Binary Tree Inorder Traversal](http://www.lintcode.com/en/problem/binary-tree-inorder-traversal/)

### Problem Statement

Given a binary tree, return the _inorder_ traversal of its nodes' values.

For example:  
Given binary tree `[1,null,2,3]`,  

    
    
    
       1
        \
         2
        /
       3
    

return `[1,3,2]`.

**Note:** Recursive solution is trivial, could you do it iteratively?

## 题解1 - 递归版

中序遍历的访问顺序为『先左再根后右』，递归版最好理解，递归调用时注意返回值和递归左右子树的顺序即可。

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
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();
        helper(root, result);
        return result;
    }

    private void helper(TreeNode root, List<Integer> ret) {
        if (root != null) {
            helper(root.left, ret);
            ret.add(root.val);
            helper(root.right, ret);
        }
    }
}
```

### 源码分析

Python 这种动态语言在写递归时返回结果好处理点，无需声明类型。通用的方法为在递归函数入口参数中传入返回结果，
也可使用分治的方法替代辅助函数。Java 中 helper 的输入参数中 ret 不能和 inorderTraversal 中的 result 一样。

### 复杂度分析

树中每个节点都需要被访问常数次，时间复杂度近似为 $$O(n)$$. 未使用额外辅助空间。

## 题解2  - 迭代版

使用辅助栈改写递归程序，中序遍历没有前序遍历好写，其中之一就在于入栈出栈的顺序和限制规则。我们采用「左根右」的访问顺序可知主要由如下四步构成。

1. 首先需要一直对左子树迭代并将非空节点入栈
2. 节点指针为空后不再入栈
3. 当前节点为空时进行出栈操作，并访问栈顶节点
4. 将当前指针p用其右子节点替代

步骤2,3,4对应「左根右」的遍历结构，只是此时的步骤2取的左值为空。

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
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();
        Deque<TreeNode> stack = new ArrayDeque<TreeNode>();

        TreeNode curr = root;
        while (curr != null || !stack.isEmpty()) {
            if (curr != null) {
                stack.push(curr);
                curr = curr.left;
            } else {
                curr = stack.pop();
                result.add(curr.val);
                curr = curr.right;
            }
        }

        return result;
    }
}
```

### 源码分析

使用栈的思想模拟递归，注意迭代的演进和边界条件即可。Java 中新建变量 curr 而不是复用 root 观察下来有一点性能提升。

### 复杂度分析

最坏情况下栈保存所有节点，空间复杂度 $$O(n)$$, 时间复杂度 $$O(n)$$.

### 分析

用栈或者Morris遍历。


### 栈

```java
// Binary Tree Inorder Traversal
// 使用栈，时间复杂度O(n)，空间复杂度O(n)
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        ArrayList<Integer> result = new ArrayList<>();
        Stack<TreeNode> s = new Stack<>();
        TreeNode p = root;

        while (!s.empty() || p != null) {
            if (p != null) {
                s.push(p);
                p = p.left;
            } else {
                p = s.pop();
                result.add(p.val);
                p = p.right;
            }
        }
        return result;
    }
}
```

### Morris中序遍历

{% codesnippet "./code/binary-tree-inorder-traversal-2."+book.suffix, language=book.suffix %}{% endcodesnippet %}

## Reference
