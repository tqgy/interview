## Search for a Range



### 描述

Given a sorted array of integers, find the starting and ending position of a given target value.

Your algorithm's runtime complexity must be in the order of `O(log n)`.

If the target is not found in the array, return `[-1, -1]`.

For example,
Given `[5, 7, 7, 8, 8, 10]` and target value 8,
return `[3, 4]`.


### 分析

已经排好了序，用二分查找。


### 重新实现 lower_bound 和 upper_bound

{% if book.java %}
{% codesnippet "./code/search-for-a-range."+book.suffix, language=book.suffix %}{% endcodesnippet %}
{% endif %}


### 相关题目

* [Search Insert Position](search-insert-position.md)
