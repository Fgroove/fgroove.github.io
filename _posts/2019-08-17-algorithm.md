---
layout: post
title:   "sorting"
date:   2019-08-17 08:55:01 +0800
categories: "Job Hunting"
tag: "algorithm"


---

[TOC]





# Algorithm

## [排序](https://www.cnblogs.com/onepixel/p/7674659.html)

### 常见算法复杂度

| 排序     | 时间复杂度（平均） | 时间复杂度（最坏） | 时间复杂度（最好） | 空间复杂度 | 稳定性 |
| -------- | :----------------: | :----------------: | :----------------: | :--------: | :----: |
| 冒泡排序 |      $O(n^2)$      |      $O(n^2)$      |       $O(n)$       |   $O(1)$   |  稳定  |
| 选择排序 |      $O(n^2)$      |      $O(n^2)$      |      $O(n^2)$      |   $O(1)$   | 不稳定 |
| 插入排序 |      $O(n^2)$      |      $O(n^2)$      |       $O(n)$       |   $O(1)$   |  稳定  |
| 快速排序 |     $O(nlogn)$     |      $O(n^2)$      |     $O(nlogn)$     | $O(logn)$  | 不稳定 |
| 堆排序   |     $O(nlogn)$     |     $O(nlogn)$     |     $O(nlogn)$     |   $O(1)$   | 不稳定 |
|          |                    |                    |                    |            |        |
|          |                    |                    |                    |            |        |
|          |                    |                    |                    |            |        |
|          |                    |                    |                    |            |        |
|          |                    |                    |                    |            |        |

### 1.冒泡排序（Bubble Sort)

通过与**相邻元素的比较**和交换来把小的数交换到最前面。

#### 1.1 算法描述

- `5,3,8,6,4`,从后向前冒泡
- `3|5,4,8,6`,一次冒泡完成，把最小的数3排到最前面。
- `3,4|5,6,8`

#### 1.2 算法分析

* 时间复杂度：$O(n^2)$
  * 最好：$O(n)$。正序有序，则只需要比较n次。
  * 最坏：$O(n^2)$。逆序有序，则需要比较(n-1)+(n-2)+……+1。

* 空间复杂度：$O(1)$
* 稳定性：稳定。相邻元素的比较和交换，相等的话不会进行交换。

```java
public class BubbleSort {

    public static void bubbleSort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;
        for(int i=0; i<arr.length-1; i++) {
            for(int j=arr.length-1; j>i; j--) {
                if(arr[j] < arr[j-1]) {
                    swap(arr, j-1, j);
                }
            }
        }
    }

    public static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```

#### [冒泡排序最佳情况的时间复杂度，为什么是O(n)？](https://www.cnblogs.com/melon-h/archive/2012/09/20/2694941.html)

上面的代码的时间复杂度确实仍是$O(n^2)$，但算法可以改进，使最佳情况时为$O(n)$。

设置标志位，没有交换发生时，停止排序：

```java
public void bubbleSort(int arr[]) {
    boolean didSwap;
    for(int i = 0, len = arr.length; i < len - 1; i++) {
        didSwap = false;
        for(int j = 0; j < len - i - 1; j++) {
            if(arr[j + 1] < arr[j]) {
                swap(arr, j, j + 1);
                didSwap = true;
            }
        }
        if(didSwap == false)
            return;
    }    
}
```



### 2.选择排序（selection sort)

首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。 

其实选择排序可以看成**冒泡排序的优化**，因为其目的相同，只是选择排序只有在确定了最小数的前提下才进行交换，大大减少了交换的次数。

#### 2.1 算法描述

n个记录的直接选择排序可经过n-1趟直接选择排序得到有序结果。

- `5,3,8,6,4`
- `3|5,8,6,4`
- `3,4|8,6,5`
- ……

#### 2.2 算法分析

- 时间复杂度：$O(n^2)$
  - 最好：$O(n^2)$。交换0次，但是每次都要找到最小的元素，因此大约必须遍历`N*N`次。
  - 最坏：$O(n^2)$。平均情况下。

- 空间复杂度：$O(1)$
- 稳定性：不稳定。`5 8 5 2 9`,第一趟选择第1个元素5会与2进行交换，那么原序列中两个5的相对先后顺序也就被破坏了。

```java
public class SelectSort {

    public static void selectSort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;
        int minIndex = 0;
        for(int i=0; i<arr.length-1; i++) { //只需要比较n-1次
            minIndex = i;
            for(int j=i+1; j<arr.length; j++) { //从i+1开始比较，因为minIndex默认为i了，i就没必要比了。
                if(arr[j] < arr[minIndex]) {
                    minIndex = j;
                }
            }

            if(minIndex != i) { //如果minIndex不为i，说明找到了更小的值，交换之。
                swap(arr, i, minIndex);
            }
        }

    }

    public static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

}
```

### 3.插入排序（Insertion Sort）

插入排序不是通过**交换位置**而是通过比较**找到合适的位置插入元素**来达到排序的目的的。类似打扑克牌抓牌过程。

注意在插入一个数的时候要保证这个数前面的数已经有序。

#### 3.1 算法描述

简单插入排序

* `5,3,8,6,4`
* `3,5,8,6,4`
* `3,5,6,8,4`
* `3,4,5,6,8`

#### 3.2算法分析

- 时间复杂度：$O(n^2)$
  - 最好：$O(n)$。正序有序(从小到大)，这样只需要比较n次，不需要移动。
  - 最坏：$O(n^2)$。逆序有序,这样每一个元素就需要比较n次，共有n个元素。

- 空间复杂度：$O(1)$
- 稳定性：稳定。如果遇见一个与插入元素相等的，那么把待插入的元素放在相等元素的后面。

```java
public class InsertSort {

    public static void insertSort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;

        for(int i=1; i<arr.length; i++) { //假设第一个数位置时正确的；要往后移，必须要假设第一个。

            int j = i;
            int target = arr[i]; //待插入的

            //后移
            while(j > 0 && target < arr[j-1]) {
                arr[j] = arr[j-1];
                j --;
            }

            //插入 
            arr[j] = target;
        }

    }

}
```



### 4.快速排序（Quick Sort）

快速排序虽然高端，但其实其思想是来自冒泡排序，冒泡排序是通过相邻元素的比较和交换把最小的冒泡到最顶端，而快速排序是**比较和交换小数和大数**，这样一来**不仅把小数冒泡到上面同时也把大数沉到下面**。

思想：冒泡+二分+递归分治

#### 4.1 算法描述

基准选左边，右指针先行；反之左指针。

因为在两个指针相遇时，要交换基准数到相遇的位置。一般选取左边第一个数作为基准数，所以最后相遇的数要和基准数交换，那么相遇的数一定要比基准数小。

* `5,3,8,6,4`,右指针找比基准数小的，左指针找比基准数大
* `4,3,5,6,8`
* `3,4,5,6,8`

#### 4.2算法分析

- 时间复杂度：$O(nlogn)$

  - 最好：$O(nlogn)$。因为每次都将序列分为两个部分(一般二分都复杂度都和logN相关)。

  - 最坏：$O(n^2)$。序列基本有序，选取的枢轴元素为最大值或最小值时，退化为冒泡排序，几乎要比较`N*N/2`次。

    改进快速排序算法，随机选取界点或最左、最右、中间三个元素中的值处于中间的作为界点，通常可以避免原始序列有序的最坏情况。

- 空间复杂度：$O(logn)$
- 稳定性：不稳定。每次都需要和基准数交换。

```java
public class QuickSort {
    //一次划分
    public static int partition(int[] arr, int left, int right) {
        int pivotKey = arr[left];
        int pivotPointer = left;

        while(left < right) {
            while(left < right && arr[right] >= pivotKey)
                right --;
            while(left < right && arr[left] <= pivotKey)
                left ++;
            swap(arr, left, right); //把大的交换到右边，把小的交换到左边。
        }
        swap(arr, pivotPointer, left); //最后把pivot交换到中间
        return left;
    }

    public static void quickSort(int[] arr, int left, int right) {
        if(left >= right)
            return ;
        int pivotPos = partition(arr, left, right);
        quickSort(arr, left, pivotPos-1);
        quickSort(arr, pivotPos+1, right);
    }

    public static void sort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;
        quickSort(arr, 0, arr.length-1);
    }

    public static void swap(int[] arr, int left, int right) {
        int temp = arr[left];
        arr[left] = arr[right];
        arr[right] = temp;
    }

}
```

**优化：**上面代码中基准数已经在pivotKey中保存了，所以不需要每次交换都设置一个temp变量，在交换左右指针的时候只需要**先后覆盖**就可以了。这样既能减少空间的使用还能降低赋值运算的次数。

优化后和优化前的中间过程有变化，因为不再是**交换**，是**先后覆盖**。

```java
public class QuickSort {

    public static int partition(int[] arr, int left, int right) {
        int pivotKey = arr[left];

        while(left < right) {
            while(left < right && arr[right] >= pivotKey)
                right --;
            arr[left] = arr[right]; //把小的移动到左边
            while(left < right && arr[left] <= pivotKey)
                left ++;
            arr[right] = arr[left]; //把大的移动到右边
        }
        arr[left] = pivotKey; //最后把pivot赋值到中间
        return left;
    }

    public static void quickSort(int[] arr, int left, int right) {
        if(left >= right)
            return ;
        int pivotPos = partition(arr, left, right);
        quickSort(arr, left, pivotPos-1);
        quickSort(arr, pivotPos+1, right);
    }

    public static void sort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;
        quickSort(arr, 0, arr.length-1);
    }

}
```



### 5.堆排序（Heap Sort）

堆排序是借助堆来实现的选择排序，思想同简单的选择排序，以下以大顶堆为例。

堆的性质：

* 节点`i`的孩子为`2*i+1`和`2*i+2`节点，
* 大顶堆要求父节点大于等于其2个子节点，小顶堆要求父节点小于等于其2个子节点。

**注意：**如果想升序排序就使用大顶堆，反之使用小顶堆。原因是堆顶元素需要交换到序列尾部。

#### 5.1 算法描述

1. 如何由一个无序序列键成一个堆？

   答：直接使用**线性数组**来表示一个堆，需要自底向上从第一个非叶元素开始挨个调整成一个堆。

2.  如何在输出堆顶元素之后，调整剩余元素成为一个新的堆？

* 首先是将堆顶元素和最后一个元素交换。
* 然后比较当前堆顶元素的左右孩子节点，（因为除了当前的堆顶元素，左右孩子堆均满足条件，）这时需要选择当前堆顶元素与左右孩子节点的**较大者（大顶堆）**交换，直至叶子节点。我们称这个自堆顶到叶子的调整为**筛选**。

#### 5.2算法分析

- 时间复杂度：$O(nlogn)$
  - 最好：$O(nlogn)$
  - 最坏：$O(nlogn)$

- 空间复杂度：$O(1)$
- 稳定性：不稳定。`5 8 5 2 9`,第一趟选择第1个元素5会与2进行交换，那么原序列中两个5的相对先后顺序也就被破坏了。

```java
public class HeapSort {

    /**
     * 堆筛选，除了start之外，start~end均满足大顶堆的定义。
     * 调整之后start~end称为一个大顶堆。
     * @param arr 待调整数组
     * @param start 起始指针
     * @param end 结束指针
     */
    public static void heapAdjust(int[] arr, int start, int end) {
        int temp = arr[start];

        for(int i=2*start+1; i<=end; i*=2) {
            //左右孩子的节点分别为2*i+1,2*i+2

            //选择出左右孩子较大的下标
            if(i < end && arr[i] < arr[i+1]) {
                i ++; 
            }
            if(temp >= arr[i]) {
                break; //已经为大顶堆，=保持稳定性。
            }
            arr[start] = arr[i]; //将子节点上移
            start = i; //下一轮筛选
        }

        arr[start] = temp; //插入正确的位置
    }

    public static void heapSort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;

        //建立大顶堆
        for(int i=arr.length/2; i>=0; i--) {
            heapAdjust(arr, i, arr.length-1);
        }

        for(int i=arr.length-1; i>=0; i--) {
            swap(arr, 0, i);
            heapAdjust(arr, 0, i-1);
        }

    }

    public static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

}
```



### 6.希尔排序

希尔排序是插入排序的一种高效率的实现，也叫缩小增量排序。

简单的插入排序中，如果待排序列是正序时，时间复杂度是O(n)，如果序列是基本有序的，使用直接插入排序效率就非常高。希尔排序就利用了这个特点。

基本思想是：先将整个待排记录序列分割成为若干子序列分别进行直接插入排序，待整个序列中的记录基本有序时再对全体记录进行一次直接插入排序。

#### 6.1 算法描述

#### 6.2算法分析

- 时间复杂度：$O(nlogn)$
  - 最好：$O(n)$,序列是正序排列，在这种情况下，需要进行的比较操作`（n-1）`次。后移赋值操作为0次。
  - 最坏：$O(nlogn)$，最坏的情况下和平均情况下差不多。

- 空间复杂度：$O(1)$
- 稳定性：不稳定。**跳跃式地往前移**。

```java
public class ShellSort {

    /**
     * 希尔排序的一趟插入
     * @param arr 待排数组
     * @param d 增量
     */
    public static void shellInsert(int[] arr, int d) {
        for(int i=d; i<arr.length; i++) {
            int j = i - d;
            int temp = arr[i];    //记录要插入的数据  
            while (j>=0 && arr[j]>temp) {  //从后向前，找到比其小的数的位置   
                arr[j+d] = arr[j];    //向后挪动  
                j -= d;  
            }  

            if (j != i - d)    //存在比其小的数 
                arr[j+d] = temp;

        }
    }

    public static void shellSort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;
        int d = arr.length / 2;
        while(d >= 1) {
            shellInsert(arr, d);
            d /= 2;
        }
    }

}
```



### 排序

#### .1 算法描述

#### .2算法分析

- 时间复杂度：$O(n^2)$
  - 最好：$O(n^2)$。交换0次，但是每次都要找到最小的元素，因此大约必须遍历`N*N`次。
  - 最坏：$O(n^2)$。平均情况下。

- 空间复杂度：$O(1)$
- 稳定性：不稳定。`5 8 5 2 9`,第一趟选择第1个元素5会与2进行交换，那么原序列中两个5的相对先后顺序也就被破坏了。



### 排序

#### .1 算法描述

#### .2算法分析

- 时间复杂度：$O(n^2)$
  - 最好：$O(n^2)$。交换0次，但是每次都要找到最小的元素，因此大约必须遍历`N*N`次。
  - 最坏：$O(n^2)$。平均情况下。

- 空间复杂度：$O(1)$
- 稳定性：不稳定。`5 8 5 2 9`,第一趟选择第1个元素5会与2进行交换，那么原序列中两个5的相对先后顺序也就被破坏了。



### 排序

#### .1 算法描述

#### .2算法分析

- 时间复杂度：$O(n^2)$
  - 最好：$O(n^2)$。交换0次，但是每次都要找到最小的元素，因此大约必须遍历`N*N`次。
  - 最坏：$O(n^2)$。平均情况下。

- 空间复杂度：$O(1)$
- 稳定性：不稳定。`5 8 5 2 9`,第一趟选择第1个元素5会与2进行交换，那么原序列中两个5的相对先后顺序也就被破坏了。



### 排序

#### .1 算法描述

#### .2算法分析

- 时间复杂度：$O(n^2)$
  - 最好：$O(n^2)$。交换0次，但是每次都要找到最小的元素，因此大约必须遍历`N*N`次。
  - 最坏：$O(n^2)$。平均情况下。

- 空间复杂度：$O(1)$
- 稳定性：不稳定。`5 8 5 2 9`,第一趟选择第1个元素5会与2进行交换，那么原序列中两个5的相对先后顺序也就被破坏了。



### 参考

1. [面试中的 10 大排序算法总结](http://www.codeceo.com/article/10-sort-algorithm-interview.html)
2. [十大经典排序算法（动图演示）](https://www.cnblogs.com/onepixel/p/7674659.html)
3. [常用排序算法稳定性、时间复杂度分析（转，有改动）](https://www.cnblogs.com/nannanITeye/archive/2013/04/11/3013737.html)
4. [排序算法总结](https://michaelyou.github.io/2015/03/01/排序算法总结/)

## 动态规划

## 贪心算法

## 遍历

## 树

## 哈希表

