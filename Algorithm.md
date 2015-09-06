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
快排的实现：Hoare版本，在partition函数中从两侧双向至中间扫描

	int partition (int A [], int start , int end)
	{
		int pivotValue = A [start];
		int left = start -1 ;
		int right = end +1 ;
		
		while ( true )
		{
			//双向扫描
            do
            {
                 right --;
            } while (A [right] > pivotValue );

            do
            {
                 left ++;
            } while (A [left] < pivotValue );

            if ( left < right)
            {
                 int temp = A [left];
                 A [left] = A[ right];
                 A [right] = temp;
            }
            else
            {
                 return right ;
            }
		}
	}

	void quickSort (int A [], int start , int end)
	{
		int pivot ;
		if ( start < end)
		{
            pivot = partition( A , start , end );
            quickSort (A , start , pivot );
            quickSort (A , pivot + 1 , end );
		}
	}

	int main()
	{
		int test [] = {...};
		quickSort (test , 0 , N-1 );
		return 0 ;
	}


#### STL自带的sort方法（快排）
可以自定义判断规则，需要引入头文件<algorithm>，a在b前时，返回true

	static bool comp( int a , int b) {
		return a < b ;
	}
	
	bool comp( const Person &a , const Person &b) {
      return a.age < b.age ;
	}

	
sort 数据量大时用快排Quick Sort，数据量小时用插入排序Insertion Sort，如果递归层次过深，则改用堆排序Heap Sort

#### 查找数组中的元素时，可以先排序，再用二分查找

#### 合并两个数组（包括字符串）时，如果是从前往后复制每个数字（或字符），需要重复移动后面的数字（或字符）多次，可以考虑从后往前复制，从而减少移动的次数，复杂度O(n^2) -> O(n)
