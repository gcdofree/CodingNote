数据结构相关内容整理

将本人在编程过程中的一些体会，以及网上看到的心得的记录和整理，方便以后自己和团队的参考，提高效率。如果本文对其他开发人员也有所帮助，也欢迎大家给出反馈或者提出改进意见。

#### STL相关实现

顺序容器（vector, list, deque, stack, queue, heap）和关联容器（二叉搜索树, 红黑树, set, map, hashmap, hashset）
*   vector 数组，动态增加（size和capacity，其中capacity变成原来大小的两倍）
*   list 双向链表
*   hashMap(unorderedMap)和hashSet(unorderedSet) 底层是hashtable，采用开链法，每个Bucket中是一个单链表，其他的hash探测方法有线性探测法（每次加1），二次探测法（每次加2的倍数），再散列法（用多个hash函数）。这种实现结构是无序的，查找速度快，适用于判断集合中是否存在某个元素
*   map和set / multimap和multiset 底层是红黑树。这种实现结构是有序的，适用于元素的添加和删除。map和set不允许元素重复，而multimap和multiset允许元素重复
*   deque 双向队列，头尾都可以添加，删除元素，逻辑上是连续空间。用一块小空间作为中控map，其中每个元素是一个指针，指向另一端连续空间（缓冲区），用来存放数据。stack和queue都是基于deque实现
*   heap STL中默认是最大堆，由于二叉堆的结构是一个完全二叉树，树内部没有节点漏洞，可以用vector进行存储，当前节点在i处，则左子节点在2i，右子节点在2i+1，父节点在i/2。所以堆的实现就是一个vector容器和一个heap算法（用来插入、删除、取极值）。在插入元素时，新元素放在vector尾部，向上调整；pop元素时，将vector尾部元素替换到头部，向下调整
*   priority_queue 允许任意次序将元素插入到容器中，但取出时一定是从优先级最高的开始取，会自动按照元素的权值排序。STL中默认是利用一个max-heap来实现


#### 二叉树相关