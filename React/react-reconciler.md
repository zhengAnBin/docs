## 前言

> 在找到 react-reconciler 的入口文件时(ReactFiberReconciler)发现和入口文件里的函数是引用了和入口文件同名且带有 old 和 new 的另外两个文件，而且大多数的文件都此特征。
> 这看起来像是 React 在开发一些新特性时为了不影响旧代码所使用的模式。
> 而决定 ReactFiberReconciler 使用 new 或 old 是被 shared 包里的 ReactFeatureFlags 文件中的 enableNewReconciler 变量控制着，默认等于 false。所以 React 默认使用的是 old 里的代码

### updateContainer

```jsx
// ReactFiberReconciler.old.js
export function updateContainer(
    element: ReactNodeList,
    container: OpaqueRoot,
    parentComponent: ?React$Component<any, any>,
    callback: ?Function
): Lane {
    // element 是虚拟 DOM
    // current 就是整棵 Fiber 树
    // parentComponent 实际上是 null

    const current = container.current;
    const eventTime = requestEventTime();

    // lane 表示优先级
    const lane = requestUpdateLane(current);

    /**
     * 此处省略一部分代码 ......
     */

    // eventTime 结合 lane 创建一个 update 对象
    // 一个 update 对象意味着一个更新
    const update = createUpdate(eventTime, lane);
    // Caution: React DevTools currently depends on this property
    // being called "element".
    update.payload = { element };

    // 处理callback
    callback = callback === undefined ? null : callback;
    if (callback !== null) {
        update.callback = callback;
    }

    // 将 update 入队
    enqueueUpdate(current, update);
    scheduleUpdateOnFiber(current, lane, eventTime);

    // 返回优先级
    return lane;
}
```

捋一捋一 updateContainer 的逻辑

-   创建 Fiber 的 lane (优先级)
-   结合 lane 创建 Fiber 的 update 对象，并将其入队
-   调度 scheduleUpdateOnFiber

### scheduleUpdateOnFiber

```jsx
export function scheduleUpdateOnFiber(fiber: Fiber, lane: Lane, eventTime: number) {
    // ...

    // 在第一次调用ReactDOM.render的链路中 这里的判断结果会是true
    if (lane === SyncLane) {
        // & 操作符
        // 这里是一种位运算技巧，判断二进制的最后一位是否相同
        // 如果相同说当前处于该状态中
        if (
            // Check if we're inside unbatchedUpdates
            (executionContext & LegacyUnbatchedContext) !== NoContext &&
            // Check if we're not already rendering
            (executionContext & (RenderContext | CommitContext)) === NoContext
        ) {
            // Register pending interactions on the root to avoid losing traced interaction data.
            schedulePendingInteractions(root, lane);

            // This is a legacy edge case. The initial mount of a ReactDOM.render-ed
            // root inside of batchedUpdates should be synchronous, but layout updates
            // should be deferred until the end of the batch.
            // 重点是此函数 ！
            // 进入 render 阶段
            performSyncWorkOnRoot(root);
        } else {
            ensureRootIsScheduled(root, eventTime);
            schedulePendingInteractions(root, lane);
            if (executionContext === NoContext) {
                // Flush the synchronous work now, unless we're already working or inside
                // a batch. This is intentionally inside scheduleUpdateOnFiber instead of
                // scheduleCallbackForFiber to preserve the ability to schedule a callback
                // without immediately flushing it. We only do this for user-initiated
                // updates, to preserve historical behavior of legacy mode.
                resetRenderTimer();
                flushSyncCallbackQueue();
            }
        }
    } else {
        // ...
        // 异步更新的逻辑处理
    }

    // We use this when assigning a lane for a transition inside
    // `requestUpdateLane`. We assume it's the same as the root being updated,
    // since in the common case of a single root app it probably is. If it's not
    // the same root, then it's not a huge deal, we just might batch more stuff
    // together more than necessary.
    mostRecentlyUpdatedRoot = root;
}

// 有趣的是，这个函数里的所有标识符（即变量名，函数名）都可以直接翻译而得知它的作用
// 该函数处理了一些优先级、打断相关的逻辑。但在 ReactDOM.render 第一次调用过程中意义不大。
// 函数调用 performSyncWorkOnRoot 函数。它是整个链路中最核心的一环，标记着React正式进入渲染
```

从 ReactDOM.render 到 performSyncWorkOnRoot 函数调用之前，都是在初始化一些上下文，这些上下文决定 React 是否开启异步渲染，是否为首次渲染等

ReactDOM.render 方法渲染模式是同步的，这并没有发挥 Fiber 的优势（异步渲染），可以将 ReactDOM.render 调用方式替换为 ReactDOM.createRoot 让 React 发挥 Fiber 优势

先顺着同步模式的调用栈去认识 React 的整个流程[（首次渲染的调用栈）](/React/react-dom-render.md)。

同步渲染：legacy 模式

异步渲染：concurrent 模式
