# Binary Search | Set 1

>Binary search is easiest difficult algorithm to get it right.

## Problem Statement

Given a sorted array of N distinct elements. Find a key in the array using least number of comparisons. (Do you think binary search is optimal to search a key in sorted array?)


### 最简单的方式

没有理论，直接上经典的binary search算法.

```
// 返回key的位置； －1表示没找到
vector<int>::size_type BinarySearch(vector<int> nums, int key)
{
    vector<int>::size_type n = nums.size();
    vector<int>::size_type low = 0, high = n-1, mid;

    while (low <= high) {
        mid = low + (high-low)/2; //经典溢出问题的改进
        
        if (nums[mid] == key) { //第一次比较
            return mid;
        }
        else if (nums[mid] > key) { //第二次比较
            high = mid - 1;
        }
        else {
            low = mid + 1;
        }
    }
    
    return -1;
}
```

我这里采用比较繁琐的方式(`vector<int>::size_type`，而没有直接定义为`int`)来实现**binary search**，相信在底层库中必然是直接才用模版形式。关于这部分以后再说吧！目前先按照`vector<int>::size_type`来码。

最坏情况下，上述binary_search需要logN + 1次比较：

每次进入while循环都需要进行两次比较，直到最后一次。

---

关于`vector<int>::size_type`类型：

>在c++中，string类以及大多数标准库类型都定义了几种配套的类型。这些配套类型体现了标准库类型谕机器无关的特性，类型size_type即是其中的一种。

在具体使用的时候，通过作用域操作符来表明名字size_type是在类string中定义的: 

`string::size_type`

**但是**

>在vector中，要是用`size_type`，需首先指定它是由哪种类型定义的。vector对象的类型总是包含着元素的类型：

```
vector<int>::size_type //right
vector::size_type //wrong
```

对于size()函数，如果定义返回值是一个int或者unsigned类型，编译器会给出警告，提示**精度缺失**问题。

其实，size()函数返回`vector<int>::size_type`类型，是一个**无符号类型的值**，而且**能够存放下任何vector对象的大小**。所有拥有存放`vector<int>`类的size函数返回值的变量，都应该是`vector<int>::size_type`类型的

当然，在c++11中可以直接：

`auto n = nums.size(); //n的类型为vector::size_type`

这样就能理解`vector<int> n = nums.size()`了。

---

### 改进

使用更少的比较次数：

```
// nums[high] > key; nums[low] <= key;
// 边界：|high - low| = 1
vector<int>::size_type BinarySearch(vector<int> nums, int key)
{
    vector<int>::size_type n = nums.size();
    vector<int>::size_type low = 0, high = n-1, mid;

    while ((high-low) > 1) {
        mid = low + (high-low)/2;
        
        if (nums[mid] > key) {
            high = mid;
        }
        else {
            low = mid;
        }
        
    }
    if (nums[low] == key) {
        return low;
    }
    else {
        return -1;
    }
}
```

在while循环中，只需要一次比较。