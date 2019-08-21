---
layout: post
title:   "sorting"
date:   2019-08-17 08:55:01 +0800
categories: "Job Hunting"
tag: "algorithm"
---

* content
{:toc}






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
| 希尔排序 |     $O(nlogn)$     |     $O(nlogn)$     |       $O(n)$       |   $O(1)$   | 不稳定 |
| 归并排序 |     $O(nlogn)$     |     $O(nlogn)$     |     $O(nlogn)$     |   $O(n)$   |  稳定  |
| 计数排序 |      $O(n+k)$      |      $O(n+k)$      |      $O(n+k)$      |  $O(n+k)$  |  稳定  |
| 桶排序   |      $O(n+k)$      |      $O(n^2)$      |       $O(n)$       |  $O(n+k)$  |  稳定  |
| 基数排序 |                    |                    |                    |            |        |

### FAQ

#### 1.疑问：冒泡和选择算法每次调用`Swap()`的临时变量,希尔排序和堆排序每次调用函数的临时变量，空间复杂度不应该是$O(n)$吗？

答：这些都是辅助变量，每次调用后都会释放掉所占的辅助空间，所以下次调用函数的栈空间和辅助空间与这部分释放的空间就不相关了，因而空间复杂度还是$O(1)$。

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

#### 3.1算法描述

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

#### 4.1算法描述

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

- 空间复杂度：$O(logn)$,最坏情况下$O(n)$
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
        int pivotPos = partition(arr, left, right); // pivotPos变量：空间复杂度
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

#### 5.1算法描述

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



### 7. 归并排序（Merge Sort）

归并排序是另一种不同的排序方法，使用了**递归分治**的思想。

基本思想是，先递归划分子问题，然后合并结果。

#### 7.1算法描述

* `49, 38, 65, 97, 76, 13, 27` 初始
* `38, 49, 65, 97, 13, 76, 27 ` 一趟归并
* `38, 49, 65, 97, 13, 27, 76   ` 两趟归并
* `13, 27, 38, 49, 65, 97, 76 ` 两趟归并

#### 7.2算法分析

- 时间复杂度：$O(nlogn)$，一趟归并比较n次，总共logN趟。
  - 最好：$O(nlogn)$。类似选择排序，最好最坏时间复杂度不变。
  - 最坏：$O(nlogn)$。

- 空间复杂度：$O(n)$，维护了数组。
- 稳定性：稳定。合并过程中可以保证如果两个当前元素相等，处在前面的序列的元素保存在结果序列的前面。

```java
public class MergeSort {

    public static void mergeSort(int[] arr) {
        mSort(arr, 0, arr.length-1);
    }

    /**
     * 递归分治
     * @param arr 待排数组
     * @param left 左指针
     * @param right 右指针
     */
    public static void mSort(int[] arr, int left, int right) {
        if(left >= right)
            return ;
        int mid = (left + right) / 2;

        mSort(arr, left, mid); //递归排序左边
        mSort(arr, mid+1, right); //递归排序右边
        merge(arr, left, mid, right); //合并
    }

    /**
     * 合并两个有序数组
     * @param arr 待合并数组
     * @param left 左指针
     * @param mid 中间指针
     * @param right 右指针
     */
    public static void merge(int[] arr, int left, int mid, int right) {
        //[left, mid] [mid+1, right]
        int[] temp = new int[right - left + 1]; //中间数组

        int i = left;
        int j = mid + 1;
        int k = 0;
        while(i <= mid && j <= right) {
            if(arr[i] <= arr[j]) {
                temp[k++] = arr[i++];
            }
            else {
                temp[k++] = arr[j++];
            }
        }

        while(i <= mid) {
            temp[k++] = arr[i++];
        }

        while(j <= right) {
            temp[k++] = arr[j++];
        }

        for(int p=0; p<temp.length; p++) {
            arr[left + p] = temp[p];
        }

    }
}
```

### 8.计数排序

用待排序的数作为计数数组的下标，统计每个数字的个数。然后依次输出即可得到有序序列。

计数排序要求输入的数据必须是有确定范围的整数。

#### 8.1算法描述

- 找出待排序的数组中**最大**和**最小**的元素；
- 统计数组中每个值为`i`的元素出现的次数，存入数组C的第`i`项；
- 对所有的计数累加（从C中的第一个元素开始，每一项和前一项相加）；
- 反向填充目标数组：将每个元素`i`放在新数组的第`C[i]`项，每放一个元素就将`C[i]`减去1。

#### 8.2算法分析

- 时间复杂度：$O(n^2)$
  - 最好：$O(n^2)$。交换0次，但是每次都要找到最小的元素，因此大约必须遍历`N*N`次。
  - 最坏：$O(n^2)$。平均情况下。

- 空间复杂度：$O(1)$
- 稳定性：稳定。

```java
public class CountSort {

    public static void countSort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;

        int max = max(arr);

        int[] count = new int[max+1];
        Arrays.fill(count, 0);

        for(int i=0; i<arr.length; i++) {
            count[arr[i]] ++;
        }

        int k = 0;
        for(int i=0; i<=max; i++) {
            for(int j=0; j<count[i]; j++) {
                arr[k++] = i;
            }
        }

    }

    public static int max(int[] arr) {
        int max = Integer.MIN_VALUE;
        for(int ele : arr) {
            if(ele > max)
                max = ele;
        }

        return max;
    }

}
```



### 9.桶排序

桶排序算是计数排序的一种改进和推广。桶排序要比计数排序复杂许多。

[桶排序](http://hxraid.iteye.com/blog/647759)的基本思想：

* 假设有一组长度为N的待排关键字序列`K[1....n]`。首先将这个序列划分成M个的子区间(桶) 。
* 然后基于某种映射函数 ，将待排序列的关键字k映射到第i个桶中(即桶数组B的下标 i) ，那么该关键字k就作为B[i]中的元素(每个桶B[i]都是一组大小为N/M的序列)。
* 接着对每个桶B[i]中的所有元素进行比较排序(可以使用快排)。
* 然后依次枚举输出B[0]….B[M]中的全部内容即是一个有序序列。

`bindex=f(key) `  其中，bindex 为桶数组B的下标(即第bindex个桶), k为待排序列的关键字。

桶排序之所以能够高效，其关键在于这个映射函数，它必须做到：如果关键字k1<k2，那么f(k1)<=f(k2)。也就是说B(i)中的最小数据都要大于B(i-1)中最大数据。很显然，映射函数的确定与数据本身的特点有很大的关系。

#### 9.1算法描述

假如待排序列`K= {49、 38 、 35、 97 、 76、 73 、 27、 49 }`。这些数据全部在1—100之间。因此我们定制10个桶，然后确定映射函数`f(k)=k/10`。则第一个关键字49将定位到第4个桶中`(49/10=4)`。依次将所有关键字全部堆入桶中，并在每个非空的桶中进行快速排序后得到如图所示。只要顺序输出每个B[i]中的数据就可以得到有序序列了。

桶排序分析：

桶排序利用**函数的映射关系**，**减少了几乎所有的比较**工作。实际上，桶排序的**`f(k)`值的计算**，其作用就相当于**快排中划分**，**希尔排序中的子序列**，**归并排序中的子问题**，已经把大量数据分割成了基本有序的数据块(桶)。然后只需要对桶中的少量数据做先进的比较排序即可。

#### .2算法分析

- 时间复杂度：$O(n+k)$​
  - 最好：$O(n)$。
  - 最坏：$O(n^2)$。

- 空间复杂度：$O(n+k)$
- 稳定性：稳定。

对N个关键字进行桶排序的时间复杂度分为两个部分：

(1) 循环**计算每个关键字的桶映射函数**，这个时间复杂度是$O(n)$。

(2) 利用先进的排序算法对每个桶内的数据进行排序，其时间复杂度为 $ ∑ O(N_i*logN_i) $。其中$N_i$ 为第i个桶的数据量。

很显然，第(2)部分是桶排序性能好坏的决定因素。**尽量减少桶内数据的数量**是提高效率的唯一办法(因为基于比较排序的最好平均时间复杂度只能达到$O(n*logn)$了。因此，我们需要尽量做到下面两点：

(1) 映射函数f(k)能够将N个数据**平均的分配**到M个桶中，这样每个桶就有[N/M]个数据量。

(2) **尽量的增大桶的数量**。极限情况下每个桶只能得到一个数据，这样就完全避开了桶内数据的“比较”排序操作。当然，做到这一点很不容易，数据量巨大的情况下，f(k)函数会使得桶集合的数量巨大，空间浪费严重。这就是一个时间代价和空间代价的权衡问题了。

对于N个待排数据，M个桶，平均每个桶[N/M]个数据的桶排序平均时间复杂度为：

$O(N)+O(M*(N/M)*log(N/M))=O(N+N*(logN-logM))=O(N+N*logN-N*logM)$

当`N=M`时，即极限情况下每个桶只有一个数据时。桶排序的最好效率能够达到$O(N)$。

**总结：** 桶排序的平均时间复杂度为线性的`O(n+k)`，其中$k=N*(logN-logM)$。如果相对于同样的N，桶数量M越大，其效率越高，最好的时间复杂度达到$O(N)$。 

桶排序的空间复杂度为$O(N+M)$，如果输入数据非常庞大，而桶的数量也非常多，则空间代价无疑是昂贵的。

```java
public class BucketSort {

    public static void bucketSort(int[] arr) {
        if(arr == null && arr.length == 0)
            return ;

        int bucketNums = 10; //这里默认为10，规定待排数[0,100)
        List<List<Integer>> buckets = new ArrayList<List<Integer>>(); //桶的索引

        for(int i=0; i<10; i++) {
            buckets.add(new LinkedList<Integer>()); //用链表比较合适
        }

        //划分桶
        for(int i=0; i<arr.length; i++) {
            buckets.get(f(arr[i])).add(arr[i]);
        }

        //对每个桶进行排序
        for(int i=0; i<buckets.size(); i++) {
            if(!buckets.get(i).isEmpty()) {
                Collections.sort(buckets.get(i)); //对每个桶进行快排
            }
        }

        //还原排好序的数组
        int k = 0;
        for(List<Integer> bucket : buckets) {
            for(int ele : bucket) {
                arr[k++] = ele;
            }
        }
    }

    /**
     * 映射函数
     * @param x
     * @return
     */
    public static int f(int x) {
        return x / 10;
    }

}
```



### 10.基数排序

基数排序**不需要进行比较**。基数排序是一种借助多关键字排序思想对单逻辑关键字进行排序的方法。

所谓的多关键字排序就是有多个优先级不同的关键字。比如说成绩的排序，如果两个人总分相同，则语文高的排在前面，语文成绩也相同则数学高的排在前面。。。如果对数字进行排序，那么个位、十位、百位就是不同优先级的关键字，如果要进行升序排序，那么个位、十位、百位优先级一次增加。基数排序是通过多次的收分配和收集来实现的，关键字优先级低的先进行分配和收集。

#### 10.1算法描述

- 取得数组中的最大数，并取得位数；
- arr为原始数组，从最低位开始取每个位组成radix数组；
- 对radix进行计数排序（利用计数排序适用于小范围数的特点）；

#### 10.2算法分析

- 时间复杂度：$O(n^2)$
  - 最好：$O(n^2)$。交换0次，但是每次都要找到最小的元素，因此大约必须遍历`N*N`次。
  - 最坏：$O(n^2)$。平均情况下。

- 空间复杂度：$O(1)$
- 稳定性：稳定。基数排序基于分别排序，分别收集。

基数排序基于分别排序，分别收集，所以是稳定的。但基数排序的性能比桶排序要略差，每一次关键字的桶分配都需要O(n)的时间复杂度，而且分配之后得到新的关键字序列又需要O(n)的时间复杂度。假如待排数据可以分为d个关键字，则基数排序的时间复杂度将是O(d*2n) ，当然d要远远小于n，因此基本上还是线性级别的。

基数排序的空间复杂度为O(n+k)，其中k为桶的数量。一般来说n>>k，因此额外空间需要大概n个左右。

```java
public class RadixSort {

    public static void radixSort(int[] arr) {
        if(arr == null && arr.length == 0)
            return ;

        int maxBit = getMaxBit(arr);

        for(int i=1; i<=maxBit; i++) {

            List<List<Integer>> buf = distribute(arr, i); //分配
            collecte(arr, buf); //收集
        }

    }

    /**
     * 分配
     * @param arr 待分配数组
     * @param iBit 要分配第几位
     * @return
     */
    public static List<List<Integer>> distribute(int[] arr, int iBit) {
        List<List<Integer>> buf = new ArrayList<List<Integer>>();
        for(int j=0; j<10; j++) {
            buf.add(new LinkedList<Integer>());
        }
        for(int i=0; i<arr.length; i++) {
            buf.get(getNBit(arr[i], iBit)).add(arr[i]);
        }
        return buf;
    }

    /**
     * 收集
     * @param arr 把分配的数据收集到arr中
     * @param buf 
     */
    public static void collecte(int[] arr, List<List<Integer>> buf) {
        int k = 0;
        for(List<Integer> bucket : buf) {
            for(int ele : bucket) {
                arr[k++] = ele;
            }
        }

    }

    /**
     * 获取最大位数
     * @param x
     * @return
     */
    public static int getMaxBit(int[] arr) {
        int max = Integer.MIN_VALUE;
        for(int ele : arr) {
            int len = (ele+"").length();
            if(len > max)
                max = len;
        }
        return max;
    }

    /**
     * 获取x的第n位，如果没有则为0.
     * @param x
     * @param n
     * @return
     */
    public static int getNBit(int x, int n) {

        String sx = x + "";
        if(sx.length() < n)
            return 0;
        else
            return sx.charAt(sx.length()-n) - '0';
    }

}
```



## 总结

在前面的介绍和分析中我们提到了冒泡排序、选择排序、插入排序三种简单的排序及其变种快速排序、堆排序、希尔排序三种比较高效的排序。后面我们又分析了基于分治递归思想的归并排序还有计数排序、桶排序、基数排序三种线性排序。我们可以知道排序算法要么简单有效，要么是利用简单排序的特点加以改进，要么是以空间换取时间在特定情况下的高效排序。但是这些排序方法都不是固定不变的，需要结合具体的需求和场景来选择甚至组合使用。才能达到高效稳定的目的。没有最好的排序，只有最适合的排序。

\1. 从平均时间来看，快速排序是效率最高的，但快速排序在最坏情况下的时间性能不如堆排序和归并排序。而后者相比较的结果是，在n较大时归并排序使用时间较少，但使用辅助空间较多。

\2. 上面说的简单排序包括除希尔排序之外的所有冒泡排序、插入排序、简单选择排序。其中直接插入排序最简单，但序列基本有序或者n较小时，直接插入排序是好的方法，因此常将它和其他的排序方法，如快速排序、归并排序等结合在一起使用。

\3. 基数排序的时间复杂度也可以写成O(d*n)。因此它最使用于n值很大而关键字较小的的序列。若关键字也很大，而序列中大多数记录的最高关键字均不同，则亦可先按最高关键字不同，将序列分成若干小的子序列，而后进行直接插入排序。

\4. 从方法的稳定性来比较，基数排序是稳定的内排方法，所有时间复杂度为O(n^2)的简单排序也是稳定的。但是快速排序、堆排序、希尔排序等时间性能较好的排序方法都是不稳定的。稳定性需要根据具体需求选择。

\5. 上面的算法实现大多数是使用线性存储结构，像插入排序这种算法用链表实现更好，省去了移动元素的时间。具体的存储结构在具体的实现版本中也是不同的。

附：基于比较排序算法时间下限为O(nlogn)的证明：

基于比较排序下限的证明是通过决策树证明的，决策树的高度Ω（nlgn），这样就得出了比较排序的下限。

首先要引入决策树。 首先决策树是一颗二叉树，每个节点表示元素之间一组可能的排序，它予以京进行的比较相一致，比较的结果是树的边。 先来说明一些二叉树的性质，令T是深度为d的二叉树，则T最多有2^片树叶。 具有L片树叶的二叉树的深度至少是logL。 所以，对n个元素排序的决策树必然有n!片树叶（因为n个数有n!种不同的大小关系），所以决策树的深度至少是log(n!)，即至少需要log(n!)次比较。 而 log(n!)=logn+log(n-1)+log(n-2)+…+log2+log1 >=logn+log(n-1)+log(n-2)+…+log(n/2) >=(n/2)log(n/2) >=(n/2)logn-n/2 =O(nlogn) 所以只用到比较的排序算法最低时间复杂度是O(nlogn)。

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

