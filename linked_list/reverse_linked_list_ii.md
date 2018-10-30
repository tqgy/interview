# Reverse Linked List II

## Question

- leetcode: [Reverse Linked List II | LeetCode OJ](https://leetcode.com/problems/reverse-linked-list-ii/)
- lintcode: [(36) Reverse Linked List II](http://www.lintcode.com/en/problem/reverse-linked-list-ii/)

### Problem Statement

Reverse a linked list from position m to n.

#### Example

Given **1-&gt;2-&gt;3-&gt;4-&gt;5-&gt;NULL**, m = 2 and n = 4, return
1-&gt;4-&gt;3-&gt;2-&gt;5-&gt;NULL.

#### Note

Given m, n satisfy the following condition: 1 ≤ m ≤ n ≤ length of list.

#### Challenge

Reverse it in-place and in one-pass

## 题解

此题在上题的基础上加了位置要求，只翻转指定区域的链表。由于链表头节点不确定，祭出我们的dummy杀器。此题边界条件处理特别tricky，需要特别注意。

1. 由于只翻转指定区域，分析受影响的区域为第m-1个和第n+1个节点
2. 找到第m个节点，使用for循环n-m次，使用上题中的链表翻转方法
3. 处理第m-1个和第n+1个节点
4. 返回dummy->next

### Java

```java
/**
 * Definition for ListNode
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    /**
     * @param ListNode head is the head of the linked list 
     * @oaram m and n
     * @return: The head of the reversed ListNode
     */
    public ListNode reverseBetween(ListNode head, int m , int n) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        
        // find the mth node
        ListNode premNode = dummy;
        for (int i = 1; i < m; i++) {
            premNode = premNode.next;
        }
        
        // reverse node between m and n
        ListNode prev = null, curr = premNode.next;
        while (curr != null && (m <= n)) {
            ListNode nextNode = curr.next;
            curr.next = prev;
            prev = curr;
            curr = nextNode;
            m++;
        }
        
        // join head and tail before m and after n
        premNode.next.next = curr;
        premNode.next = prev;
        
        return dummy.next;
    }
}
```

### 源码分析

1. 处理异常
2. 使用dummy辅助节点
3. 找到premNode——m节点之前的一个节点
4. 以nNode和postnNode进行遍历翻转，注意考虑在遍历到n之前postnNode可能为空
5. 连接premNode和nNode，`premNode->next = nNode;`
6. 连接mNode和postnNode，`mNode->next = postnNode;`

**务必注意node 和node->next的区别！！**，node指代节点，而`node->next`指代节点的下一连接。


### 分析

这题非常繁琐，有很多边界检查，15分钟内做到bug free很有难度！


### 代码

{% codesnippet "./code/reverse-linked-list-ii."+book.suffix, language=book.suffix %}{% endcodesnippet %}
