数据结构相关内容整理

将本人在编程过程中的一些体会，以及网上看到的心得的记录和整理，方便以后自己和团队的参考，提高效率。如果本文对其他开发人员也有所帮助，也欢迎大家给出反馈或者提出改进意见。

*	[STL相关实现](#stl)
*	[二叉树相关](#binarytree)
*	[红黑树相关](#rbtree)

<h4 id="stl">STL相关实现</h4>

模板
*   模板是泛型编程的基础，在实例化时需要指定所操作的类型，包括函数模板和类模板

        template<typename T>
        int compare(const T &v1, const T &v2) {
             if (v1 < v2) return -1;
             if (v2 < v1) return 1;
             return 0;
        }

        template<class T> class Queue {
        public:
             Queue();
             T &front();
             void push (const T &);
        }

*   编译器在看到模板被实例化时，就会产生对应类型的模板实例，如vector<string>和vector<int>就会产生两份代码（分别用string和int替换T），造成代码膨胀。
*   类模板的成员函数在实现时如下：

        template <class T> void Queue<T>::push(const T &val) {...}

*   STL（Standard Template Library）标准模板库，特点是数据结构和算法分离（如sort可用于vector，deque），基于模板而非继承，封装，多态，有足够的通用性，提高复用性。
*   STL是典型的基于泛型的编程，不依赖于数据结构的特定实现，而只是依赖于该结构的几个基本的语义属性，将算法用这种方法从特定实现中抽象出来, 而且效率无损（也就是对于不同的数据类型效率旗鼓相当）的编程方法

顺序容器（vector, list, deque, stack, queue, heap）和关联容器（二叉搜索树, 红黑树, set, map, hashmap, hashset）
*   vector 数组，动态增加（size和capacity，其中capacity变成原来大小的两倍）
*   list 双向链表
*   hashMap(unorderedMap)和hashSet(unorderedSet) 底层是hashtable，采用开链法，每个Bucket中是一个单链表，其他的hash探测方法有线性探测法（每次加1），二次探测法（每次加2的倍数），再散列法（用多个hash函数）。这种实现结构是无序的，查找速度快，适用于判断集合中是否存在某个元素
*   map和set / multimap和multiset 底层是红黑树。这种实现结构是有序的，适用于元素的添加和删除。map和set不允许元素重复，而multimap和multiset允许元素重复
*   deque 双向队列，头尾都可以添加，删除元素，逻辑上是连续空间。用一块小空间作为中控map，其中每个元素是一个指针，指向另一端连续空间（缓冲区），用来存放数据。stack和queue都是基于deque实现
*   heap STL中默认是最大堆，由于二叉堆的结构是一个完全二叉树，树内部没有节点漏洞，可以用vector进行存储，当前节点在i处，则左子节点在2i，右子节点在2i+1，父节点在i/2。所以堆的实现就是一个vector容器和一个heap算法（用来插入、删除、取极值）。在插入元素时，新元素放在vector尾部，向上调整；pop元素时，将vector尾部元素替换到头部，向下调整
*   priority_queue 允许任意次序将元素插入到容器中，但取出时一定是从优先级最高的开始取，会自动按照元素的权值排序。STL中默认是利用一个max-heap来实现

*   vector的数据结构包括iterator start，finish等，但是没有记录整个vector大小的count，所以size()方法的实现是return size_type(end() - begin());

*   map是基于红黑树实现的，所以map.size()方法实际上是调用RBTree.size()方法。由于红黑树的实现中是有记录树节点大小的变量，所以实现是return node_count;

*   sort 数据量大时用快排Quick Sort，数据量小时用插入排序Insertion Sort，如果递归层次过深，则改用堆排序Heap Sort

*   Visual C++使用的是PJ STL，GCC使用的是SGI STL。STL是C++的标准函数库，STl的六大组件是容器，算法，迭代器，适配器（Adaptor），分配器（Allocator），仿函数

*   Stack, queue, priority_queue(heap)是没有迭代器的，属于容器适配器（Container Adaptor）
*   迭代器iterator，能够依次访问容器内的元素，同时不会暴露该容器内的表述方式
*   仿函数，是行为类似函数的对象。类似于函数指针，但是比函数指针更具有泛型的特征。包含一元unary和二元binary仿函数（实现自己的仿函数时需要进行继承）

        template <class T>
        struct greater : public binary_function<T, T, bool> {
             bool operator()(const T &x, const T &y) const {return x > y;}
        }
        sort(data.begin(), data.end(), comp); // 函数指针
        sort(data.begin(), data.end(), greater<int>()); // 仿函数

---

<h4 id="binarytree">二叉树相关</h4>

完全二叉树（除最后一层外，叶子全满，最后一层叶子集中在左侧）和满二叉树（叶子全满）

二叉树的度是节点的子节点个数 n = n0 + n1 + n2; n0 = n2 + 1;总度数 + 1 = 总节点数

二叉搜索树的中序遍历有序

根据前序遍历和中序遍历，或是后序遍历和中序遍历，可以复原出一个二叉搜索树，仅有前序遍历和后序遍历则不能

---

<h4 id="rbtree">红黑树相关</h4>

红黑树是一种平衡二叉搜索树：
*   节点要么是红的，要么是黑的
*   根节点是黑的
*   每个叶子节点，即空节点是黑的
*   如果一个节点是红的，两个子节点就是黑的
*   对于每个节点，从该节点到对应的叶子节点的简单路径包含相同数目的黑色节点
*   从根到叶子的最长路径不多于最短路径的两倍
节点包含的属性: left, right, data, color, parent
