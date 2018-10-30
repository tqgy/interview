# Binary Tree Preorder Traversal

Tags: Tree, Stack, Medium

## Question

- leetcode: [Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)
- lintcode: [Binary Tree Preorder Traversal](http://www.lintcode.com/en/problem/binary-tree-preorder-traversal/)

### Problem Statement

Given a binary tree, return the _preorder_ traversal of its nodes' values.

For example:  
Given binary tree `{1,#,2,3}`,  

    
    
    
       1
        \
         2
        /
       3
    

return `[1,2,3]`.

**Note:** Recursive solution is trivial, could you do it iteratively?

## 题解1 - 递归

**面试时不推荐递归这种做法。**

递归版很好理解，首先判断当前节点(根节点)是否为`null`，是则返回空vector，否则先返回当前节点的值，然后对当前节点的左节点递归，最后对当前节点的右节点递归。递归时对返回结果的处理方式不同可进一步细分为遍历和分治两种方法。


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
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();
        if (root != null) {
            // Divide
            List<Integer> left = preorderTraversal(root.left);
            List<Integer> right = preorderTraversal(root.right);
            // Merge
            result.add(root.val);
            result.addAll(left);
            result.addAll(right);
        }
        
        return result;
    }
}
```

### Java - Traversal

```
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

    public List<Integer> preorderTraversal(TreeNode root) {
        if (root != null) {
            result.add(root.val);
            preorderTraversal(root.left);
            preorderTraversal(root.right);
        }

        return result;
    }
}
```

### 源码分析

使用遍历的方法保存递归返回结果需要使用辅助递归函数`traverse`，将结果作为参数传入递归函数中，传值时注意应使用`vector`的引用。另外一个方法则是使用类的私有变量保存最终结果。
分治方法首先分开计算各结果，最后合并到最终结果中。
C++ 中由于是使用vector, 将新的vector插入另一vector不能再使用push_back, 而应该使用insert。
Java 中使用`addAll`方法.

### 复杂度分析

遍历树中节点，时间复杂度 $$O(n)$$, 遍历的方法未使用额外空间，分治的方法生成了临时变量，最差情况下空间复杂度为 $$(n)$$.

## 题解2 - 迭代

迭代时需要利用栈来保存遍历到的节点，纸上画图分析后发现应首先进行出栈抛出当前节点，保存当前节点的值，随后将右、左节点分别入栈(注意入栈顺序，先右后左)，迭代到其为叶子节点(NULL)为止。

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
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();

        Deque<TreeNode> stack = new ArrayDeque<TreeNode>();
        if (root != null) stack.push(root);
        while (!stack.isEmpty()) {
            root = stack.pop();
            result.add(root.val);
            if (root.right != null) stack.push(root.right);
            if (root.left != null) stack.push(root.left);
        }

        return result;
    }
}
```

### 源码分析

1. 对root进行异常处理
2. 将root压入栈
3. 循环终止条件为栈s为空，所有元素均已处理完
4. 访问当前栈顶元素(首先取出栈顶元素，随后pop掉栈顶元素)并存入最终结果
5. 将右、左节点分别压入栈内，以便取元素时为先左后右。
6. 返回最终结果

其中步骤4,5,6为迭代的核心，对应前序遍历「根左右」。

所以说到底，**使用迭代，只不过是另外一种形式的递归。**使用递归的思想去理解遍历问题会容易理解许多。

### 复杂度分析

使用辅助栈，最坏情况下栈空间与节点数相等，其空间复杂度为 $$O(n)$$, 对每个节点遍历一次，时间复杂度为 $$O(n)$$.

### 分析

用栈或者Morris遍历。


### 栈

{% if book.java %}
```java
// Binary Tree Preorder Traversal
// 使用栈，时间复杂度O(n)，空间复杂度O(n)
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        ArrayList<Integer> result = new ArrayList<>();
        Stack<TreeNode> s = new Stack<>();
        if (root != null) s.push(root);

        while (!s.isEmpty()) {
            final TreeNode p = s.pop();
            result.add(p.val);

            if (p.right != null) s.push(p.right);
            if (p.left != null) s.push(p.left);
        }
        return result;
    }
}
```
{% endif %}


### Morris先序遍历

{% codesnippet "./code/binary-tree-preorder-traversal-2."+book.suffix, language=book.suffix %}{% endcodesnippet %}
