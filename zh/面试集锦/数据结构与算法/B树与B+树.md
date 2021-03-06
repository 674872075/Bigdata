## 一，b树

b树（balance tree）和b+树应用在数据库索引，可以认为是m叉的多路平衡查找树，但是从理论上讲，二叉树查找速度和比较次数都是最小的，为什么不用二叉树呢？ 
因为我们要考虑磁盘IO的影响，它相对于内存来说是很慢的。数据库索引是存储在磁盘上的，当数据量大时，就不能把整个索引全部加载到内存了，只能逐一加载每一个磁盘页（对应索引树的节点）。所以我们要减少IO次数，对于树来说，IO次数就是树的高度，而“矮胖”就是b树的特征之一，它的每个节点最多包含m个孩子，m称为b树的阶，m的大小取决于磁盘页的大小。

**关键字：一个节点中存储的信息数量**

**[]表示向上取整**

**M阶  [m/2]-1<=关键字<=m-1**

█一个M阶的b树具有如下几个特征：

1. 定义任意非叶子结点最多只有M个儿子，且M>2；
2. 根结点的儿子数为[2, M]；
3. 除根结点以外的非叶子结点的儿子数为[M/2, M]，向上取整；
4. 非叶子结点的关键字个数=儿子数-1；
5. 所有叶子结点位于同一层；
6. k个关键字把节点拆成k+1段，分别指向k+1个儿子，同时满足查找树的大小关系。

█有关b树的一些特性，注意与后面的b+树区分：

1. 关键字集合分布在整颗树中；
2. 任何一个关键字出现且只出现在一个结点中；
3. 搜索有可能在非叶子结点结束；
4. 其搜索性能等价于在关键字全集内做一次二分查找；

█如图是一个3阶b树，顺便讲一下查询元素5的过程： 
![2](assert\2.png) 
1，第一次磁盘IO，把9所在节点读到内存，把目标数5和9比较，小，找小于9对应的节点；

![3](assert\3.png) 
2，第二次磁盘IO，还是读节点到内存，在内存中把5依次和2、6比较，定位到2、6中间区域对应的节点； 
3，第三次磁盘IO就不上图了，跟第二步一样，然后就找到了目标5。

可以看到，b树在查询时的比较次数并不比二叉树少，尤其是节点中的数非常多时，但是内存的比较速度非常快，耗时可以忽略，所以只要树的高度低，IO少，就可以提高查询性能，这是b树的优势之一。

█b树的插入删除元素操作： 
比如我们要在下图中插入元素4： 
![4](assert\4.png) 
1，首先自顶向下查询找到4应该在的位置，即3、5之间； 
2，但是3阶b树的节点最多只能有2个元素，所以把3、4、5里面的中间元素4上移（中间元素上移是插入操作的关键）； 
3，上一层节点加入4之后也超载了，继续中间元素上移的操作，现在根节点变成了4、9； 
4，还要满足查找树的性质，所以对元素进行调整以满足大小关系，始终维持多路平衡也是b树的优势，最后变成这样： 
![5](assert\5.png) 
再比如我们要删除元素11： 
1，自顶向下查询到11，删掉它； 
2，然后不满足b树的条件了，因为元素12所在的节点只有一个孩子了，所以我们要“左旋”，元素12下来，元素13上去： 
![6](assert\6.png) 
这时如果再删除15呢？很简单，当元素个数太少以至于不能再旋转时，12直接上去就行了。

## 二，b+树

b+树，是b树的一种变体，查询性能更好。m阶的b+树的特征：

**M阶  [m/2]<=关键字<=m**

1. 有n棵子树的非叶子结点中含有n个关键字（b树是n-1个），这些关键字不保存数据，只用来索引，所有数据都保存在叶子节点（b树是每个关键字都保存数据）。
2. 所有的叶子结点中包含了全部关键字的信息，及指向含这些关键字记录的指针，且叶子结点本身依关键字的大小自小而大顺序链接。
3. 所有的非叶子结点可以看成是索引部分，结点中仅含其子树中的最大（或最小）关键字。
4. 通常在b+树上有两个头指针，一个指向根结点，一个指向关键字最小的叶子结点。
5. 同一个数字会在不同节点中重复出现，根节点的最大元素就是b+树的最大元素。

![7](assert\7.png)

█b+树相比于b树的查询优势：

1. b+树的中间节点不保存数据，所以磁盘页能容纳更多节点元素，更“矮胖”；

2. b+树查询必须查找到叶子节点，b树只要匹配到即可不用管元素位置，因此b+树查找更稳定（并不慢）；

3. 对于范围查找来说，b+树只需遍历叶子节点链表即可，b树却需要重复地中序遍历，如下两图：

   ![8](assert\8.png)

   总结：	

   ​	B树又称为B-树,每个节点都存储信息。B+树所有非叶子结点仅仅起到索引的作用，非叶子节点中的每个索引项只含有对应子树的最大关键字和指向该子树的指针,叶子结点存储的是指向对应行记录的存储地址，每个节点对应一个记录的存储地址

   ​        在B+树中，叶节点包含了全部关键字，即在非叶子节点中出现的关键字也会出现在叶子结点中，而且叶子结点的指针指向记录；而在B树中，叶子结点包含的关键字和其他节点包含的关键字是不重复的

   ​		在B+树中，有一个指针指向关键字最小的叶子结点，所有的叶子结点链接成一个单链表

[B树和B+树]: https://www.cnblogs.com/nullzx/p/8729425.html	"B树和B+树的插入、删除图文详解"

