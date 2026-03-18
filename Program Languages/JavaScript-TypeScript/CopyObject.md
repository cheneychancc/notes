在 JavaScript 中复制对象的所有常用做法，其核心可以分为**浅拷贝**（只复制表层属性，嵌套对象共享引用）和**深拷贝**（复制所有层级，嵌套对象也完全独立）两大类，下面把每种做法讲清楚，包括用法、优缺点和适用场景。

### 一、浅拷贝（适合无嵌套/仅需表层复制的对象）
浅拷贝的核心是：只复制对象的第一层属性，如果属性值是对象/数组，复制的是**引用**（修改新对象的嵌套属性会影响原对象）。

#### 1. Object.assign()（最常用）
- **用法**：以空对象为目标，源对象为要复制的对象。
- **代码示例**：
  ```javascript
  const original = { a: 1, b: { c: 2 } };
  const copy = Object.assign({}, original);

  copy.a = 10; // 不影响原对象
  copy.b.c = 20; // 影响原对象（嵌套对象是引用）
  console.log(original.a); // 1
  console.log(original.b.c); // 20
  ```
- **优点**：语法简洁，兼容性好（ES6+）。
- **缺点**：只能浅拷贝，无法处理嵌套对象。

#### 2. 扩展运算符 `...`（最简洁）
- **用法**：用扩展运算符展开原对象，构建新对象。
- **代码示例**：
  ```javascript
  const original = { a: 1, b: { c: 2 } };
  const copy = { ...original };

  copy.a = 10; // 不影响原对象
  copy.b.c = 20; // 影响原对象
  console.log(original.b.c); // 20
  ```
- **优点**：写法最简洁，语义清晰。
- **缺点**：同样是浅拷贝，ES6 语法（需环境支持）。

#### 3. Object.create()（原型继承式拷贝）
- **用法**：基于原对象的原型创建新对象，再复制属性。
- **代码示例**：
  ```javascript
  const original = { a: 1, b: { c: 2 } };
  const copy = Object.create(Object.getPrototypeOf(original));
  Object.assign(copy, original); // 结合assign复制属性
  ```
- **优点**：保留原对象的原型链。
- **缺点**：步骤繁琐，仍为浅拷贝，极少单独用。

#### 4. for...in 循环（手动遍历）
- **用法**：手动遍历原对象的可枚举属性，逐个赋值给新对象。
- **代码示例**：
  ```javascript
  const original = { a: 1, b: { c: 2 } };
  const copy = {};
  for (let key in original) {
    if (original.hasOwnProperty(key)) { // 避免复制原型链属性
      copy[key] = original[key];
    }
  }
  ```
- **优点**：兼容性极好（支持老旧浏览器），可自定义复制逻辑。
- **缺点**：代码冗余，浅拷贝，需手动过滤原型属性。

### 二、深拷贝（适合有嵌套对象/需要完全独立的对象）
深拷贝的核心是：复制对象的所有层级，新对象和原对象完全独立，修改嵌套属性互不影响。

#### 1. JSON.parse(JSON.stringify())（最常用的简易深拷贝）
- **用法**：先把对象转为 JSON 字符串，再解析成新对象。
- **代码示例**：
  ```javascript
  const original = { a: 1, b: { c: 2 } };
  const copy = JSON.parse(JSON.stringify(original));

  copy.b.c = 20; // 不影响原对象
  console.log(original.b.c); // 2
  ```
- **优点**：写法简单，无需额外依赖。
- **缺点**：有局限性，无法复制：
  - 函数、undefined、Symbol 类型（会被忽略）；
  - 循环引用的对象（会报错）；
  - Date 类型（会转为字符串）、RegExp 类型（会转为空对象）。

#### 2. 手动递归深拷贝（自定义可控）
- **用法**：写递归函数，遍历对象的每一层，逐个复制（包括嵌套对象）。
- **代码示例**：
  ```javascript
  function deepClone(obj) {
    // 处理非对象/空值
    if (obj === null || typeof obj !== 'object') return obj;
    // 处理数组
    if (obj instanceof Array) {
      const arrCopy = [];
      for (let i = 0; i < obj.length; i++) {
        arrCopy[i] = deepClone(obj[i]);
      }
      return arrCopy;
    }
    // 处理普通对象
    if (obj instanceof Object) {
      const objCopy = {};
      for (let key in obj) {
        if (obj.hasOwnProperty(key)) {
          objCopy[key] = deepClone(obj[key]);
        }
      }
      return objCopy;
    }
  }

  // 测试
  const original = { a: 1, b: { c: 2 }, d: [3, 4] };
  const copy = deepClone(original);
  copy.b.c = 20;
  copy.d[0] = 30;
  console.log(original.b.c); // 2（不影响）
  console.log(original.d[0]); // 3（不影响）
  ```
- **优点**：完全自定义，可处理数组/嵌套对象，无 JSON 方法的局限性（可扩展支持函数、Date 等）。
- **缺点**：需自己写代码，复杂场景（如循环引用、特殊类型）需额外处理。

#### 3. 第三方库（专业稳定）
- **用法**：使用成熟的库（如 Lodash 的 `_.cloneDeep`），处理所有边界情况。
- **代码示例**（需先安装 Lodash）：
  ```bash
  # 安装 Lodash
  npm install lodash
  ```
  ```javascript
  const _ = require('lodash');

  const original = { a: 1, b: { c: 2 }, fn: () => console.log('hello') };
  const copy = _.cloneDeep(original);

  copy.b.c = 20;
  copy.fn(); // 正常执行，不影响原对象
  console.log(original.b.c); // 2
  ```
- **优点**：专业、稳定，支持所有类型（函数、循环引用、Date、RegExp 等），无需自己处理边界。
- **缺点**：需引入第三方库（若项目已用 Lodash 则无成本）。

### 三、特殊场景：处理循环引用的深拷贝
如果对象存在循环引用（如 `obj.self = obj`），JSON 方法会报错，手动递归也会栈溢出，可通过 `WeakMap` 缓存已复制的对象解决：
```javascript
function deepCloneWithCycle(obj, cache = new WeakMap()) {
  if (obj === null || typeof obj !== 'object') return obj;
  // 缓存已复制的对象，避免循环引用
  if (cache.has(obj)) return cache.get(obj);

  let copy;
  if (obj instanceof Array) {
    copy = [];
    cache.set(obj, copy);
    for (let i = 0; i < obj.length; i++) {
      copy[i] = deepCloneWithCycle(obj[i], cache);
    }
  } else {
    copy = {};
    cache.set(obj, copy);
    for (let key in obj) {
      if (obj.hasOwnProperty(key)) {
        copy[key] = deepCloneWithCycle(obj[key], cache);
      }
    }
  }
  return copy;
}

// 测试循环引用
const original = { a: 1 };
original.self = original; // 循环引用
const copy = deepCloneWithCycle(original);
console.log(copy.self === copy); // true（正确复制，不报错）
```

### 总结
1. **浅拷贝优先选**：扩展运算符 `{ ...obj }`（简洁）或 `Object.assign({}, obj)`（兼容性好），适合无嵌套的简单对象。
2. **深拷贝优先选**：
   - 简单场景（无函数/循环引用）：`JSON.parse(JSON.stringify(obj))`；
   - 复杂场景（有函数/循环引用/特殊类型）：Lodash 的 `_.cloneDeep`（推荐）或自定义递归深拷贝。
3. 核心区别：浅拷贝只复制表层属性，嵌套对象共享引用；深拷贝复制所有层级，新老对象完全独立。
