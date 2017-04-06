Contents:

1. quicksort


```
// 快排
	function swap(arr, i, j) {
		var temp = arr[i];
		arr[i] = arr[j];
		arr[j] = temp;
	}
	
	var arr = [3, 7, 2, 1, 9, 3, 6];
	
	function quick_sort(arr, l, r) {
		if (l >= r) {
			return;
		}
		var i = l, j = r;
		var x = arr[i];
		while (i < j) {
			while (i < j && arr[j] >= x) {
				--j;
			}
			while (i < j && arr[i] <= x) {
				++i;
			}
			if (i < j) {
				swap(arr, i, j);
			}
		}
		swap(arr, l, i);
		
		quick_sort(arr, l, i-1);
		quick_sort(arr, i+1, r);
	}
	
	quick_sort(arr, 0, arr.length-1);
	console.log(arr);
```
