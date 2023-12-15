---
title: Sorting Algorithms
url: sorting-algorithms
date: 2018-05-25 20:00:00
description: 排序算法
categories: Computer Science
tags: [Algorithm,Interview]
---

## Bubble Sort
冒泡排序也许是大部分人CS生涯里学到的第一种排序算法，它的基本思想是：依次比较两个相邻记录的关键字，如果逆序就进行交换，直到没有逆序的记录。

每一趟排序可以将前$i$个元素的最大值冒泡到最后，因此共需$n-1$趟；每一趟都要比较$j$和$j+1$的值，因此$j$取值为$[0,i-1]$。

```cpp
// 迭代
void bubbleSort(vector<int>& nums) {
    for (int i = nums.size() - 1; i > 0; --i) {
        for (int j = 0; j < i; ++j) {
            if (nums[j] > nums[j + 1]) {
                swap(nums[j], nums[j + 1]);
            }
        }
    }
}

// 递归
void bubble(vector<int>& nums, int l, int r) {
    if (l >= r)
        return;
    for (int k = l; k < r; ++k) {
        if (nums[k] > nums[k + 1])
            swap(nums[k], nums[k + 1]);
    }
    bubble(nums, l, r - 1);
}
```

冒泡排序也是可以稍稍优化的：试想如果序列是$[2,1,3,4,5]$，其实我们只需交换前两个元素，即第一趟有交换，走完第二趟发现没有交换时就可以结束了。

```cpp
void bubble_sort(int nums[], int n) {
	bool has_swap = true;
	for (int i = n - 1; i > 0 && has_swap; --i) {
		has_swap = false;
		for (int j = 0; j < i; ++j) {
			if (nums[j] > nums[j + 1]) {
                swap(nums[j], nums[j + 1]);
				has_swap = true;
            }	
		}
	}
}
```

对于最后优化的代码，可以看到：  
最好的情况就是待排序列已经全部有序，这样要进行$n-1$次比较，时间复杂度O(n)；  
最坏的情况就是待排序列全部逆序，需要进行n(n-1)/2次比较，并且还有等数量级的交换，时间复杂度$O(n^2)$。稳定。

## Selection Sort
所谓选择排序，就是持续选择$[i+1,n-1]$中最小的元素并与$i$交换，因此前面的部分必然全局有序。

```cpp
void selection_sort(int nums[], int n) {
	for (int i = 0; i < n; ++i) {
	    int min_id = i;
		for (int j = i + 1; j < n; ++j) {
			if (nums[j] < nums[min_id]) min_id = j;
		}
        swap(nums[i], nums[min_id]);
	}
}
```

选择排序需要N次交换以及$\frac{N^2}{2}$次比较，数据移动次数与数组大小呈线性关系，移动次数是最少的。
时间复杂度$O(n^2)$，不稳定。

## Insertion Sort
插入排序其实就是打牌：每次拿到一张牌$i$，从$i-1$开始向前扫描寻找第一个使得$cur>nums[j]$的位置$j$，找到位置后将$[j+1,i-1]$所有元素向后移一位，接着将拿到的牌放到$j+1$。不能保证前面的部分全局有序，因为后面拿到的牌可能是最小的。

```cpp
void insertion_sort(int nums[], int n) {
	for (int i = 1; i < n; ++i) {
		int cur = nums[i];
		int j = i - 1;
		while (j >= 0 && cur < nums[j]) {
			nums[j + 1] = nums[j];
			--j;
		}
		nums[j + 1] = cur;
	}
}
```

最好情况：元素全部有序，$N-1$次比较、$0$次交换；复杂度$O(n)$。  
最坏情况：元素全部逆序，大约$\frac{N^2}{2}$次比较和$\frac{N^2}{2}$次交换，复杂度$O(n^2)$。  
平均情况下：大约$\frac{N^2}{4}$次比较和$\frac{N^2}{4}$次交换，复杂度$O(n^2)$。稳定。

折半插入排序  
直接插入排序前面的子序列是有序的，所以如果是顺序表，那么可以先折半查找出元素的待插入位置，再统一移动该位置之后的所有元素：

```
void insertionSortOptimized(int A[], int n)
{
	//将A[i]插入到合适位置
	for (int i = 1; i < n; i++)
	{
		int tmp = A[i];
		int low = 0, high = i - 1;
		while (low <= high)
		{
			int mid = (low + high) >> 1;
			if (A[mid] > tmp)
			{
				high = mid - 1;
			}
			else
			{
				low = mid + 1;
			}
		}

		//统一后移元素
		for (int j = i - 1; j >= low; j--)
		{
			A[j + 1] = A[j];
		}

		A[low] = tmp;
	}
}
```
性能：折半插入排序将元素比较次数减少为$O(nlogn)$，但是移动次数依然是$O(n^2)$，故总的时间复杂度为$O(n^2)$。

## Shell Sort
基本思想：将待排序表分为若干$A[i], A[i+d], A[i+2d]...$子表，$d$称为增量，对这些子表执行直接插入排序，当整个表中的元素“基本有序”时，对整个表来一次直接插入排序。

希尔排序(Shell Sort)是基于插入排序的一种**不稳定**排序方法。  
1，将整个序列分为h个子序列；  
2，第一趟将每个子序列进行插入排序；  
3，第二趟将增量缩小，重复2；  
4，直至增量为1，就是简单插入排序。

eg:  
![这里写图片描述](20180707130824188.png)  
![这里写图片描述](20180707130836718.png)

希尔排序最优时间复杂度$O(n)$，最差情况下也突破了平方级别的运行时间。  
对于最差情况，之前的冒泡、选择要消除逆序，采用交换相邻元素的方法，也就是每次只能消除一个逆序，那么希尔每次交换隔得很远的元素，每次可以消除多个逆序，这样就节省了大量的交换时间。

```cpp
void shellSort(vector<int>& nums) {
	const int n = nums.size();
	for (int d = n >> 1; d > 0; d >>= 1) // 增量选之前的一半
		for (int i = d; i < n; ++i)  // 将nums[i]插入有序子表
			if (nums[i] < nums[i - d]) {
				int tmp = nums[i];
				int j;
				// 查找插入位置
				for (j = i - d; j >= 0 && tmp < nums[j]; j -= d)
					nums[j + d] = nums[j];  // 后移
				nums[j + d] = tmp;
			}
}
```
性能：时间复杂度依赖于选取的增量序列，大约是$O(n^{1.3})$，最坏是$O(n^2)$。


## Merge Sort
归并排序是将若干有序子表归并为一个新的有序表的算法. 初始时数组可以看作$n$个有序子表, 一趟2路归并排序可以将其合并为$n/2$个有序子表:

```cpp
void merge(vector<int>& num, int low, int mid, int high) {
	vector<int> tmp(high - low + 1);
	int i = low, j = mid + 1;  // 左有序和右有序开始位置
	int k = 0;
	while (i <= mid && j <= high) {
		if (num[i] < num[j]) {
			tmp[k++] = num[i++];
		}
		else {
			tmp[k++] = num[j++];
		}
	}
	while (i <= mid)
		tmp[k++] = num[i++];
	while (j <= high)
		tmp[k++] = num[j++];

	for (i = 0; i < k; ++i) {
		num[low + i] = tmp[i];
	}
}

void mergeSort(vector<int>& num, int low, int high) {
	if (low >= high)
		return;

	int mid = (low + high) >> 1;
	mergeSort(num, low, mid);
	mergeSort(num, mid + 1, high);
	merge(num, low, mid, high);
}
```
归并排序需要辅助数组, 因此空间复杂度为$O(n)$. 时间复杂度的分析与快排完全类似, 也是$O(nlogn)$, 不过归并排序是一种稳定的排序算法.

归并排序一个比较重要的点在于归并时可以做一些逆序对的统计等, 参考如下2题:  
[数组单调和](https://www.nowcoder.com/questionTerminal/8397609ba7054da382c4599d42e494f3)  
[数组中的逆序对](https://www.nowcoder.com/questionTerminal/bb06495cc0154e90bbb18911fd581df6)

## Heap Sort
堆是一种将数组看作complete binary tree的数据结构，分为大顶堆（parent>=children）和小顶堆（parent<=children），由于父结点和孩子结点这种奇妙的大小关系，堆也被用来做排序了...对于一颗完全二叉树，结点$i$的父结点为$(i-1)//2$，孩子结点为$2i+1,2i+2$。

排序前先要建堆，有2种主要的建堆方法（以大顶堆为例）：

 1. Top-down  
  Top-down的方式主要通过`HeapInsert`的方法，从空heap开始，每次插入并向上调整`sift_up`一个元素，复杂度$O(nlgn)$。
 2. Bottom-up  
  Bottom-up的方式主要依赖于一种叫做`heapify`的操作，你也可以叫它嬉皮化。对某个结点a进行`heapify`非常简单：对比a以及a两个孩子$c_1,c_2$的值，如果a是最大的，操作结束；否则将a与$max(c_1,c_2)$交换，递归直到a变为叶子结点或者a是三者中最大值，`heapify`操作的复杂度为$O(lgn)$。  
  可以发现：一次`heapify`下沉操作`sift_down`只能保证从a向下交换的路径上的每一棵局部小子树满足堆的性质（即只对原数组的部分位置进行了调整），并不能保证整棵树都满足堆的性质，甚至无法保证向下交换的整条路径满足根结点最大（如`[3 2 4 0 1 6 8]`对根操作后变为`[4 2 8 0 1 6 3]`），所以为了建堆，需要从最后一个非叶子结点$(n-1-1)//2$（也即最后一个结点的父结点）开始，对之前的每个结点都进行`heapify`操作，这样就可以保证整棵树都满足堆的性质。时间复杂度为$O(n)$，[How can building a heap be O(n) time complexity?](https://stackoverflow.com/questions/9755721/how-can-building-a-heap-be-on-time-complexity)  
  那么`heapify`建堆能不能从前向后进行呢？答案是不能，还是上面那个例子。从前往后最大元素调不到堆顶，从后往前则已经保证了父结点是最大的，因此最大元素可以一直向上调。

建好堆后，堆顶元素即为最大值，此时将堆顶（数组的第一个元素）和最后一个元素交换，则最后一个元素有序（最大值），但破坏了大顶堆性质，对堆顶元素进行`heapify`下沉操作（只需`heapify`前n-1个元素），保持大根堆即可：
```cpp
// Inserted num is now at idx and sift up
void heap_insert(vector<int>& nums, int idx) {
    while (nums[idx] > nums[(idx - 1) / 2]) {
        swap(nums[idx], nums[(idx - 1) / 2]);
        idx = (idx - 1) / 2;
    }
}

// 在[start, end]范围内对nums[start]向下调整
void heapify(vector<int>& nums, int start, int end) {
    if (start >= end) return;
    int maxIdx = start, left = 2 * start + 1, right = 2 * start + 2;
    if (left <= end && nums[left] > nums[maxIdx]) maxIdx = left;
    if (right <= end && nums[right] > nums[maxIdx]) maxIdx = right;
    if (maxIdx != start) {
        swap(nums[start], nums[maxIdx]);
        heapify(nums, maxIdx, end);
    }
}

void heapifyIter(vector<int>& nums, int start, int end) {
    while (2 * start + 1 <= end) {
        int maxIdx = start, left = 2 * start + 1, right = 2 * start + 2;
        if (left <= end && nums[left] > nums[maxIdx]) maxIdx = left;
        if (right <= end && nums[right] > nums[maxIdx]) maxIdx = right;
        if (maxIdx == start) {  // 父亲最大无法继续下沉
            break;
        }
        swap(nums[start], nums[maxIdx]);
        start = maxIdx;
    }
}

void buildMaxHeap(vector<int>& nums) {
    int lastIdx = nums.size() - 1;
    for (int i = (lastIdx - 1) / 2; i >= 0; --i) {
        heapify(nums, i, lastIdx);
    }
}

void heapSort(vector<int>& nums) {
    buildMaxHeap(nums);
    for (int i = nums.size() - 1; i > 0; --i) {
        swap(nums[0], nums[i]);
        heapify(nums, 0, i - 1);
    }
}
```
`heapify`方式的堆排序时间复杂度$O(nlogn)$。  
一般求前$K$大元素都采用堆排序，因为只需要调整$K$次，故$O(nlogK)$，而快排要将所有元素排完后才能取出前$K$个。

## Quick Sort
快排的核心思想是分治, 选一个`pivot`使得比`pivot`小的元素都存储在数组的左边, 比`pivot`大的元素存储在数组右边, 对左右两个子数组递归调用`quickSort`. 因此如何partition便成为快排的关键, 可以参考[01:40:00开始](https://www.bilibili.com/video/BV13g41157hK?p=3).

```cpp
/**
 * partition nums into two regions: <= >
 * 
 * [l, small_idx]: <= pivot
 * [small_idx + 1, i - 1]: > pivot
 * [i, r): unvisited
 */
int partition(vector<int>& nums, int l, int r) {
    srand(time(nullptr));
    int index = l + rand() % (r - l + 1);
    swap(nums[index], nums[r]);  // now the pivot is nums[r]
    int smallerIndex = l - 1;
    for (int i = l; i < r; ++i) {
        if (nums[i] <= nums[r]) {  // nums[i] < nums[r]也可
            swap(nums[i], nums[++smallerIndex]);
        }
    }
    swap(nums[r], nums[++smallerIndex]);
    return smallerIndex;
}

/**
 * partition num into 3 regions: < == > （荷兰国旗问题）
 * @return lowerbound and upperbound of pivot inclusive
 * 
 * [l, small_idx]: < pivot
 * [small_idx + 1, i - 1]: == pivot
 * [i, big_idx - 1]: unvisted
 * [big_idx, r): > pivot
 */
pair<int, int> partition(vector<int>& nums, int l, int r) {
    srand(time(nullptr));
    int index = l + rand() % (r - l + 1);
    swap(nums[index], nums[r]);  // now the pivot is nums[r]
    int smallerIndex = l - 1, greaterIndex = r;
    int i = l;
    while (i < greaterIndex) {
        if (nums[i] < nums[r]) {
            swap(nums[i++], nums[++smallerIndex]);
        }
        else if (nums[i] == nums[r]) {
            i++;
        }
        else {
            swap(nums[i], nums[--greaterIndex]);
        }
    }
    swap(nums[r], nums[greaterIndex]);
    return {++smallerIndex, greaterIndex};
}

void quickSort(vector<int>& nums, int l, int r) {
    if (l >= r) {
        return;
    }
    int pivotIndex = partition(nums, l, r);
    quickSort(nums, l, pivotIndex - 1);
    quickSort(nums, pivotIndex + 1, r);
}

// 非递归，用栈保存要操作的范围的下标
void quickSortIter(vector<int>& num, int low, int high) {
	stack<int> s;
	s.push(low);
	s.push(high);

	while (!s.empty()) {
		int r = s.top();
		s.pop();
		int l = s.top();
		s.pop();
		int pivotPos = partition(num, l, r);
		if (l < pivotPos - 1) {
			s.push(l);
			s.push(pivotPos - 1);
		}
		if (pivotPos + 1 < r) {
			s.push(pivotPos + 1);
			s.push(r);
		}
	}
}

int partition(vector<int>& num, int low, int high) {
	int pivot = num[low];
	while (low < high) {
		while (low < high && num[high] >= pivot) {
			--high;
		}
		num[low] = num[high];
		while (low < high && num[low] <= pivot) {
			++low;
		}
		num[high] = num[low];
	}
	num[low] = pivot;
	return low;
}
```

当每次划分的两个子问题规模分别为0和n时, 会触发最坏的时间复杂度$O(n^2)$. 当两个子问题规模相同时, 时间复杂度最好为$O(nlgn)$. 假设输入数据的所有排列不是等概率的, 那么为了避免最坏情况的发生, 选择pivot可以**随机**进行, 这样被pivot分开的两个子问题规模会接近$n/2$. 另外, 快排的时间复杂度中常数因子非常小, 所以是实际中使用最广泛的排序算法.

复杂度的证明既可以用主定理, 也可以自己推导, 在最优情况下:
$$
T(n)=2T(n/2)+n \\
=2(2T(n/4)+n/2)+n=4T(n/4)+2n \\
=4(2T(n/8)+n/4)+2n=8T(n/8)+3n \\
... \\
=nT(1)+nlogn=n+nlogn
$$

## Counting Sort
[计数排序](https://zhuanlan.zhihu.com/p/270158986)适用于数据量很大，但是数据类别很少的情况，可以做到线性时间。  
举例来看：如果有100万个字符串，但只有cat, dog, person三种类型，采用基于比较的排序方式，可以做到$NlogN$，计数排序采用了一种完全不同的思想：

 - 新建一个`counts[3]`，记录每种类型数据的出现次数；
 - 遍历待排序数组，完成`count[]`的统计，并创建一个结果数组`sorted[]`：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200624082843898.png)  
 - 基于`count[]`，我们完全可以知道第一个cat应该放置在0，第一个dog应该放置在`count[0]=4`处，第一个person应该放置在`count[0]+count[1]=6`处，为了更加清晰，创建一个`starts[3]`表示每类数据中的第一个的起始位置：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200624083353219.png)  
 - 接着第二次遍历待排序数组，遇到第一个cat，我们知道它应该放在`sorted[starts[0]]`；第一个dog应该放在`sorted[starts[1]]`，第二个dog应该放在`sorted[starts[1]+1]`。或者可以这样做：每当放置完一个dog，就`++starts[1]`，这样下一次的dog还是会放在`sorted[starts[1]]`，最终结果：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200624084335135.png)

对于字符串排序，我们需要规定`counts[]`中每个下标对应哪种类型。如果对于非负整数，我们可以用`counts[i]`表示i的出现次数，接着遍历`counts[]`，将整数i放置`counts[i]`次；如果有负数，可以找到最小值min和最大值max，平移到0~max-min即可。

```cpp
void countingSort(vector<int>& nums, int upper) {
    vector<int> cnt(upper + 1, 0);
    for (int num : nums) {
        cnt[num]++;
    }
    for (int i = 1; i <= upper; ++i) {
        cnt[i] += cnt[i - 1];
    }
    // cnt[i]: number of elements <= i
    vector<int> tmp(nums.size());
	// 从后往前遍历保证排序稳定性
    for (int i = nums.size() - 1; i >= 0; --i) {
        tmp[cnt[nums[i]] - 1] = nums[i];
        cnt[nums[i]]--;
    }
    nums.assign(tmp.begin(), tmp.end());
}
```

## Radix Sort
计数排序的前提就是需要知道待排序数组的内容/范围，那么如果范围很大，空间上是无法忍受的，由此来看更加general的基数排序：如果给定某种基（二进制2/十进制10/小写字母26）下的待排序数据，基数排序会逐位处理。基数排序有两种方式：

 1. MSD(Most Significant Digit)  
从高位向低位，每一位上可以用计数排序：  
356, 112, 904, 294, 209, 820, 394, 810；    
由于对第二位排序不能改变第一位排序的结果，所以要求按位排序算法必须是**稳定**的。
 2. LSD(Least Significant Digit)  
从右到左处理，每一位上可以用桶（队列）：  
[112], [294, 209], [356, 394], [820, 810], [904]；  
对于每个桶采用类似的方法直到最后一位，以[294, 209]为例，接着处理第二位：[209], [294]。  
最后收集每个桶中的元素即可。

比如对于$[13,21,11,52,62]$，准备0-9的10个桶，从个位数字看起，依次放入相应的桶中：  
1号桶：[21,11]左侧表示先进桶即队头  
2号桶：[52,62]  
3号桶：[13]  

接着收集得到$[21,11,52,62,13]$个位已经有序，接着从十位看起：  
1号桶：[11,13]  
2号桶：[21]  
5号桶：[52]  
6号桶：[62]

收集得到$[11,13,21,52,62]$，这一过程需要注意11和13两个元素，虽然十位相同，但是个位小的11先进桶并且也要先出桶排在13前面。

具体的实现可以用队列表达桶，但是一般都用`cnt[10]`和`tmp[n]`实现：`cnt`统计个位每个数字的频率，`tmp`接收每趟收集的结果并存入原数组。  
cnt: [0,2,2,1,0,0,0,0,0,0] -> [0,2,4,5,5,5,5,5,5,5]表示在原数组中个位<=2的数有4个  
对原数组**反向遍历**，62个位为2，cnt[2]=4因此62应该放在`tmp[3]`上，依次类推得到tmp: [21,11,52,62,13]完成第一趟

cnt: [0,2,1,0,0,1,1,0,0,0] -> [0,2,3,3,3,4,5,5,5,5]重复上述过程，之所以反向遍历，就是为了模拟进出桶时先进先出的性质。对于11和13，如果正向遍历，先看个位小的11，就会在`tmp[1]`中先放11然后在`tmp[0]`中放13违背了顺序。计算cnt前缀和数组时13是追加在11之上的，因此往`tmp`放置时需要先处理后面的13。

```cpp
void radixsort(vector<int>& num, int l, int r, int max_digit_cnt) {
    int radix = 1;
    vector<int> tmp(r - l + 1);
    for (int d = 0; d < max_digit_cnt; ++d) {
        vector<int> cnt(10, 0);
        for (int i = l; i <= r; ++i) {
            int digit = num[i] / radix % 10;
            ++cnt[digit];
        }
        for (int i = 1; i < 10; ++i) {
            cnt[i] += cnt[i - 1];
        }
        for (int i = r; i >= l; --i) {
            int digit = num[i] / radix % 10;
            tmp[--cnt[digit]] = num[i];
        }
        for (int i = l; i <= r; ++i) {
            num[i] = tmp[i];
        }
        radix *= 10;
    }
}
```

## Bucket Sort
```cpp
void bucketSort(vector<int>& nums, int upper) {
    int len = nums.size();
    vector<vector<int>> buckets(len);
    for (int num : nums) {
        buckets[num * len / upper].emplace_back(num);
    }
    int idx = 0;
    for (auto bucket : buckets) {
        std::sort(bucket.begin(), bucket.end());
        for (int num : bucket) {
            nums[idx++] = num;
        }
    }
}
```

![排序](https://www.runoob.com/wp-content/uploads/2019/03/sort.png)