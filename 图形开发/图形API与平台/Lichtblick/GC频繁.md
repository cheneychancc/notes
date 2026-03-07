## GC频繁导致渲染卡顿掉帧

### GC
> GC: 垃圾回收（Garbage Collection）

GC不是免费操作。

垃圾回收时会：
```
扫描内存
找到无用对象
释放内存

这个过程会暂停JS执行，导致FPS掉帧，画面卡顿
```

### 原因

```
假设在代码每一帧都创建对象
function renderFrame(points) {
  const positions = new Float32Array(points.length * 3)
}
假设：10FPS
每秒会创建：10 个 Float32Array
点云：200K点
数组大小：200K * 3 * 4 byte 约等于 2.4MB
每秒创建：24MB
这些数组很快就会变成垃圾对象
```

### 图形工程常见优化思路

```
尽量避免运行时内存分配

初始化时创建
运行时复用

例如：
const positions = new Float32Array(MAX_POINTS * 3)

function update(points){
  positions.set(points)
}

这样不会反复创建对象

GC几乎不会触发

对C++来说，内存是手动管理，不会突然GC，所以实时渲染更稳定。

```
