### useMemo

在组件的顶层调用`useMemo`来缓存每次重新渲染都需要计算的结果.

你可以仅仅依赖 useMemo 作为性能优化手段。否则，使用 state 变量 或者 ref 可能更加合适

```JavaScript
  const cachedValue = useMemo(calculateValue, dependencies)

  // 在初次渲染时，useMemo 返回不带参数调用 calculateValue 的结果。
  // 在接下来的渲染中，如果依赖项没有发生改变，它将返回上次缓存的值；否则将再次调用 calculateValue，并返回最新结果。
  // ex: 在下方代码示例中，calculateValue是第一个()=>{}, dependencies是[props.dataSources]
  // 在严格模式下，为了 帮你发现意外的错误，React 将会 调用你的计算函数两次。这只是一个开发环境下的行为，并不会影响到生产环境。
  // 如果计算函数是一个纯函数（它本来就应该是），这将不会影响到代码逻辑。其中一次的调用结果将被忽略。
  const dataSources = useMemo(() => {
    const sources = [
      new Ros1LocalBagDataSourceFactory(),
      new Ros2LocalBagDataSourceFactory(),
      new FoxgloveWebSocketDataSourceFactory(),
      new RosbridgeDataSourceFactory(),
      new UlogLocalDataSourceFactory(),
      new SampleNuscenesDataSourceFactory(),
      new McapLocalDataSourceFactory(),
      new RemoteDataSourceFactory(),
    ];

    return props.dataSources ?? sources;
  }, [props.dataSources]);

```

### useContext

`useContext`是一个`React Hook` ，可以让你读取和订阅组件中的`context`（全局共享数据容器）。

```TypeScript
import { createContext, useContext } from "react";

// Example 创建一个 Context 对象
const SharedRootContext = createContext<ISharedRootContext>({
  deepLinks: [],
  dataSources: [],
  extensionLoaders: [],
});

// SharedRootContext
//  ├── Provider   (提供数据)
//  └── Consumer   (读取数据)

// 组件只使用useSharedRootContext，不直接接触Context。
export function useSharedRootContext(): ISharedRootContext {
  return useContext(SharedRootContext);
}

// 通常在最外层使用
<SharedRootContext.Provider
  value={{
    deepLinks,
    dataSources,
    extensionLoaders,
  }}
>
  <App />
</SharedRootContext.Provider>
```



