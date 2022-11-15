### beginWork

```js
function beginWork(current: Fiber | null, workInProgress: Fiber, renderLanes: Lanes): Fiber | null {
    const updateLanes = workInProgress.lanes;

    if (current !== null) {
        const oldProps = current.memoizedProps;
        const newProps = workInProgress.pendingProps;

        // 如果 props 发生改变，触发更新
        if (
            oldProps !== newProps ||
            hasLegacyContextChanged() ||
            // Force a re-render if the implementation changed due to hot reload:
            (__DEV__ ? workInProgress.type !== current.type : false)
        ) {
            // If props or context changed, mark the fiber as having performed work.
            // This may be unset if the props are determined to be equal later (memo).
            didReceiveUpdate = true;
        } else if (!includesSomeLane(renderLanes, updateLanes)) {
            didReceiveUpdate = false;
            // This fiber does not have any pending work. Bailout without entering
            // the begin phase. There's still some bookkeeping we that needs to be done
            // in this optimized path, mostly pushing stuff onto the stack.

            // ...

            return bailoutOnAlreadyFinishedWork(current, workInProgress, renderLanes);
        } else {
            if ((current.flags & ForceUpdateForLegacySuspense) !== NoFlags) {
                // This is a special case that only exists for legacy mode.
                // See https://github.com/facebook/react/pull/19216.
                didReceiveUpdate = true;
            } else {
                // An update was scheduled on this fiber, but there are no new props
                // nor legacy context. Set this to false. If an update queue or context
                // consumer produces a changed value, it will set this to true. Otherwise,
                // the component will assume the children have not changed and bail out.
                // 首次渲染最终会进入此条件
                didReceiveUpdate = false;
            }
        }
    } else {
        didReceiveUpdate = false;
    }

    // Before entering the begin phase, clear pending update priority.
    // TODO: This assumes that we're about to evaluate the component and process
    // the update queue. However, there's an exception: SimpleMemoComponent
    // sometimes bails out later in the begin phase. This indicates that we should
    // move this assignment out of the common path and into each branch.
    workInProgress.lanes = NoLanes;

    // 根据不同的 tag 再一次将 Fiber 树的信息完善
    switch (workInProgress.tag) {
        case IndeterminateComponent: {
            return mountIndeterminateComponent(
                current,
                workInProgress,
                workInProgress.type,
                renderLanes
            );
        }
        case LazyComponent: {
            const elementType = workInProgress.elementType;
            return mountLazyComponent(
                current,
                workInProgress,
                elementType,
                updateLanes,
                renderLanes
            );
        }
        case FunctionComponent: {
            const Component = workInProgress.type;
            const unresolvedProps = workInProgress.pendingProps;
            const resolvedProps =
                workInProgress.elementType === Component
                    ? unresolvedProps
                    : resolveDefaultProps(Component, unresolvedProps);
            return updateFunctionComponent(
                current,
                workInProgress,
                Component,
                resolvedProps,
                renderLanes
            );
        }
        case ClassComponent: {
            const Component = workInProgress.type;
            const unresolvedProps = workInProgress.pendingProps;
            const resolvedProps =
                workInProgress.elementType === Component
                    ? unresolvedProps
                    : resolveDefaultProps(Component, unresolvedProps);
            return updateClassComponent(
                current,
                workInProgress,
                Component,
                resolvedProps,
                renderLanes
            );
        }
        case HostRoot:
            return updateHostRoot(current, workInProgress, renderLanes);
        case HostComponent:
            return updateHostComponent(current, workInProgress, renderLanes);
        case HostText:
            return updateHostText(current, workInProgress);

        // ... 这还有好多 case
    }
}

// switch 部分就是 beginWork 的核心
// 对各个不同的 tag 创建相应的节点
// 其中有很多个 updateXXXX 函数。
// 无法详细的展开
// 但是他们都最终都会调用 reconcileChildren 来生成当前节点的子节点
```

### reconcileChildren

reconcileChildren 只是做逻辑分发，最终又调用 mountChildFibers 和 reconcileChildFibers 函数

而两个函数处理了 ChildReconciler 函数的不同情况

> var reconcileChildFibers = ChildReconciler(true);
> var mountChildFibers = ChildReconciler(false);

[ChildReconciler](https://github.com/facebook/react/blob/56e9feead0f91075ba0a4f725c9e4e343bca1c67/packages/react-reconciler/src/ReactChildFiber.old.js#L253) 的代码量更加大

里面有很多 placeXXX、deleteXXX、updateXXX、reconcileXXX 这样命名有规律的函数，这些函数是对 Fiber 节点的创建、增加、删除、修改的操作

这些函数被 ChildReconciler 返回的 reconcileChildFibers 函数间接消费

ChildReconciler 入参的 boolean 值，是用于标记后续的操作方式的

至此，beginWork 的工作结束，Fiber 树上被标记着各种对应 DOM 的操作类型。

紧接着就 commit
