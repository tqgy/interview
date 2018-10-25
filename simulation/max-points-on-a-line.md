## Max Points on a Line


### 描述

Given `n` points on a 2D plane, find the maximum number of points that lie on the same straight line.


### 分析

暴力枚举法。两点决定一条直线，`n`个点两两组合，可以得到$$\dfrac{1}{2}n(n+1)$$条直线，对每一条直线，判断`n`个点是否在该直线上，从而可以得到这条直线上的点的个数，选择最大的那条直线返回。复杂度`O(n^3)`。

上面的暴力枚举法以“边”为中心，再看另一种暴力枚举法，以每个“点”为中心，然后遍历剩余点，找到所有的斜率，如果斜率相同，那么一定共线对每个点，用一个哈希表，key为斜率，value为该直线上的点数，计算出哈希表后，取最大值，并更新全局最大值，最后就是结果。时间复杂度`O(n^2)`，空间复杂度`O(n)`。


### 以边为中心

{% if book.java %}
{% codesnippet "./code/max-points-on-a-line-1."+book.suffix, language=book.suffix %}{% endcodesnippet %}
{% endif %}


### 以点为中心

{% if book.java %}
```java
// Max Points on a Line
// 暴力枚举，以点为中心，时间复杂度O(n^2)，空间复杂度O(n^2)
public class Solution {
    public int maxPoints(Point[] points) {
        if (points.length < 3) return points.length;
        int result = 0;

        HashMap<Double, Integer> slope_count = new HashMap<>();
        for (int i = 0; i < points.length-1; i++) {
            slope_count.clear();
            int samePointNum = 0; // 与i重合的点
            int point_max = 1;    // 和i共线的最大点数

            for (int j = i + 1; j < points.length; j++) {
                final double slope; // 斜率
                if (points[i].x == points[j].x) {
                    slope = Double.POSITIVE_INFINITY;
                    if (points[i].y == points[j].y) {
                        ++ samePointNum;
                        continue;
                    }
                } else {
                    if (points[i].y == points[j].y) {
                        // 0.0 and -0.0 is the same
                        slope = 0.0;
                    } else {
                        slope = 1.0 * (points[i].y - points[j].y) /
                                (points[i].x - points[j].x);
                    }
                }

                int count = 0;
                if (slope_count.containsKey(slope)) {
                    final int tmp = slope_count.get(slope);
                    slope_count.put(slope, tmp + 1);
                    count = tmp + 1;
                } else {
                    count = 2;
                    slope_count.put(slope, 2);
                }

                if (point_max < count) point_max = count;
            }
            result = Math.max(result, point_max + samePointNum);
        }
        return result;
    }
}
```
{% endif %}
