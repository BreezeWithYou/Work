## UE4



## 容器

### TArray

TArray，是UE4的可动态扩容数组[容器](https://cloud.tencent.com/product/tke?from_column=20065&from=20065)，是UE4里最常见，也是用的最多的一种容器，类似于STL中的vector，除了数组的基本功能外，

[TArray 容器介绍](https://cloud.tencent.com/developer/article/1892625)

越界时会触发check（系统的assert）崩溃。在写代码时可能不确定是否越界的情况，也不能通过崩溃的方式避免，因此TArray还额外提供了IsValidIndex这样的inline函数，用于检查index是否为有效值，内部实现就是判断是否大于等于0，小于等于数组数量。虽然和自己手写判断没有本质区别，但是毕竟是一个常用操作，可以起到简化代码的作用。



可以使用上面这个RemoveAtSwap函数

供的几个函数可以轻而易举的让TArray变成小根堆，大根堆，然后还可以做堆排序，堆插入，堆删除。可能你会说快速排序和堆排序复杂度相同，直接快速排序就好了，干嘛费这么大功夫用维护一个堆。但在实际业务中，有不少情况用堆来实现功能会有明显的优势。最后会具体来说，先来介绍基本用法。



### TSet and TMap

元素本身并不连续存储，而是通过hash映射存储，因此相对于数组，查询元素是非常快速的。在之前的一篇文章里有提到，TSet是通过TSparseArray实现的，而TMap是通过TSet实现的。C++11也有类似的容器：std::unordered_set和std::unordered_map，实现也基本一致



TSet和TMap是支持排序的，如果你用过C++的unordered_set或unordered_map，你可能会觉得很震惊，一个本身通过hash索引的无序容器，名字都叫unordered，还能排序？这就是UE4这两个容器最有特色的地方。其实实现非常简单，前面也说了，因为内部实现本身是TSparseArray，迭代的时候是包装的TSparseArray迭代器进行访问的，而TSparseArray肯定是可以排序的，又因为Hash数组保存的是hash到index的映射，那么只要在排序后重新Rehash一遍，就完成了排序功能。



## GC 

UE4采用了标记-清扫垃圾回收方式，是一种经典的垃圾回收方式。一次垃圾回收分为两个阶段。第一阶段从一个根集合出发，遍历所有可达对象，遍历完成后就能标记出可达对象和不可达对象了，这个阶段会在一帧内完成。第二阶段会渐进式的清理这些不可达对象，因为不可达的对象将永远不能被访问到，所以可以分帧清理它们，避免一下子清理很多UObject，比如map卸载时，发生明显的卡顿。

GC发生在游戏线程上，对UObject进行清理，支持多线程GC。



## 网络同步的原理

Ue 的反射手段

1. Unreal以UObject为基类构建了一个一元化的对象系统. 
2. Unreal为每个特定的UObject子类，构建了该子类的UClass，UFunction，UProperty等元信息，我们也称之为反射信息。
3. 利用反射信息，我们可以知道一个类属性在数据结构中的偏移，进而可以对该属性进行读取和修改。
4. 利用反射信息，我们可以通过类函数的字符串名称，实现对成员函数的调用。
5. 基于以上两点，Unreal可以方便的实现属性的编码解码，以及远程过程函数及其参数的序列化和反序列化，并实现远程调用。

有了对反射的理解，我们以移动的RPC为例，介绍下远程过程调用的全过程。对于RPC相对比较熟悉的同学可以自行跳过。

所有的RPC都需要声明为UFUNCTION。在Unreal内部有三种RPC机制，

1. DS调用, 客户端执行。UFUNCTION中添加client修饰符。命名规则以Client为前缀。
2. 客户端调用，DS执行。UFUNCTION中添加server修饰符。命名规则以Server为前缀。
3. DS调用，DS和所有客户端执行。UFUNCTION中添加NetMulticast修饰符。命名规则以Multicast为前缀。



首先在使用UFunction(sever,client,NetMuticast) 反射系统会在 gen.cpp 中生成该函数的函数体，通过 `ProcessEvent` 调用执行该函数，并最终转发到 `CallRemoteFunction` ，在最终转发的函数上会先 GetActorConnection 。所以说RPC都依赖于连接（UNetConnection）创建的通信信道（UChannel）RPC的后续处理和属性同步类似，都是使用利用反射系统构造的FRepLayout（同步属性泪飙）进行编码构造Bunch包，然后通过网络发送给对端。



注意事项：

1. DS调用在客户端执行的RPC时，如果对象本身没有同步过，则会触发一次属性同步。 
2. 非可靠RPC不会立即发送，会放在队列中统一发送。
3. 可靠RPC会立即发送；且管理也略微复杂，有发送待确认队列（大小通过RELIABLE_BUFFER定义）和接收排序队列；在待确认队列满和构造编码包失败的时候触发连接的关闭。所以一些高频操作慎用reliable RPC。