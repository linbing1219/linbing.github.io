---
layout:     post
title:      数据结构及算法-排序算法
subtitle:    "\"sort algorithms\""
date:       2020-04-01
author:     Jimmy
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Algorithms
---


#### 1、快速排序

##### 1.1 代码

```
package com.huawei.mrs.sort;

import java.util.Arrays;

/**
 * 快排
 * 1、选取基准pivot
 * 2、将所有比基准下的元素放在基准的左边，比基准大的元素放在基准右边
 * 3、递归
 */
public class ForwardQuickSort implements IArraySort {
    @Override
    public int[] sort(int[] sourceArray) throws Exception {
        int[] array = Arrays.copyOf(sourceArray, sourceArray.length);
        quickSort(array, 0, array.length - 1);
        return array;
    }

    private int[] quickSort(int[] array, int left, int right) {
        if (left < right) {
            int partitionIndex = partition(array, left, right);
            quickSort(array, left, partitionIndex - 1);
            quickSort(array, partitionIndex + 1, right);
        }
        return array;
    }

    private int partition(int[] array, int left, int right) {
        int pivot = left;
        int index = pivot + 1;
        for (int i=index; i<=right; i++) {
            if (array[i] < array[pivot]) {
                swap(array, i, index);
                index ++;
            }
        }
        swap(array, index-1, pivot);
        return index - 1;
    }

    private void swap(int[] array, int i, int j) {
        int temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }

    public static void main(String[] args) {
        int[] testData = {3, 44, 38, 5, 47, 15, 36, 26, 27, 2, 46, 4, 19, 50, 48};
        try {
            Arrays.stream(new ForwardQuickSort().sort(testData)).forEach(it -> System.out.println(it));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```

*优化点在于，怎么选择基准上，防止坏情况的出现*

##### 1.2 算法改进

- （1）三分取中法：为了解决从首位或者末尾获取到的pivot过于偏激，导致对算法的影响。三分取中法就是取待排序数组的首位数、中位数和末尾数优先进行简单的排序，然后将排序后的值分别按照大小顺序插入原数组的首部，中部和尾部，并取三数中的中值作为基准。

-（2）三向切分快排：解决大量重复元素的排序复杂度未降低的问题，主要做法是记录小于、等于和大于基准值的位置。（荷兰国旗问题）

-（3）双轴快排：两个基准值，分三段进行递归。
JDK1.8：首先判断数组的长度是否大于286，大于的话就用归并排序；再判断数组的长度是否小于47，小于的话使用插入排序；否则双轴快排。

#### 2、插入排序

算法复杂度：时间复杂度O(n2)，空间复杂度O(1)

基本排序算法中，一般情况下我们直接选择快速排序算法。但在快排分区规模比较小时，我们会改用插入排序算法去排序那个分区的数据。

经验是，小数据量/接近有序的数据进行排序，使用插入排序。

算法改进：数组已经有序部分，查找插入元素位置时，使用二分查找。

#### 3、归并算法

##### 3.1 代码

```
package com.huawei.mrs.sort;

import java.util.Arrays;

/**
 * 归并算法：
 * 1、用到一个临时数组，空间复制度O(n)。时间复制度O(nlogn)
 */
public class MergeSort implements IArraySort {
    @Override
    public int[] sort(int[] sourceArray) throws Exception {
        int[] array = Arrays.copyOf(sourceArray, sourceArray.length);
        if (array.length < 2) {
            return array;
        }
        int middle = (int) Math.floor(array.length / 2);
        int[] left = Arrays.copyOfRange(array, 0, middle);
        int[] right = Arrays.copyOfRange(array, middle, array.length);

        return merge(sort(left), sort(right));
    }

    private int[] merge(int[] left, int[] right) {
        int[] res = new int[left.length + right.length];
        int i = 0;
        while (left.length > 0 && right.length > 0) {
            if (left[0] <= right[0]) {
                res[i++] = left[0];
                left = Arrays.copyOfRange(left, 1, left.length);
            } else {
                res[i++] = right[0];
                right = Arrays.copyOfRange(right, 1, right.length);
            }
        }
        while (left.length > 0) {
            res[i++] = left[0];
            left = Arrays.copyOfRange(left, 1, left.length);
        }
        while (right.length > 0) {
            res[i++] = right[0];
            right = Arrays.copyOfRange(right, 1, right.length);
        }

        return res;
    }

    public static void main(String[] args) {
        int[] testData = {3, 44, 38, 5, 47, 15, 36, 26, 27, 2, 46, 4, 19, 50, 48};
        try {
            Arrays.stream(new ForwardQuickSort().sort(testData)).forEach(it -> System.out.println(it));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

#### 4、堆排序

堆结构：近似完全二叉树，满足性质：子节点的键值或者索引总是小于/大于其父节点。

大顶堆：每个节点的值都大于其子节点的值，堆排序中用于升序排序；
小顶堆：每个节点的值都小于其子节点的值，降序排序。

堆排序比较和交换的次数比快速排序多，所以平均而言比快排慢。
但是针对top K场景，堆排序的优势就出来了。


优先队列（JDK中的PriorityQueue）也是堆排序的另一个使用场景，其需要在一组不停更新的数据中找到最大/最小元素。

#### 5、外排算法

算法引入   http://data.biancheng.net/out_sort/
当待排序的文件大小比内存的可用容量还大时，文件无法一次性放到内存中进行排序，需要借助于外部存储器。

外部排序算法有两个阶段组成：
1、按照内存大小，将大文件分成若干个长度为l的子文件（l小于内存可用容量），然后将各个子文件依次读入内存，使用适当的内排算法对其进行排序（排好序的子文件统称为归并段或者顺段），将排好序的归并段写入外存，为下一个子文件排序腾出内存空间。
2、对得到的归并段进行合并，直至得到整个有序的文件为止。


注意：在实际归并的过程中，由于内存容量的限制不能满足同时将 2 个归并段全部完整的读入内存进行归并，只能不断地取 2 个归并段中的每一小部分进行归并，通过不断地读数据和向外存写数据，直至 2 个归并段完成归并变为 1 个大的有序文件。

对于外部排序算法而言，影响整体排序效率的因素主要取决于读取外存的次数，即访问外存的次数越多，算法花费时间就越多，效率就越低。那么，对于同一个文件来说，对其进行外部排序访问外存的次数与归并次数成正比，即归并的次数越多，访问外存的次数就越多。

一般情况下，对于具有m个初始归并段进行k-路平衡归并时，归并的次数为s=⌊logk m⌋（其中 s 表示归并次数）。
所以想要达到减少归并次数从而提高算法效率的目的，可以从两个角度实现：
1、增加k-路平衡归并中的k值
2、尽量减少初始归并段的数量m，即增加每个归并段的容量。

多路平衡归并算法

k并不是越大越好，因为k越大，虽然会减少读写外存数据的次数，但会增加内部归并时间。

为了避免在增加k值的过程中影响内部归并的效率，在进行k-路归并时可以使用“败者树”来实现，该方法在增加k值时不会影响其内部归并的效率。

胜者树和败者树的区别就是：胜者树中的非终端结点中存储的是胜利的一方；而败者树中的非终端结点存储的是失败的一方。而在比较过程中，都是拿胜者去比较。


置换选择排序算法

目的是减小m的值。



最佳归并树

无论是等分还是置换选择排序得到的归并段，如何设置他们的归并顺序，可以得到对外存的访问次数降到最低？

构造霍夫曼树（带权路径长度最短的二叉树）
