# C++ Stl 复习

https://github.com/huihut/interview/blob/master/STL/STL.md

## 概述部分

STL — Standard Template Library，标准模板库

- *容器*：放数据
- *分配器*：是来支持容器将数据放到内存里
- *算法*：是一个个函数来处理存放在容器里的数据
- *迭代器*：就是来支持算法操作容器的
- *仿函数*：作用类似函数，例如相加相减等等
- *适配器*：有三种，分别将容器，迭代器，仿函数来进行一个转换

![](https://img-blog.csdnimg.cn/img_convert/27cf3c914a48eb17215ed6ee2ab96dd8.png)

### 容器分类

分类：**序列式（顺序）容器** *Sequence Container*，**关联式容器** *Associative Container* **容器适配器**

* 序列式容器：按照放入的次序进行排列

![](https://img-blog.csdnimg.cn/img_convert/bed6c43c7d21b1960111552ca0930afb.png)

下面容器均为序列式（顺序）容器

Array 数组，固定大小

Vector 向量，会自动扩充大小，每次扩充 容量*=   2

Deque 双向队列，双向都可以扩充

List 链表，双向链表

Forward-List 链表，单向链表

**关联式容器**：有 *key* 和 *value*，适合快速的查找

STL中实现使用红黑树（高度平衡二叉树和哈希表

Set，*key* 就是 *value*，元素不可重复 

Map，*key* 和 *value* 是分开的，元素不可重复

MultiSet，元素是可以重复的

* multiset.size()

* multiset.find(), log(n) 时间复杂度, 使用全局的即 ::find(multiset.begin(), multiset.end());

MultiMap, 不可用 [] 进行插入操作，但是Map是可以的，因为MultiMap 的一个键可能对应多个值，但Map，一个键只能对应一个值。 

Unordered~，HashTable Separate Chaining

### STL 容器

| 容器                                                                                           | 底层数据结构            | 时间复杂度                                 | 有无序 | 可不可重复 | 其他                                               |
| -------------------------------------------------------------------------------------------- | ----------------- | ------------------------------------- | --- | ----- | ------------------------------------------------ |
| [array](https://github.com/huihut/interview/tree/master/STL#array)                           | 数组                | 随机读改 O(1)                             | 无序  | 可重复   | 支持随机访问                                           |
| [vector](https://github.com/huihut/interview/tree/master/STL#vector)                         | 数组                | 随机读改、尾部插入、尾部删除 O(1)<br>头部插入、头部删除 O(n) | 无序  | 可重复   | 支持随机访问                                           |
| [deque](https://github.com/huihut/interview/tree/master/STL#deque)                           | 双端队列              | 头尾插入、头尾删除 O(1)                        | 无序  | 可重复   | 一个中央控制器 + 多个缓冲区，支持首尾快速增删，支持随机访问                  |
| [forward_list](https://github.com/huihut/interview/tree/master/STL#forward_list)             | 单向链表              | 插入、删除 O(1)                            | 无序  | 可重复   | 不支持随机访问                                          |
| [list](https://github.com/huihut/interview/tree/master/STL#list)                             | 双向链表              | 插入、删除 O(1)                            | 无序  | 可重复   | 不支持随机访问                                          |
| [stack](https://github.com/huihut/interview/tree/master/STL#stack)                           | deque / list      | 顶部插入、顶部删除 O(1)                        | 无序  | 可重复   | deque 或 list 封闭头端开口，不用 vector 的原因应该是容量大小有限制，扩容耗时 |
| [queue](https://github.com/huihut/interview/tree/master/STL#queue)                           | deque / list      | 尾部插入、头部删除 O(1)                        | 无序  | 可重复   | deque 或 list 封闭头端开口，不用 vector 的原因应该是容量大小有限制，扩容耗时 |
| [priority_queue](https://github.com/huihut/interview/tree/master/STL#priority_queue)         | vector + max-heap | 插入、删除 O(log2n)                        | 有序  | 可重复   | vector容器+heap处理规则                                |
| [set](https://github.com/huihut/interview/tree/master/STL#set)                               | 红黑树               | 插入、删除、查找 O(log2n)                     | 有序  | 不可重复  |                                                  |
| [multiset](https://github.com/huihut/interview/tree/master/STL#multiset)                     | 红黑树               | 插入、删除、查找 O(log2n)                     | 有序  | 可重复   |                                                  |
| [map](https://github.com/huihut/interview/tree/master/STL#map)                               | 红黑树               | 插入、删除、查找 O(log2n)                     | 有序  | 不可重复  |                                                  |
| [multimap](https://github.com/huihut/interview/tree/master/STL#multimap)                     | 红黑树               | 插入、删除、查找 O(log2n)                     | 有序  | 可重复   |                                                  |
| [unordered_set](https://github.com/huihut/interview/tree/master/STL#unordered_set)           | 哈希表               | 插入、删除、查找 O(1) 最差 O(n)                 | 无序  | 不可重复  |                                                  |
| [unordered_multiset](https://github.com/huihut/interview/tree/master/STL#unordered_multiset) | 哈希表               | 插入、删除、查找 O(1) 最差 O(n)                 | 无序  | 可重复   |                                                  |
| [unordered_map](https://github.com/huihut/interview/tree/master/STL#unordered_map)           | 哈希表               | 插入、删除、查找 O(1) 最差 O(n)                 | 无序  | 不可重复  |                                                  |
| [unordered_multimap](https://github.com/huihut/interview/tree/master/STL#unordered_multimap) | 哈希表               | 插入、删除、查找 O(1) 最差 O(n)                 | 无序  | 可重复   |                                                  |

## 分配器

**STL分配器内部原理**
STL分配器用于容器内存管理。主要职责是: new申请空间; delete释放空间。为了精密分工,分配器要将两阶段分开: 

1. 内存配置先由allocate0 (operator new0)完成，然后对象构造由
   构造函数负责; 

2. 对象析构先由析构函数完成，内存释放由deallocate0 (operator delete0)完成。

C++默认的内存管理采用malloc(), free() 等，会频繁的在堆动态分配和回收内存，内存空间碎片化严重，导致空间利用率低。内存池很好的解决了这个问题，它是针对小对象而言的，首先申请一定数量，指定大小(通常8B)的内存块，当有新的内存申请就拿出一个块，如果不够再申请。
算法:

1. 预申请-个内存区chunk,将内存中按照对象大小划分成多个内存块block

2. 维持- -个空闲内存块链表，通过指针相连，标记头指针为第一个空闲块

3. 每次新申请-个对象的空间，则将该内存块从空闲链表中去除，更新空闲链表头指针

4. 每次释放一一个对象的空间，则重新将该内存块加到空闲链表头

5. 如果-个内存区占满了，则新开辟一个内存区, 维持一个内存区的链表， 同指针相连，头指针指向最新的内存区，新的内存块从该区内重新划分和申请



**STL的两级分配器**
为了提升内存管理效率，STL采用两级分配器:

对于大于128B的内存申请,采用第一级分配器，用malloc(),realloc(), free()进行空间分配;

对于小于128B的内存申请,采用内存池技术，采用链表进行管理。

[C++内存管理-分配器](https://blog.csdn.net/weixin_52665939/article/details/131620359)

自定义分配器：

* 内存共享：对于多进程或者多线程应用，我们可能需要共享内存空间。自定义分配器可以使我们将对象存储在共享内存中。

* 内存泄漏探测：在复杂的应用中，内存泄漏可能是一个难以定位的问题。自定义分配器可以帮助我们追踪内存的分配和释放，从而检测内存泄漏。

* 预分配对象存储：对于一些知道内存需求的应用，预先分配内存可以避免频繁的内存分配和释放，提高性能。

* 内存池：对于频繁分配和释放小块内存的应用，使用内存池可以减少内存碎片，提高性能。

分配器都是与容器共同使用的，一般分配器参数用默认值即可

*allocator* 中有 `allocate`，`deallocate` 其分别用函数 `::operator new` 和 `::operator delete` 来调用 c 中的 *malloc* 和 *free*

这里经过包装还是调用的 malloc 和 free，其执行效率变慢；且如果申请的空间比较小，会有<u>较大比例的额外开销</u>（cookie，调试模式所需空间等等）

## 红黑树

[平衡树](https://zhuanlan.zhihu.com/p/509160379)

红黑树是对平衡树的一种改进，平衡树严格要求每个节点的高度差相等，导致每次进行插入/删除节点的时候，都需要通过左旋和右旋来进行调整。红黑树通过维持以下性质，保证了高度差不超过一半，避免了频繁的调整。

**性质**

* 每个结点要么是红色要么是黑色
* 根节点是黑色的，叶节点是黑色的空节点、
* 任何相邻的节点都不能同时为红色，也就是说，红色节点是被黑色节点隔开的；
* 所有红色结点的两个孩子结点都是黑色的
* 任意节点到其可到达的叶节点之间有相同数量的黑色结点



**红黑树的插入**

1. 插入新节点，按照平衡二叉树的方式

2. 插入该结点后，将该结点染成红色

3. 双红修正：如果插入后的结点的父节点为红色，所以上面第 4 条性质，需要进行修正
   
   * 结点左右旋转
   
   * 结点颜色改变
   
   [bilibili红黑树详解，从 11.13 开始讲步骤3](https://www.bilibili.com/video/BV1Ru4y1G77q/?spm_id_from=333.337.search-card.all.click&vd_source=ecc58cd957e33aab43b117e32abc6db9)

## Vector

**参考链接：**

[C++关于vector的详细介绍_c++ vector-CSDN博客](https://blog.csdn.net/2301_76366823/article/details/128876930)

**Vector 概述**

vector底层本质是一个顺序表，它是一个可变长的数组，采用连续存储的空间来存储数据。与数组相比，vector消耗更多的内存来交换管理存储和以有效方式动态增长的能力。

**Vector 扩容规则**

其实vector一次扩容多少倍并没有确定的数值，一般就是1.5倍增长到2倍增长这样一个范围，具体的由编辑器确定。

Vector 常用

`vector::insert` 时间复杂度`O(N)`

`vector::clear` 

从vector中删除所有的元素（被销毁），留下size为0的容器，capacity 保持不变。

**`emplace_ back()`和`push_ back()`哪个更好，为什么?**

`emplace_ back()`更好，因为它调用的是移动构造函数。而push_ back()调用的是拷贝构造函数移动构造函数不需要分配新的内存空间，所以更快一些。

```C++
// 111_ Alloc表示内存分配器，
此参数几乎不需要我们关心
templatetypename _Tp, typename. Alloc>
struct_ Vector_ base
{
struct_ Vector_ impl
: public. Tp_ alloc type
{
pointer_ M start;
pointer M finish;
pointer M end 
_of_ storage;
}

```



## List

List.sort()（而不用全局的sort函数）, 之所以那么特殊，是因为他的迭代器类型不是随机访问迭代器。

全局sort函数：可以看到该函数使用的是随机访问迭代器。

## deque queue stack

deque上常见操作的复杂性（效率）如下：

* 随机访问 - 常数O(1)

* 在结尾或开头插入或移除元素 - 摊销不变O(1)

* 插入或移除元素 - 线性O(n)

双端队列）是double-ended queue 的一个不规则缩写。deque是具有动态大小的序列容器，可以在两端（前端或后端）扩展或收缩。

[deque 详细介绍](https://blog.csdn.net/weixin_57439178/article/details/130721509)

## Map

map 是关联容器，按照特定顺序存储由 key value (键值) 和 mapped value (映射值) 组合形成的元素。

map是STL的一个关联容器，以键值对存储的数据，其类型可以自己定义，每个关键字在map中只能出现一次，关键字不能修改。

   红黑树 ，是一种 二叉搜索树 ，但 在每个结点上增加一个存储位表示结点的颜色，可以是 Red 或Black 。 通过对 任何一条从根到叶子的路径上各个结点着色方式的限制，红黑树确保没有一条路径会比其他路径长出俩倍 ，因而是 接近平衡 的。

红黑树的性质

* 每个结点不是红色就是黑色
* 根节点是黑色的 
* 每个叶子结点都是黑色的 ( 此处的叶子结点指的是空结点 )
* 如果一个节点是红色的，则它的两个孩子结点是黑色的 
* 对于每个结点，从该结点到其所有后代叶结点的简单路径上，均包含相同数目的黑色结点 


原文链接：https://blog.csdn.net/zhao19971014/article/details/127765817



在映射中，键值通常用于对元素进行排序和唯一标识，而映射的值存储与此键关联的内容。该类型的键和映射的值可能不同，并且在部件类型被分组在一起VALUE_TYPE，这是一种对类型结合两种：



map支持唯一键值，每个键只能出现一次；而multimap中相同键可以出现多次。multimap不支持[]操作符。

## hastable

**哈希函数：**

哈希函数：所以哈希函数的设计是hastable的关键。哈希函数将“键”映射成的“索引”，

设计要点：

- 一致性：一样的键对应一样的哈希映射
- 高效性：计算高效便捷
- 均匀性：哈希值均匀分布(取模）

除留余数法 

- 取关键字被某个不大于散列表长度 m 的数 p 求余，得到的作为散列地址。

直接定址法

取关键字的某个线性函数为散列地址：Hash(key)= A*key + B。优点：简单，速度快，节省空间，查找key O(1)的时间复杂度 缺点：当数据范围大时会浪费空间，不能处理浮点数，字符串数据。 使用场景：适用于整数，数据范围比较集中

常见的 has 函数的实现过程：

先将key 转成整形处理获取-> 获取hascode 之后对表（桶）取余得到has 值。



当 元素个数大于容量个数时，会进行 rehash 操作，

`rehash()`的时间复杂度为O( n )



### resize和reserve的区别

reserve：

- reserve 用于为容器预留空间，但不真正在预留的空间中创建对象。这意味着，当使用 reserve 时，容器的 capacity 会被修改，但 size 保持不变。
- reserve 的使用场景是，在编程时已知容器将来需要容纳的元素数量，从而提前为这些元素预留足够的空间。这样可以避免在运行时频繁地进行内存分配和对象拷贝，从而提高内存使用率和CPU效率。
- reserve 函数接受一个参数，表示需要预留的容器的空间大小。

resize：

- resize 用于改变容器的当前大小，并且如果指定了第二个参数，它还会在新的空间中创建这些对象。
- 当使用 resize 时，容器的 capacity 和 size 都会被修改，以反映新的元素数量。
- resize 函数可以有两个参数，第一个参数指定新的容器大小，第二个参数指定要加入容器中的新元素。如果第二个参数被省略，则调用元素对象的默认构造函数。

