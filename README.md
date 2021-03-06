topology
========

画拓扑图

核心流程
--------

#### 数据获取

- 输入：islandId 和 当前url.  
    根据当前URL判断是生产环境/测试环境，拓扑图的类型为设备拓扑/slice拓扑。  
    根据拓扑类型判断所需的节点类型。  
    生产环境使用 AJAX，测试环境除 AJAX，也可以直接读取本地文件。  
    根据 islandId 取数据。
- 输出：图的节点和节点间连接关系。  
    节点和连接各种一个 dict 存储，key 为节点/连接类型。  
    节点的 value 为 Object, key 存id, value 存 详细信息string。  
    连接的 value 为集合，集合的每一个元素是[from, to],

#### 生成树。

- 输入: 图，包含节点和连接信息。
- 输出：各类节点的分层关系。

#### 计算节点坐标

如果存在多颗树，根据树的最大宽度为每颗树分配画布的宽度。

计算没层节点坐标时，n 节点的算法如下：画布等分为 (n + 1) 份。左右各半份空白空间。
第 i 个节点的坐标为 每份的宽度 * i。将该坐标作为节点的中点。

若树的宽度小于单节点图片的宽度，树与树之间存在交叠。

#### 生成 html。

- 输入: 节点坐标，节点的长宽。
- 输出：html

线的坐标，根据节点坐标自动计算。

尺寸计算
------------

#### 计算原则

1. 理想情况下，可以在一个屏幕内看到完整的拓扑图，不管设备节点的数量有多少。
2. 如果节点数量较少，则缩小画布大小以适应拓扑图，避免上下留白。
3. 如果节点数量过多，无法用最佳尺寸在一个屏幕内展示完整图片，则压缩尺寸。
4. 如果尺寸压缩到最小的极限长度，则调整画布大小，支持鼠标滚动来看到完整的拓扑图。

#### 参数确定

- 屏幕大小: 900px * 540px (5:3)
- 图片大小: 120px * 120px (1:1)
- 行间距: 1 倍图片高度
- 行内节点最小间距: 0
- 行内节点最大间距: 1 倍图片距离

根据节点坐标和边信息生成 html
-----------------------------

#### 基本功能

1. 根据节点类型、id、坐标、描述信息生成节点的 html 代码。
2. 根据边的端点坐标，生成所有边的 html 代码。

#### 影响因子

1. 单因子

    - 节点: 类型、数量、每类节点的数量,
    - 边: 数量, 边的节点在 nodes 中存在/不存在。

2. 因子组合

    - 重复边: 不同类型的节点中包含重复 id
    - 重复节点
    - 边的端点是否全部包含在节点的数据结构中

#### 测试数据

节点类型使用：flowvisor，ovs，host。

节点坐标，为了方便测试数据的重用，节点的坐标遵循如下规则：
每层放一类设备，按照从左至右的顺序依次排列。
节点的Id，以设备类型首字母开始，后跟序列号。每一类设备从 001 开始编号。

具体用例见单元测试代码。
