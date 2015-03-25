# CodingNote
总结了一些编程所遇到的问题、解决方案、技巧。

将本人在编程过程中的一些体会，以及网上看到的心得的记录和整理，方便以后自己和团队的参考，提高效率。如果本文对其他开发人员也有所帮助，也欢迎大家给出反馈或者提出改进意见。

#### if条件中对整数的判断 if(a)
遵从的原则是非0即为真
	
	int a1 = 2, a2 = 0, a3 = -2;
	if(a1) // 返回true
	if(a2) // 返回false
	if(a3) // 返回true

#### 使用位运算代替整数的乘法（左移）和除法（右移）
如果需要乘以或除以2n，都可以用移位的方法代替

	int a1 = 3;
	int a2 = a1 * 2; // a2 = a1 << 1;
	int a3 = a1 / 2; // a2 = a1 >> 1;

#### 使用位运算代替整数的取模（与运算）
如果需要和2n取模，都可以用与运算的方法代替

	int a1 = 3;
	int a2 = a1 % 4; // a2 = a1 & 3;

#### 避免不必要的除法
* 加法和减法，速度快于乘法和除法
* 避免连续除法（改用倒数的乘法）

#### 对大多数类，优先使用+= 、 -= 、 *= 和 /=，而不是使用+ 、 - 、 * 、 和/
	
	int a = b + c + e; // int a; a += b; a += c; a += e;

#### 对于对象，使用前缀操作符（++obj），而不是后缀操作符（obj++）
* 使用后缀操作符需要执行一次对象拷贝（这也导致了额外的构造和析构函数调用），而前缀的构造函数不需要一个临时的拷贝。
* 尤其在循环中，包括迭代器iterator

	`for(; ; ++iter);`

#### 对于循环，可以提前计算好判断循环终止条件的变量
* 每次处理判断条件时，都会造成重复计算
* 以vector为例，每次计算begin(), end(), size()都是比较耗时的操作，所以在循环中尽量避免
	
	`vector<int> v;`  
	`for(vector<int>::iterator iter = v.begin(); iter != v.end(); ++iter);`  
	`for(int i = 0; i < v.size(); ++i);`  
	`// 替换成如下代码`  
	`vector<int> v;`  
	`vector<int>::iterator iter_end = v.end(); // 提前计算好v.end()`  
	`for(vector<int>::iterator iter = v.begin(); iter != iter_end; ++iter);`  
	`int size = v.size(); // 提前计算好v.size()`  
	`for(int i = 0; i < size; ++i);`

* 在循环中执行不发生变化的部分，确保提取到循环外部，减少循环内部的计算量

#### 通过引用传递结构体而不是传值（使用&vector而不是vector）
#### 使用类初始化列表Color::Color() : r(0), g(0), b(0) {}，而不是初始化函数Color::Color() { r= g = b = 0; }
#### Switch语句中根据发生频率来进行case排序，最频繁使用的放在最前
#### C++中，string可以按照数组的样式进行操作，也有运算符重载
	
	string temp = "asdf";
	cout << temp[2]; // 输出"d"
	temp += temp;
	cout << temp; // 输出"asdfasdf"

#### 内存对齐，char 1字节，short 2字节，int 4字节，long 4字节，float 4字节，double 8字节。对齐时以最大的元素长度基准，进行对齐，所以总大小一定是最大元素长度的倍数