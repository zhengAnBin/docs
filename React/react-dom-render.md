---
title: react-dom-render
---

### performSyncWorkOnRoot

```jsx
//  ReactFiberWorkLoop.old.js
function performSyncWorkOnRoot(root) {

  // ...

  if ( ... ) {
    renderRootSync()
  }

  // ...
  const finishedWork: Fiber = (root.current.alternate: any);
  root.finishedWork = finishedWork;
  root.finishedLanes = lanes;
  commitRoot(root);

  // Before exiting, make sure there's a callback scheduled for the next
  // pending level.
  ensureRootIsScheduled(root, now());

  return null;
}

// 代码上呈现出来的是调用了 renderRootSync 函数

```

### renderRootSync

```js
function renderRootSync(root: FiberRoot, lanes: Lanes) {
  // ...

  // If the root or lanes have changed, throw out the existing stack
  // and prepare a fresh one. Otherwise we'll continue where we left off.
  if (workInProgressRoot !== root || workInProgressRootRenderLanes !== lanes) {
    prepareFreshStack(root, lanes);
    startWorkOnPendingInteractions(root, lanes);
  }

  // ...

  do {
    try {
      workLoopSync();
      break;
    } catch (thrownValue) {
      handleError(root, thrownValue);
    }
  } while (true);
  resetContextDependencies();
  if (enableSchedulerTracing) {
    popInteractions(((prevInteractions: any): Set<Interaction>));
  }

  executionContext = prevExecutionContext;
  popDispatcher(prevDispatcher);

  // ...

  // Set this to null to indicate there's no in-progress render.
  workInProgressRoot = null;
  workInProgressRootRenderLanes = NoLanes;

  return workInProgressRootExitStatus;
}

// prepareFreshStack 省略不展开
// prepareFreshStack 函数调用了 createWorkInProgress。再度创建一颗 Fiber 树
// 前面在 legacyRenderSubtreeIntoContainer 就已经构造了一颗 Fiber 树
// diff 就是借助这两颗树进行的。
```

### createWorkInProgress

```jsx
// ReactFiber.old.js
function createWorkInProgress(current: Fiber, pendingProps: any): Fiber {
  let workInProgress = current.alternate;
  if (workInProgress === null) {
    // We use a double buffering pooling technique because we know that we'll
    // only ever need at most two versions of a tree. We pool the "other" unused
    // node that we're free to reuse. This is lazily created to avoid allocating
    // extra objects for things that are never updated. It also allow us to
    // reclaim the extra memory if needed.
    workInProgress = createFiber(
      current.tag,
      pendingProps,
      current.key,
      current.mode
    );

    // ...
    // 这里省略的代码还是在把 current 上的一些信息，挂到 workInProgress 上
  } else {
    // ...
  }

  // ...
  // 又将 current 上的信息，挂到 workInProgress 上

  return workInProgress;
}

// 创建了一颗名为 workInProgress 的 Fiber 树，而且这颗树是根据 current 创建的。
// workInProgress 上所携带的信息与 current 如出一辙
// 也就说 workInProgress 是 current 的副本
// 而且它们的还有着这样的关联
// workInProgress 的 alternate 将指向 current
// current 的 alternate 将反过来指向 workInProgress。
```

### workLoopSync

```jsx
function workLoopSync() {
  // Already timed out, so perform work without checking if we need to yield.
  while (workInProgress !== null) {
    performUnitOfWork(workInProgress);
  }
}

// workLoopSync 出奇简单
// 判断 workInProgress 不为空，就一直循环调用 performUnitOfWork
```

### performUnitOfWork

```jsx
function performUnitOfWork(unitOfWork: Fiber): void {
  // The current, flushed, state of this fiber is the alternate. Ideally
  // nothing should rely on this, but relying on it here means that we don't
  // need an additional field on the work in progress.
  const current = unitOfWork.alternate;
  setCurrentDebugFiberInDEV(unitOfWork);

  let next;
  if (enableProfilerTimer && (unitOfWork.mode & ProfileMode) !== NoMode) {
    startProfilerTimer(unitOfWork);
    next = beginWork(current, unitOfWork, subtreeRenderLanes);
    stopProfilerTimerIfRunningAndRecordDelta(unitOfWork, true);
  } else {
    next = beginWork(current, unitOfWork, subtreeRenderLanes);
  }

  resetCurrentDebugFiberInDEV();
  unitOfWork.memoizedProps = unitOfWork.pendingProps;
  if (next === null) {
    // If this doesn't spawn new work, complete the current work.
    completeUnitOfWork(unitOfWork);
  } else {
    workInProgress = next;
  }

  ReactCurrentOwner.current = null;
}
```

performUnitOfWork 里有两个重点函数

beginWork 和 completeUnitOfWork

completeUnitOfWork 经过逻辑处理最终调用 completeWork 来处理处理核心工作

beginWork: 被称之为 [render](/React/source/beginWork) 阶段

completeWork: 被称之为 [commit](/React/source/completeWork) 阶段
