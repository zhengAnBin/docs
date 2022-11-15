## 前言

ReactDOM 可以理解为是一个的渲染引擎，一个将虚拟 DOM 转换为真实 DOM 的渲染器

```jsx
import ReactDOM from "react-dom";

ReactDOM.render(<div>React App</div>, document.getElementById("root"), () => {
    // 渲染成功 ...
});

// 这段代码将所有的虚拟DOM转换为真实DOM之后，挂载到id为root的DOM元素上
// 成功后调用第三个参数
```

### render

```jsx
function render(element, container, callback) {
    // ...
    // 判断了container是不是一个DOM元素
    // 如果不是DOM元素，抛出错误 "Target container is not a DOM element."

    // 返回了legacyRenderSubtreeIntoContainer函数的调用
    return legacyRenderSubtreeIntoContainer(null, element, container, false, callback);
}

function legacyRenderSubtreeIntoContainer(
    parentComponent,
    children,
    container,
    forceHydrate: boolean,
    callback: ?Function
) {
    let root: RootType = (container._reactRootContainer: any);
    let fiberRoot;
    // 在 container 元素上挂载 _reactRootContainer 可以区分在当前 container 上这是不是第一次渲染虚拟 DOM
    // 第一次需要构造 fiberRoot 对象。所以调用了 legacyCreateRootFromDOMContainer
    if (!root) {
        // Initial mount
        root = container._reactRootContainer = legacyCreateRootFromDOMContainer(
            container,
            forceHydrate
        );
        fiberRoot = root._internalRoot;
        if (typeof callback === "function") {
            const originalCallback = callback;
            callback = function () {
                const instance = getPublicRootInstance(fiberRoot);
                originalCallback.call(instance);
            };
        }
        // Initial mount should not be batched.
        unbatchedUpdates(() => {
            updateContainer(children, fiberRoot, parentComponent, callback);
        });
    } else {
        fiberRoot = root._internalRoot;
        if (typeof callback === "function") {
            const originalCallback = callback;
            callback = function () {
                const instance = getPublicRootInstance(fiberRoot);
                originalCallback.call(instance);
            };
        }
        // Update
        updateContainer(children, fiberRoot, parentComponent, callback);
    }
    // 上面的代码最终都调用 updateContainer 函数

    // 返回 fiberRoot 对象
    return getPublicRootInstance(fiberRoot);
}

// 又分别调用了三个函数的调用
// legacyCreateRootFromDOMContainer 、 updateContainer 、 getPublicRootInstance
// getPublicRootInstance 和 updateContainer 函数属于 react-reconciler 包的内容，这里不展开
```

### fiberRoot 对象

```jsx
// legacyCreateRootFromDOMContainer 函数最终调用了各种函数，创建了 fiberRoot 节点， 这个过程并不重要
// 直接看一个 fiberRoot 节点上包含哪些信息即可

{
    // FiberRootNode
    _internalRoot: {
        callbackNode: null
        callbackPriority: 0
        containerInfo: div#root
        context: {}
        // current 字段里包含海量属性
        // 不过不难看出 current 是个 FiberNode。而且是整棵 Fiber 树的头部
        current: { tag: 3, key: null, elementType: null, type: null, ... },
        entangledLanes: 0
        // ... 省略一些属性
        expiredLanes: 0
        finishedLanes: 0
        finishedWork: null
        hydrate: false
        interactionThreadID: 1
        memoizedInteractions: {size: 0}
        mutableReadLanes: 0
        mutableSourceEagerHydrationData: null
        pendingChildren: null
        pendingContext: null
        pendingInteractionMap: {size: 0}
        pendingLanes: 0
        pingCache: null
        pingedLanes: 0
        suspendedLanes: 0
        tag: 0
        timeoutHandle: -1
        _debugRootType: "createLegacyRoot()"
    }
}

// 展开 current

current: {
    actualDuration: 3.400000002235174
    actualStartTime: 88
    alternate: FiberNode {tag: 3, key: null, elementType: null, type: null, stateNode: FiberRootNode, …}
    child: FiberNode {tag: 8, key: null, elementType: Symbol(react.strict_mode), type: Symbol(react.strict_mode), stateNode: null, …}
    childLanes: 0
    dependencies: null
    elementType: null
    firstEffect: FiberNode {tag: 8, key: null, elementType: Symbol(react.strict_mode), type: Symbol(react.strict_mode), stateNode: null, …}
    flags: 256
    index: 0
    key: null
    lanes: 0
    lastEffect: FiberNode {tag: 8, key: null, elementType: Symbol(react.strict_mode), type: Symbol(react.strict_mode), stateNode: null, …}
    memoizedProps: null
    memoizedState: {element: {…}}
    mode: 8
    nextEffect: null
    pendingProps: null
    ref: null
    return: null
    selfBaseDuration: 0.6999999992549419
    sibling: null
    stateNode: FiberRootNode {tag: 0, containerInfo: div#root, pendingChildren: null, current: FiberNode, pingCache: null, …}
    tag: 3
    treeBaseDuration: 1.6000000014901161
    type: null
    updateQueue: {baseState: {…}, firstBaseUpdate: null, lastBaseUpdate: null, shared: {…}, effects: null}
    _debugHookTypes: null
}
```

从上面的展开的对象属性可以看出 fiberRoot 关联的是 container 节点（真实的 DOM 节点）

而 current 关联的是虚拟 DOM 节点

紧接着就是将 fiberRoot 和调用 ReactDOM.render 时的参数一起传入 updateContainer 方法中

在第一次调用 updateContainer 时，是把该函数做为 unbatchedUpdates 的回调函数。

unbatchedUpdates 函数设置了一些上下文信息，让 React 知道这是初次渲染

[updateContainer(children, fiberRoot, parentComponent, callback)](/react-reconciler)
