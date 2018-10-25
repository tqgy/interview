# Heap - 堆

一般情况下，堆通常指的是**二叉堆**，**二叉堆**是一个近似**完全二叉树**的数据结构，但由于对二叉树平衡及插入/删除操作较为麻烦，二叉堆实际上使用数组来实现。即物理结构为数组，逻辑结构为完全二叉树。子结点的键值或索引总是小于（或者大于）它的父节点，且每个节点的左右子树又是一个**二叉堆**(大根堆或者小根堆)。根节点最大的堆叫做最大堆或大根堆，根节点最小的堆叫做最小堆或小根堆。**常被用作实现优先队列。**

## 特点

1. **以数组表示，但是以完全二叉树的方式理解**。
2. 唯一能够同时最优地利用空间和时间的方法——最坏情况下也能保证使用 $$2N \log N$$ 次比较和恒定的额外空间。
3. 在索引从0开始的数组中：
  - 父节点 `i` 的左子节点在位置`(2*i+1)`
  - 父节点 `i` 的右子节点在位置`(2*i+2)`
  - 子节点 `i` 的父节点在位置`floor((i-1)/2)`

## 堆的基本操作

以大根堆为例，堆的常用操作如下。

1. 最大堆调整：将堆的末端子节点作调整，使得子节点永远小于父节点
2. 创建最大堆：将堆所有数据重新排序
3. 堆排序：移除位于第一个数据的根节点，并做最大堆调整的递归运算

其中步骤1是给步骤2和3用的。

![Heapsort-example](../../shared-files/images/Heapsort-example.gif)

## 堆实现

使用迭代实现也较为简单，注意索引值初始化和置位即可，尤其是记住最后一个索引的值 last，Java 的实现中 `poll()` 操作同时带有排序功能。

### Java

```java
import java.util.*;

/**
 * Created by billryan on 29/7/2018.
 */
public class MaxHeap {
    private final int MAX_N = 10;
    private final int[] heap = new int[MAX_N];
    private int last = 0;

    public int getLast() {
        return last;
    }

    public void push(int x) {
        int i = last++;
        while (i > 0) {
            int p = (i - 1) / 2;
            if (heap[p] >= x) {
                break;
            }
            heap[i] = heap[p];
            i = p;
        }
        heap[i] = x;
    }

    public int pop() {
        int result = heap[0];
        int x = heap[--last];
        heap[last] = result;

        int i = 0;
        while (2 * i + 1 < last) {
            int left = 2 * i + 1, right = 2 * i + 2, swap = left;
            if (right < last && heap[left] < heap[right]) {
                swap = right;
            }
            if (heap[swap] <= x) {
                break;
            }

            heap[i] = heap[swap];
            i = swap;
        }
        heap[i] = x;

        return result;
    }

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append("maxHeap: [");
        for (int i = 0; i < last - 1; i++) {
            sb.append(String.format("%d, ", heap[i]));
        }
        if (last > 0) {
            sb.append(heap[last - 1]);
        }
        sb.append("]");
        return sb.toString();
    }

    public static void main(String[] args) {
        MaxHeap maxHeap = new MaxHeap();

        int[] array = new int[]{6, 5, 3, 1, 8, 7, 2, 4, 10, 9};
        for (int i : array) {
            maxHeap.push(i);
            System.out.println(maxHeap);
        }

        for (int i = maxHeap.getLast() - 1; i >= 0; i--) {
            System.out.println("pop max heap value: " + maxHeap.pop());
            System.out.println(maxHeap);
        }

        PriorityQueue<Integer> pq = new PriorityQueue<Integer>(10, Collections.reverseOrder());
        for (int i : array) {
            pq.offer(i);
            System.out.println(pq);
        }

        // Top K problem
        int k = 5;
        for (int i = 0; i < k; i++) {
            Integer topk = pq.poll();
            if (topk != null) {
                System.out.println("top " + (i + 1) + ": " + topk);
            } else {
                System.out.println("poll null value!!!");
            }
        }
    }
}
```
