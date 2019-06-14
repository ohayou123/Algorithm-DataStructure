
## 原理
A星算法擅长解决方块类路径的寻路问题，以起始点的方块为起点，向该方块周围的方块进行探测&计算，以（为该起点周围需要探测的每个砖块赋予数据，并对数据进行比较，然后确认下一个可能移动的砖块，并以它作为下一个起始点，重复以上操作）为原理，直到找到路径终点为止，并通过路径之间的类似链表结构，从终点方块遍历到起点方块，并记录中途所有路径方块，然后让寻路者沿着记录的路径的所有方块逐一移动，直到终点为止。

## 搜索区域(The Search Area)
我们假设某人要从 A 点移动到 B 点，但是这两点之间被一堵墙隔开。如图 1 ，绿色是 A ，红色是 B ，中间蓝色是墙。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs20190611185142.png)

把要搜寻的区域划分成了正方形的格子。这是寻路的第一步，简化搜索区域，就像我们这里做的一样。这个特殊的方法把我们的搜索区域简化为了 2 维数组。数组的每一项代表一个格子，它的状态就是可走 (walkalbe) 和不可走 (unwalkable) 。通过计算出从 A 到 B需要走过哪些方格，就找到了路径。一旦路径找到了，人物便从一个方格的中心移动到另一个方格的中心，直至到达目的地。

方格的中心点我们成为“节点 (nodes) ”。如果你读过其他关于 A* 寻路算法的文章，你会发现人们常常都在讨论节点。为什么不直接描述为方格呢？因为我们有可能把搜索区域划为为其他多变形而不是正方形，例如可以是六边形，矩形，甚至可以是任意多变形。而节点可以放在任意多边形里面，可以放在多变形的中心，也可以放在多边形的边上。我们使用这个系统，因为它最简单。

每个格子设置三个属性 F,G,H 其中：F=G+H
- H表示到目的的最短距离（不考虑屏障）
- G表示到目的的距离
```
class Pos{
    int F,G,H;
}
```
比如有7*5的格子
```
Pos[][] path = new Pos[5][7];
```

维持两个队列： 

```
OpenList:
CloseList:
```
![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs20190611185341.png)

首先找出当前格子上下左右所有可达性的格子，并且计算其F的值。 加入OpenList,然后从OpenList中选择最短的格子放入CloseList代表走到这个格子。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs20190611185806.png)

到这个格子以后，计算周围格子的F值，并且加入OpenList，重复之前的计算，（如果OpenList中最小值重复，则随机选择，选择第一个也行）。一直迭代到最后。
![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs20190611185925.png)

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs20190611185942.png)

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs20190611185951.png)

...

## 图解
![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs20190611190021.png)

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs20190611190035.png)

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs20190611190046.png)

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs20190611190057.png)

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs20190611190109.png)

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs20190611190121.png)

## 伪代码

```
public Node aStarSearch(Node start, Node end) {
    // 把起点加入 open list  
    openList.add(start);
    //主循环，每一轮检查一个当前方格节点
    while (openList.size() > 0) {
        // 在OpenList中查找 F值最小的节点作为当前方格节点
        Node current = findMinNode();
        // 当前方格节点从open list中移除
        openList.remove(current);
        // 当前方格节点进入 close list
        closeList.add(current);
        // 找到所有邻近节点
        List<Node> neighbors = findNeighbors(current);
        for (Node node : neighbors) {
            if (!openList.contains(node)) {
                //邻近节点不在OpenList中，标记父亲、G、H、F，并放入OpenList
                markAndInvolve(current, end, node);
            }
        }
        //如果终点在OpenList中，直接返回终点格子
        if (find(openList, end) != null) {
            return find(openList, end);
        }
    }
    //OpenList用尽，仍然找不到终点，说明终点不可到达，返回空
    return null;
}
```
