# Remove Duplicates from Sorted List

Tags: Linked List, Easy

## Question

- leetcode: [Remove Duplicates from Sorted List](https://leetcode.com/problems/remove-duplicates-from-sorted-list/)
- lintcode: [Remove Duplicates from Sorted List](http://www.lintcode.com/en/problem/remove-duplicates-from-sorted-list/)

### Problem Statement

Given a sorted linked list, delete all duplicates such that each element
appear only _once_.

For example,  
Given `1->1->2`, return `1->2`.  
Given `1->1->2->3->3`, return `1->2->3`.

## 题解

遍历之，遇到当前节点和下一节点的值相同时，删除下一节点，并将当前节点`next`值指向下一个节点的`next`, 当前节点首先保持不变，直到相邻节点的值不等时才移动到下一节点。

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
     * @return: ListNode head of linked list
     */
    public static ListNode deleteDuplicates(ListNode head) {
        ListNode curr = head;
        while (curr != null) {
            while (curr.next != null && curr.val == curr.next.val) {
                curr.next = curr.next.next;
            }
            curr = curr.next;
        }

        return head;
    }
}
```

### 源码分析

1. 首先进行异常处理，判断head是否为NULL
2. 遍历链表，`curr->val == curr->next->val`时，保存`curr->next`，便于后面释放内存(非C/C++无需手动管理内存)
3. 不相等时移动当前节点至下一节点，注意这个步骤必须包含在`else`中，否则逻辑较为复杂

~~`while` 循环处也可使用`curr != null && curr.next != null`, 这样就不用单独判断`head` 是否为空了，但是这样会降低遍历的效率，因为需要判断两处。~~使用双重`while`循环可只在内循环处判断，避免了冗余的判断，谢谢 @xuewei4d 提供的思路。

### 复杂度分析

遍历链表一次，时间复杂度为 $$O(n)$$, 使用了一个中间变量进行遍历，空间复杂度为 $$O(1)$$.

### 递归版

{% codesnippet "./code/remove-duplicates-from-sorted-list-1."+book.suffix, language=book.suffix %}{% endcodesnippet %}


### 迭代版

{% codesnippet "./code/remove-duplicates-from-sorted-list-2."+book.suffix, language=book.suffix %}{% endcodesnippet %}


## Reference

- [Remove Duplicates from Sorted List 参考程序 | 九章](http://www.jiuzhang.com/solutions/remove-duplicates-from-sorted-list/)
