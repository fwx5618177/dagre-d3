# TodoList
- [ ] [wiki-api 翻译计划](https://github.com/dagrejs/dagre/wiki#browser-scripts)  - 2020/03/16 12:11
- [ ] [graphlib api](https://github.com/dagrejs/graphlib/wiki/API-Reference) - 2020/03/17 10:11

# History
- [X] 翻译 - 2020/03/18 20:44

> 中文 | [English](ReadMe.md)

[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat)](http://choosealicense.com/licenses/mit/)

<h3>目录</h3>

[TOC]

# dagre-d3
[Dagre](https://github.com/dagrejs/dagre)是一个JavaScript库, 可以更轻易得在客户端布局连接图。此库是dagre的前端子库, 底层使用[D3](http://d3js.org)进行渲染。

如果要查看更多详细信息(包含样例和配置在内), 请查看[wiki](https://github.com/dagrejs/dagre-d3/wiki)页面。

# 设计优先级
1. 完全的客户端计算布局。如果客户端布局对你来说并非所需, 可以查看[graphviz](http://www.graphviz.org/), 这是个功能丰富的代替品。

样式举例:
<a href="http://yifanhu.net/GALLERY/GRAPHS/index.html"><img src="http://yifanhu.net/GALLERY/GRAPHS/GIF_SMALL/ACUSIM@Pres_Poisson.gif" width="200" height="100" align="right" alt="graph visualization of matrices from the University of Florida Collection"></a>

2. 速度。Darge为了更快得绘制中等图像, kennel不会采用更合适、准确的算法。
3. 渲染的不可知性。Darge仅需要非常基础的信息就能绘制图像,比如节点的维度。你可以自由地使用任何你喜欢的技术渲染图形。此处推荐学习[d3](https://github.com/d3/d3)。

# 安装
## npm 
在安装这个库之前, 需要向安装[npm](http://npmjs.org/)
然后执行安装:
```shell
$ npm install dagre
```
## Bower
```shell
$ bower install dagre
```

## Browser Scripts
你可以获取到最新版的browser-ready版本文件:
- [dagre.js](https://dagrejs.github.io/project/dagre/latest/dagre.js)
- [dagre.min.js](https://dagrejs.github.io/project/dagre/latest/dagre.min.js)

你也可以指定下载版本, 通过[realease page](https://github.com/dagrejs/dagre/releases)找到所需版本。

## 源代码构建
在构建之前, 需要先下载npm管理器。

在此项目的根目录下执行以下命令:
```shell
$ make dist
```

此命令会在当前项目的dist目录下生成dagre.js和dagre.min.js两个文件。

# 如何使用Darge

## 聚焦渲染
如上文提到, dagre仅聚焦于图像布局。 这意味着你需要使用dagre的布局信息来渲染图像。

这有一些渲染的选项:
- [dagre-d3](https://github.com/dagrejs/dagre-d3)是一个基于d3的dagre渲染器
- [JointJS](https://www.jointjs.com/opensource)是一个渲染器，提供了在渲染后编辑图像的工具
- [Cytoscape.js](http://js.cytoscape.org/)是一个可以使用Dagre作为布局的完整的图像库，支持可视化和分析用例。 Cytoscape.js拥有复杂的渲染流程，由类似于CSS的样式表书写。
<img src="./static/Cytoscape.jpg" width="300" height="200"  />

## 例子
首先，需要在HTML页面中导入Dagre库:

```html
<script src="https://PATH/TO/dagre.min.js"></script>
```

在Node.js中:
```Node
var dagre = require("dagre");
```

使用[graphlib](https://github.com/dagrejs/graphlib)在dagre中创建图像。此处，它的[API](https://github.com/dagrejs/graphlib/wiki/API-Reference)值得一看

每个节点须是一个对象，且有如下属性:
- width: node节点在像素上的宽度
- height: node节点在像素上的高度

这些属性通常来自已确定节点所需空间的渲染引擎。

以下是一个快速制作节点和边的例子:
```js
// 创建一个新的有向图
var g = new dagre.graphlib.Graph();

// 为图像标签设置一个空对象
g.setGraph({});

// 默认为每个边分配一个新的标签对象
g.setDefaultEdgeLabel(function() { return {}; });

// 新增图像的节点
// 第一个参数是节点的id
// 第二个参数是关于节点的元数据
// 在此样例中，我们将为每个节点添加label
g.setNode("kspacey",    { label: "Kevin Spacey",  width: 144, height: 100 });
g.setNode("swilliams",  { label: "Saul Williams", width: 160, height: 100 });
g.setNode("bpitt",      { label: "Brad Pitt",     width: 108, height: 100 });
g.setNode("hford",      { label: "Harrison Ford", width: 168, height: 100 });
g.setNode("lwilson",    { label: "Luke Wilson",   width: 144, height: 100 });
g.setNode("kbacon",     { label: "Kevin Bacon",   width: 121, height: 100 });

// 给图像添加边线
g.setEdge("kspacey",   "swilliams");
g.setEdge("swilliams", "kbacon");
g.setEdge("bpitt",     "kbacon");
g.setEdge("hford",     "lwilson");
g.setEdge("lwilson",   "kbacon");
```

下一步, dagre配置这些节点和边:
```js
dagre.layout(g);
```

图标的布局信息将因配置而更新。节点将会获得以下属性:
- x - 节点中心的x坐标
- y - 节点中心的y坐标

边线获取的'顶点'属性, 其中包括边缘的控制点坐标，以及节点和线相交的点，假设为矩形:
- x - 边线中, 此弯曲部分的中心的x坐标
- y - 边线中, 此弯曲部分的中心的y坐标

为上述的对象生成以下布局:
```js
g.nodes().forEach(v => console.log(`Node: ${v}: ${JSON.stringify(g.node(v))}`));

g.nodes().forEach(e => console.log(`Edge ${e.v} -> ${e.w} : ${JSON.stringify(g.edge(e))}`));
```

打印结果:
```shell
Node kspacey: {"label":"Kevin Spacey","width":144,"height":100,"x":80,"y":50}
Node swilliams: {"label":"Saul Williams","width":160,"height":100,"x":80,"y":200}
Node bpitt: {"label":"Brad Pitt","width":108,"height":100,"x":264,"y":200}
Node hford: {"label":"Harrison Ford","width":168,"height":100,"x":440,"y":50}
Node lwilson: {"label":"Luke Wilson","width":144,"height":100,"x":440,"y":200}
Node kbacon: {"label":"Kevin Bacon","width":121,"height":100,"x":264,"y":350}

Edge kspacey -> swilliams: {"points":[{"x":80,"y":100},{"x":80,"y":125},{"x":80,"y":150}]}
Edge swilliams -> kbacon: {"points":[{"x":80,"y":250},{"x":80,"y":275},{"x":203.5,"y":325.3396739130435}]}
Edge bpitt -> kbacon: {"points":[{"x":264,"y":250},{"x":264,"y":275},{"x":264,"y":300}]}
Edge hford -> lwilson: {"points":[{"x":440,"y":100},{"x":440,"y":125},{"x":440,"y":150}]}
Edge lwilson -> kbacon: {"points":[{"x":440,"y":250},{"x":440,"y":275},{"x":324.5,"y":324.21875}]}
```

# 配置布局
可以通过在图像中的适当对象上设置下表的属性，或在通过第二个参数来设置属性去配置图像的布局。 而通过参数设置优先性更高。

| 对象 | 属性 | 默认值 | 描述 |
| ---- | ---- | ----| ----|
| graph | rankdir | TB | 等级节点的方向。常见的有TB、BT、LR、RL。其中T是顶点(TOP), B是底部(Bottom), L是左边(Left)，R是右边(Right) |
| graph | align | undefined | 节点的对齐方式。有4个值: UL,UR,DL,DR。其中U是上(UP)，D是下(down)，L是左(left)，R是右(Right) |
| graph | nodesep | 50 | 水平方向上, 分隔节点的距离(节点之间的间距) |
| graph | edgesep | 10 | 在水平方向上, 线段间的距离 |
| graph | ranksep | 50 | 每个层级间的距离 |
| graph | marginx | 0 | 图形左右边缘的距离 |
| graph | marginy | 0 | 图形上下边缘的距离 |
| graph | acyclicer | undefined | 如果设置为贪婪模式(greedy), 则使用贪婪启发式来查找. 返回的弧设置是一组可以删除的线, 从而使图无环.  |
| graph | ranker | network-simplex | 在输入的图像中, 为每个节点分配排名的算法类型. 可选值: network-simplex, tight-tree, longest-path |
| node | width | 0 | 节点的宽度(像素) |
| node | height | 0 | 节点的高度(像素) |
| edge | minlen | 1 | 在线的开始和结束之间保持最少的长度 |
| edge | weight | 1 | 线的权重(宽度). 高权重的边通常比低权重的边更短更直 |
| edge | width | 0 | 线的标签的宽度(像素) |
| edge | height | 0 | 线标签的高度(像素) |
| edge | labelpos | r | 根据线的位置来放置标签. l = left, c = center, r= right |
| edge | lableoffset | 10 | 标签和线段之间的距离. 仅当labelpos为l/r时生效 |

# 生成的图像
生成的图像将会有如下属性:
|对象|属性|描述|
|---|---|---|
|graph|height|整个图像的高度|
|graph|width|整个图像的宽度|
|node,edge|x|对节点来说，是节点中心的x坐标. 对边线来说, 是线段标签的x坐标|
|node,edge|y|对节点来说,是节点中心的y坐标. 对边线来说,是线段标签的y坐标|
|edge|points|由{x,y}组成的数组控制线段的控制点|

# 第三部分例

