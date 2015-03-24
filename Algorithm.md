# AlgorithmNote
常用算法整理

将本人在编程过程中的一些体会，以及网上看到的心得的记录和整理，方便以后自己和团队的参考，提高效率。如果本文对其他开发人员也有所帮助，也欢迎大家给出反馈或者提出改进意见。

#### 二分查找 Binary Search
时间复杂度O(logn)，空间复杂度O(1)
	
	int searchInsert(int A[], int n, int target) {
      // O(logn) 二分查找
      int low = 0;
      int high = n - 1;
      while (low <= high) {
          int mid = (low + high) / 2;
          if (A[mid] == target)
             return mid;
          if (A[mid] < target)
             low = mid + 1;
          else
             high = mid - 1;
      }
      return low;
	}

当循环结束时，如果没有找到目标元素，那么low一定停在恰好比目标大的index上，high一定停在恰好比目标小的index上

#### 快速排序 Quick Sort
平均时间复杂度O(nlogn)，最坏情况（已经从小到大排好序的数组）是O(n^2)，空间复杂度O(1)