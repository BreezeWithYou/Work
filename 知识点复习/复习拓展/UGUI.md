Batching的生成和合并在Canvas：：Update里：

***\*Batching主要流程如下:\****

1. 计算Canvas alpha。
2. UI层次结构发生变化时，更新Batch顺序，对Canvas下所有UI元素（CanvasRenderer）按UI层次结构深度优先排序，生成UI Instructions。
3. 更新所有需要同步数据的renderer UI 数据，包括vertex, color, material, transform, rect, depth（按UI层次结构深度优先排序的深度）等。非活动（IsActive() == false）且不强制更新的UI元素，将不同步数据。
4. Canvas数据更新时（m_CanvasData.isDirty，如情况都可以引发：层次结构改变，同步关键数据，Canvas.Awake等），计算UI Instructions的depth并排序、生成Batch。



**Depth计算算法：**

- 遍历所有UI元素（已深度优先排序），对当前每一个UI元素CurrentUI，如果不渲染，CurrentUI.depth = -1，如果渲染该UI且底下没有其他UI元素与其相交（rect Intersects），其CurrentUI.depth = 0;
- 如果CurrentUI下面只有一个的需要渲染的UI元素LowerUI与其相交，且可以Batch（material instance id 和 texture instance id 相同，即与CurrentUI具有相同的Material和Texture），CurrentUI.depth = LowerUI.depth；否则，CurrentUI.depth= LowerUI.depth + 1;
- 如果CurrentUI下面叠了多个元素，这些元素的最大层是MaxLowerDepth，如果有多个元素的层都是MaxLowerDepth，那么CurrentUI和下面的元素是无法合批的；如果只有一个元素的层是MaxLowerDepth，并且这个元素和CurrentUI的材质、纹理相同，那么它们就能合批。

**DrawCall合批(Batch)：**

- Depth计算完后，依次根据Depth、material ID、texture ID、RendererOrder（即UI层级队列顺序，HierarchyOrder）排序（条件的优先级依次递减），剔除depth == -1的UI元素，得到Batch前的UI 元素队列VisiableList。
- 对VisiableList中相邻且可以Batch（相同material和texture等）的UI元素合并批次，然后再生成相应mesh数据进行绘制。



https://www.cnblogs.com/llstart-new0201/p/12630912.html
