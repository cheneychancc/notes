**三维八叉树（Octree）**是一种用于**三维空间划分的树形数据结构**，是二维 **四叉树（Quadtree）** 在三维空间的扩展。它广泛用于 **3D 图形、游戏引擎、点云处理、自动驾驶、物理碰撞检测** 等领域。

---

# 一、什么是三维八叉树

八叉树的核心思想是：

> **把三维空间递归划分成 8 个子立方体**

每个节点代表一个 **立方体空间**，当这个空间中的数据过多时，就把它再分成 **8 个更小的立方体**。

结构类似：

```
            Root
        / / / / / / / /
       8 children
```

三维空间划分：

```
        +-------+
       /|      /|
      / |     / |
     +-------+  |
     |  +----|--+
     | /     | /
     |/      |/
     +-------+
```

沿着 **X Y Z 三个轴**切一刀：

```
2 × 2 × 2 = 8
```

所以得到 **8 个子空间**。

---

# 二、八叉树的数据结构

典型结构：

```cpp
struct OctreeNode {
    BoundingBox bounds;       // 当前空间范围
    std::vector<Object*> objects; // 当前节点存储的物体
    OctreeNode* children[8];  // 八个子节点
    bool isLeaf;              // 是否叶子节点
};
```

核心信息：

| 字段       | 作用      |
| -------- | ------- |
| bounds   | 当前立方体空间 |
| objects  | 该空间中的对象 |
| children | 8个子空间   |
| isLeaf   | 是否继续划分  |

---

# 三、八叉树构建算法

### Step1 插入对象

当向八叉树插入物体：

```
insert(object)
```

流程：

```
1 判断当前节点是否叶子
2 如果对象数量 < 阈值
      存储在当前节点
3 如果超过阈值
      subdivide() 分裂节点
      将对象重新分配到子节点
```

---

### Step2 空间划分

假设当前节点空间：

```
(x,y,z) -> (x+w,y+h,z+d)
```

划分为 8 个：

```
child0 (左下前)
child1 (右下前)
child2 (左上前)
child3 (右上前)

child4 (左下后)
child5 (右下后)
child6 (左上后)
child7 (右上后)
```

代码示意：

```cpp
void subdivide(Node* node) {
    for (int i = 0; i < 8; i++) {
        node->children[i] = new Node();
    }
}
```

---

# 四、八叉树查询

常见查询：

### 1 空间范围查询

例如：

```
查询某个立方体范围内的物体
```

算法：

```
if (queryBox 与 node.bounds 不相交)
    return

检查 node.objects

递归查询 children
```

复杂度：

```
O(log N)
```

远优于：

```
O(N)
```

---

### 2 最近邻查询

常见于：

* 点云
* 自动驾驶
* 3D扫描

```
findNearest(point)
```

方法：

```
1 找到点所在节点
2 在附近节点搜索
3 逐层扩大范围
```

---

# 五、八叉树 vs KD Tree vs BVH

| 结构      | 空间划分    | 适合   |
| ------- | ------- | ---- |
| Octree  | 固定 8 分割 | 3D空间 |
| KD-tree | 按轴切分    | 最近邻  |
| BVH     | 包围盒     | 光线追踪 |

总结：

```
Octree = 空间划分
BVH    = 物体划分
```

---

# 六、时间复杂度

构建：

```
O(N log N)
```

查询：

```
O(log N)
```

空间复杂度：

```
O(N)
```

---

# 七、应用场景（非常重要）

## 1 3D 引擎空间管理

游戏引擎常用：

* 物体管理
* 场景分区

例如：

* Unity
* Unreal Engine

应用：

```
只渲染玩家附近物体
```

减少：

```
Draw Call
```

---

## 2 碰撞检测（Collision Detection）

如果场景有：

```
10000个物体
```

暴力检测：

```
10000²
```

使用 Octree：

```
只检测同一空间物体
```

复杂度：

```
接近 O(N log N)
```

---

## 3 自动驾驶点云处理

自动驾驶中常用：

* 点云索引
* 空间查询

例如：

LiDAR点云：

```
1 frame = 100万点
```

Octree可以：

* 空间分区
* 快速邻域搜索
* 体素化

常见于：

* PCL
* ROS

---

## 4 三维可视化（点云 / 地图）

例如：

* 自动驾驶地图
* 城市三维扫描
* 数字孪生

八叉树用于：

```
LOD (Level of Detail)
```

远处：

```
低精度
```

近处：

```
高精度
```

---

## 5 光线追踪（Ray Tracing）

加速：

```
Ray -> Scene intersection
```

例如：

* Blender
* OptiX

通过 Octree：

```
只检测可能相交的物体
```

---

# 八、一个简单例子（点云）

假设有点云：

```
1000000 points
```

如果查找：

```
半径 1m 内的点
```

暴力：

```
1000000 次
```

Octree：

```
只查 10~100 个节点
```

速度提升：

```
1000x
```

---

# 九、简单 C++ 示例

```cpp
class Octree {
public:
    BoundingBox bounds;
    vector<Point> points;
    Octree* children[8];

    void insert(Point p) {
        if (!bounds.contains(p)) return;

        if (points.size() < 10) {
            points.push_back(p);
            return;
        }

        if (children[0] == nullptr)
            subdivide();

        for (int i = 0; i < 8; i++)
            children[i]->insert(p);
    }
};
```

---

# 十、在你做的自动驾驶可视化里（重点）

你之前做：

* **3D Box 投影**
* **点云渲染**
* **Three.js**

Octree可以用在：

### 1 点云渲染优化

```
100万点
```

使用 Octree：

```
只渲染视锥内节点
```

叫：

```
Frustum Culling
```

---

### 2 点选检测

点击一个点：

```
Raycaster -> Octree
```

不用检测：

```
100万点
```

只检测：

```
几十个节点
```

---

### 3 LOD 点云

远处点云：

```
1/8采样
```

近处：

```
全部渲染
```

---

# 十一、八叉树结构图（非常直观）

```
Level0
 └── Root

Level1
 ├── child0
 ├── child1
 ├── child2
 ├── child3
 ├── child4
 ├── child5
 ├── child6
 └── child7

Level2
 每个child再分8个
```

节点数：

```
1 + 8 + 64 + 512 ...
```

---

如果你愿意，我可以再给你讲一个 **自动驾驶 / 点云系统里的 Octree + LOD 渲染完整架构**，那一套其实是 **很多自动驾驶可视化平台（例如 Foxglove 类工具）背后的核心优化方案**。
我也可以顺便讲 **Three.js 里如何实现 Octree 点云加速**（很多人面试会问）。
