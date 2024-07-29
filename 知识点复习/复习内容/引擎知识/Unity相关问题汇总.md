[优化Unity游戏中的垃圾回收 - 简书 (jianshu.com)](https://www.jianshu.com/p/84318fd7d29e)

## Unity GC

**Mono 2.8  之前**

最早的Unity 只有Mono GC 没看过不是很了解。但应该是非渐进式，不分代，大小内存不分开管理的

**li2cpp**

li2cpp 贝姆垃圾收集器是计算机应用在C/C++语言上的一个保守的[垃圾回收](https://link.zhihu.com/?target=https%3A//baike.baidu.com/item/%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6/0%3FfromModule%3Dlemma_inlink)器

BoehmGC是一种**mark-sweep(**标记-清扫)算法,大致思路包含了四个阶段:

- 准备阶段: 每个托管堆内存对象在创建出来的时候会有一个关联的标记位,来表示当前对象是否被引用,默认为0。
- 标记阶段: 从根内存节点(静态变量;栈;寄存器)出发,遍历扫描托管堆的内存节点,将被引用的内存节点标记为1。
- 清扫阶段: 遍历所有节点,将没有被标记的节点的内存数据清空,并且基于一定条件释放。
- 结束阶段: 触发注册过的回调逻辑

在整个内存分配链的最底部，BoehmGC通过平台相关接口来向操作系统申请内存。为了提高申请的效率,每次批量申请4K的倍数大小。

小内存是以**粒度（GRANULES）**对齐为基本的空闲链表管理（概念，即一个GRANULE的大小是**16字节**）

大内存是以4K为大小的内存进行对齐分配。

**SGen** 

**mark-sweep** 的基础上加了分代压缩，第一代不分大小内存，第二代分大小内存

## Unity C# 反射

Unity 反射主要是通过侵入式的UObject 的设计实现的反射的设计，来实现反射。在C#编译的时候，就会将C#源代码编译成程序集，而我们可以在加载程序运行时，动态获取和加载程序集。

程序集中包含有Microsoft 中间语言 (MSIL) 和必需的元数据，反射可以读取程序集中的元数据，利用元数据创建对象，从而实现各种功能。

元数据就是去记录类中的信息，比如序列化字段实际就是添加一条元数据

## Unity 生命周期

**主要的生命周期函数：**

 Awake >OnEnable >Start > FixedUpdate > Update>Yield >LateUpdate >OnGui>OnDisable>OnDistroy

[【Unity】Unity 生命周期-CSDN博客](https://blog.csdn.net/xiaoyaoACi/article/details/119324146)

Unity 生命周期 - 开始

**初始化**

—>Awake

仅执行一次，游戏物体创建时调用一次（不管脚本激没激活），常用于初始化

—>OnEnable 

可调用多次，当组件设置enable = true、游戏物体设置active = true或脚本激活第一次run时调用

—>Start

仅执行一次，在脚本被激活并完成初始化时调用一次（在第一次Update之前），与组件相关

—>FixedUpdate

固定时间更新，默认50fps

—>Update

当游戏运行且脚本启用时，每帧触发一次（时间不固定，与物理引擎无关的更新在这里）

—>LateUpdate

每帧执行，在其他更新执行完之后执行。（一般用于命令脚本执行，比如物体移动位置之后的相机跟随）

—>OnGUl

渲染

—>OnDisable

当组件设置enable = false、游戏物体设置active = false 或脚本激活第一次run时调用

—>OnDestroy



![](https://gitee.com/li-jiaqin-2022/pircture-all/raw/master/08.png)













## Unity Shader Lab

Shader Lab 是使用 Unity 提供的编写 Unity Shader 的一种说明性语言。它使用了一些嵌套在花括号内部的语义来描述一个 Unity Shader 文件的结构。这些结构包含了许多渲染所需数据，例如 Properties 语句块中定义的着色器所需的各种属性，这些属性将会出现在材质面板上。**不仅仅是着色器代码，还有显示一个材质所需的全部东西**

```Unity
Shader "ShaderName"
{
    Perperties
    {
        //属性
    }

    SubShader
    {
        //显卡 A 使用的子着色器
    }

    SubShader
    {
        //显卡 B 使用的子着色器
    }
}
```

### Properties

属性（Property）的定义如下：

```
Properties
{
    Name {"display name", PropertyType} = DefaultValue
    //更多属性
}
```

- Name： 在 Shader 中访问属性的渠道
- display name：出现在材质面板的名字
- PropertyType：属性的类型
- DefaultValue：默认值

常见的属性类型，其中后三个比较特殊由字符串（要么是空，要么是内置的纹理名字）+ 花括号组成（需要在 Vertex Shader 中指定是否生成纹理坐标）

![Image](https://pic4.zhimg.com/80/v2-092383328054b13fdb435025841a3ed2.jpg)

### SubShader

每个 Shader 文件中至少有一个 SubShader。Unity 加载 Shader 时，Unity会扫描所有的 SubShader语义块，然后选择第一个能在目标平台上运行的 SubShader，如果都不能运行， Unity 会使用 Fallback 语义定义的Unity Shader。

SubShader 语义块中包含的定义通常如下：

```
SubShader
{
    //可选的
    [Tags]

    //可选的
    [RenderSetup]

    Pass
    {
        ...
    }
    //Other Passes
}
```

可以在 SubShader 中定义一系列 Pass，每一个 Pass 定义了一次完整的渲染流程（Pass太多会导致渲染性能下降，所以尽可能定义较少的 Pass），状态([RenderSetup]) 和 标签（[Tags]）也可以在Pass中声明。对于标签来说，Pass中定义的标签和外部定义的标签意义不一样。而对于状态来说，内外的意义一样，所以在外面定义了，那么所有 Pass 都会生效。

#### 状态设置

这些值可以在Pass 进行设置，但有些设置是独特的，并且能作用于全部的 Pass

![](https://pic4.zhimg.com/80/v2-19875754250bdf28e4a8b81cfa70bc05.jpg)

#### SubShader 的标签

该标签是一个**键值对**（Key/Value Pair），键和值都是字符串类型，这些键值是 SubShader 和渲染引擎之间沟通的桥梁。它告诉渲染引擎：我们希望怎么以及何时渲染这个对象。

标签的结构：

```
Tags {"TagName1" = "Value1" "TagName2" = "Value2"}
```

标签类型（这是SubShader中的标签，不能用于Pass块）： ![Image](https://pic4.zhimg.com/80/v2-711e3aab5abd27ce6a81dc4a961584ca.jpg)

#### Pass 语句块

定义：

```
Pass
{
    [Name]
    [Tags]
    [RenderSetup]
    //OtherCode
}
```

首先，我们可以定义Pass的名字：

```
Name "MyPassName"
```

通过这个名字，我们可以使用ShaderLab中的UsePass命令直接使用其他 Unity Shader 中的Pass，这样可以提高代码的复用性。

```
UsePass "MyShader/MYPASSNAME"
```

需要注意 Pass 的名字会被转成大写表示，所以我们使用时也得用大写

Pass 中的标签： ![Image](https://pic4.zhimg.com/80/v2-ab6c1a8e3174e5a30e9563b476427364.jpg)

### Fallback

他告诉我们如果所有的 SubShader 在这块显卡中都不可用，那就使用最低级的Shader。

他的定义如下：

```
Fallback "name"
//或
Fallback Off
```

### Unity Shader 的形式

```
Shader "MyShader"{
    Properties {
        //所需的各种属性
    }
    SubShader {
        //真正意义上的Shader代码会出现在这里
        //表面着色器(Surface Shader)或者
        //顶点/片元着色器(Vertex/Fragment Shader)或者
        //固定函数着色器(Fixed Function Shader)
    }
    SubShader {
    //和上一个SubShader类似
    }
}
```

简单来说：主要是通过**IEnumerator**来实现一个**迭代器**，然后分帧执行 **MoveNext()**方法。

https://zhuanlan.zhihu.com/p/375376042
