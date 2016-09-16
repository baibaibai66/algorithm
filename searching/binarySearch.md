# Binary Search
>Binary search is easiest difficult algorithm to get it right.

## Problem Statement

Given a sorted array of N distinct elements. Find a key in the array using least number of comparisons. (Do you think binary search is optimal to search a key in sorted array?)

**前提**：数组是有序（从小到大）。

### 最简单易思考的方式

没有理论，直接上经典的binary search算法。

```
// 返回key的位置； －1表示没找到
int BinarySearch(vector<int> nums, int key)
{
	int  n = (int)nums.size();
	int low = 0, high = n, mid;

    while (low < high) {
        mid = low + (high-low)/2; //经典溢出问题的改进
        
        if (nums[mid] == key) { //第一次比较
            return mid;
        }
        else if (nums[mid] > key) { //第二次比较
            high = mid;
        }
        else {
            low = mid + 1;
        }
    }
    
    return -1;
}
```

最坏情况下，上述binary_search需要logN + 1次比较：

每次进入while循环都需要进行两次比较，直到最后一次。

---

关于`nums.size()函数返回vector<int>::size_type`类型：

对于size()函数，如果定义返回值是一个int或者unsigned类型，编译器会给出警告，提示**精度缺失**问题。

其实，size()函数返回`vector<int>::size_type`类型，是一个**无符号类型的值**，而且**能够存放下任何vector对象的大小**。所有拥有存放`vector<int>`类的size函数返回值的变量，都应该是`vector<int>::size_type`类型的

>在c++中，string类以及大多数标准库类型都定义了几种配套的类型。这些配套类型体现了标准库类型谕机器无关的特性，类型size_type即是其中的一种。

在具体使用的时候，通过作用域操作符来表明名字size_type是在类string中定义的: 

`string::size_type`

**但是**

>在vector中，要是用`size_type`，需首先指定它是由哪种类型定义的。vector对象的类型总是包含着元素的类型：

```
vector<int>::size_type //right
vector::size_type //wrong
```

当然，在c++11中可以直接：

`auto n = nums.size(); //n的类型为vector::size_type`

这样就能理解`为什么用了(int)nums.size()`了。

---

### 问题及改进

如果数组中有多个元素值都等于`key`，上面的函数返回的是哪一个的下标呢？

**中间那个**

有时，当我们需要找出所有等于`key`的完整区间时，想要先找到第一个元素（毕竟有序数组，等于`k`的值会在一起），该如何做呢？

在C++ STL库中，有lower_bound和upper_bound函数，这里类似的实现一下。不过，在下面程序中，需要注意的是：
	
1. 当key存在时，返回它出现的第一个位置；
2. 当key不存在时，返回这样一个下标i：在此处插入key后（原来nums[i], nums[i+1]...全部后移）仍然有序；

也就是说，当调用该函数后，去查看返回位置的元素大小是否等于key：

- 如果相等，即为数组中存在该元素，且当前位置即为其出现的第一个位置；

- 如果不相等，即数组中不存在该元素




```
#include <iostream>
#include <vector>
using namespace std;

int lower_bound(vector<int> nums, int key)
{
    int n = (int)nums.size();
    int low = 0, high = n, mid;

    while (low < high) {
        mid = low + (high-low)/2;
        
        if (nums[mid] >= key) {
            high = mid;
        }
        else {
            low = mid+1;
        }
        
    }
    
    return low;
}
```

**这里需要好好理解一下**：

程序查找区间为：[low, high)，返回值的候选区间为：[low, high]

你可以去跑跑程序看一下。

**关于nums[mid]和key的各种关系**：

- nums[mid] = key

	至少找到一个，而左边可能还有，因此区间变为[low, mid]
	
- nums[mid] > key

	当前元素大于key，所找元素在位置mid之前，因此区间为[x, mid]

- nums[mid] < key

	当前元素小于key，所找元素在位置mid之后，因此区间为[mid+1, high]
	
类似的，还可以写一个upper_bound函数：

- 当存在与key值想等的元素时，返回它出现的最后一个位置的后面一个位置
- 如果不存在，存在一个这样的下标：在此处插入key后（原来nums[i], nums[i+1]...全部后移）仍然有序；

```
int upper_bound(vector<int> nums, int key)
{
    int n = (int)nums.size();
    int low = 0, high = n, mid;
    
    while (low < high) { //upper_bound
        mid = low + (high-low)/2;
        
        if (nums[mid] <= key) {
            low = mid+1;
        }
        else {
            high = mid;
        }
    }
    
    return low;
}
```

### 改进后的总结

已经有了lower_bound和upper_bound函数，即有了求解范围统计问题的“上下界”函数。

设两个函数的返回值分别为L和R，那么：

1. 如果数组中有同key值相同的元素，则其出现的子序列为[L, R)；
2. 如果不存在，此时L == R，区间为空。

到此，二分查找算是比较完整了。

## 供直接测试

### code

```
#include <iostream>
#include <vector>
using namespace std;

int BinarySearch(vector<int> nums, int key)
{
    int  n = (int)nums.size();
    int low = 0, high = n, mid;
    while (low < high) {
        mid = low + (high-low)/2; //经典溢出问题的改进
        
        if (nums[mid] == key) { //第一次比较
            return mid;
        }
        else if (nums[mid] > key) { //第二次比较
            high = mid;
        }
        else {
            low = mid + 1;
        }
    }
    return -1;
}

int lower_bound(vector<int> nums, int key)
{
    int n = (int)nums.size();
    int low = 0, high = n, mid;
    
    while (low < high) {
        mid = low + (high-low)/2;
        
        if (nums[mid] >= key) {
            high = mid;
        }
        else {
            low = mid+1;
        }
        
    }
    
    return low;
}

int upper_bound(vector<int> nums, int key)
{
    int n = (int)nums.size();
    int low = 0, high = n, mid;
    
    while (low < high) { //upper_bound
        mid = low + (high-low)/2;
        
        if (nums[mid] <= key) {
            low = mid+1;
        }
        else {
            high = mid;
        }
    }
    
    return low;
}

int main() {
    vector<int> v{1, 2, 2, 2, 3, 4};
    cout << lower_bound(v, 2) << endl;
    cout << upper_bound(v, 2) << endl << endl;
    cout << BinarySearch(v, 2) <<endl;
    return 0;
}
```

### result

```
1
4

3
```