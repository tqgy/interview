## Trapping Rain Water


### 描述

Given `n` non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

For example, 
Given `[0,1,0,2,1,0,1,3,2,1,2,1]`, return 6.

![Trapping Rain Water](../../images/trapping-rain-water.png)


### 分析

对于每个柱子，找到其左右两边最高的柱子，该柱子能容纳的面积就是`min(max_left, max_right) - height`。所以，

1. 从左往右扫描一遍，对于每个柱子，求取左边最大值；
1. 从右往左扫描一遍，对于每个柱子，求最大右值；
1. 再扫描一遍，把每个柱子的面积并累加。

也可以，

1. 扫描一遍，找到最高的柱子，这个柱子将数组分为两半；
1. 处理左边一半；
1. 处理右边一半。


### 代码1

{% if book.java %}
{% codesnippet "./code/trapping-rain-water-1."+book.suffix, language=book.suffix %}{% endcodesnippet %}
{% endif %}


### 代码2

{% if book.java %}
{% codesnippet "./code/trapping-rain-water-2."+book.suffix, language=book.suffix %}{% endcodesnippet %}
{% endif %}


### 相关题目

* [Container With Most Water](../greedy/container-with-most-water.md)
* [Largest Rectangle in Histogram](../stack-and-queue/stack/largest-rectangle-in-histogram.md)
