---
title: react浅析
category: react
tag:
  - study
  - react
  - js
abbrlink: 21598
date: 2022-05-25 20:40:21
---

# _***一点杂谈 about Reactjs***_

## 0. react fiber

在`react16`之前，更新 dom 是无法中断的，当组件数层级很深时递归占用大量时间，造成卡顿，于是全新的`Fiber`架构应运而生，`React16`将**递归的无法中断的更新**重构为**异步的可中断更新**，解决了这个问题。

### Fiber 的结构

```js
function FiberNode(
  tag: WorkTag,
  pendingProps: mixed,
  key: null | string,
  mode: TypeOfMode
) {
  // 作为静态数据结构的属性
  this.tag = tag;
  // diff算法优化
  this.key = key;
  // 一般dom节点都是相同的，那些memo,lazy,provider那些标识会不同
  this.elementType = null;
  this.type = null;
  // 创建这个节点的函数
  this.stateNode = null;

  // 指向父级Fiber节点
  this.return = null;
  // 指向子Fiber节点
  this.child = null;
  // 指向一个兄弟Fiber节点
  this.sibling = null;
  // 指向该fiber在另一次更新时对应的fiber
  this.alternate = null;

  this.index = 0;

  this.ref = null;

  // 作为动态的工作单元的属性,hooks会挂载在这里
  this.pendingProps = pendingProps;
  this.memoizedProps = null;
  this.updateQueue = null;
  // hooks环状链表
  this.memoizedState = null;
  this.dependencies = null;

  this.mode = mode;

  this.effectTag = NoEffect;
  this.nextEffect = null;

  this.firstEffect = null;
  this.lastEffect = null;

  // 调度优先级相关
  this.lanes = NoLanes;
  this.childLanes = NoLanes;
}
```

[ReactFiber.new.js](https://github.com/facebook/react/blob/main/packages/react-reconciler/src/ReactFiber.new.js)

### 工作逻辑

> fiber 采用双缓存技术来保存树,在 React 工作时共存在四种与 dom 相关的树

1.  `jsx`树
2.  `workInProgress fiber`树
3.  `current fiber`树
4.  `dom`树

渲染成`dom`树的树是`current fiber`树,正在内存中构建的树是`workInProgress fiber` 树,他们通过`alternate`相连接, diff 算法就是比较这两棵树的差异的

```js
currentFiber.alternate === workInProgressFiber;
workInProgressFiber.alternate === currentFiber;
```

当`workInProgress Fiber树`在内存中构建完成交给`Renderer`渲染在页面上后，`fiber root node`的`current`指针指向`workInProgress Fiber树`，此时`workInProgress Fiber树`就变为`current Fiber树`。

## 1. react hooks 原理

> react hooks 就是在当前组件对应的 fiber 节点上存放了 memorizedState 链表，每个 hook 都从对应的链表节点上存取自己的值。

## 2. react hooks 中的闭包陷阱

react hooks 的闭包陷阱常指 useEffect useCallback 这类 hooks 回调内部引用某个 state，但是未将其添加到依赖项里导致无法获取到最新的 state。

## **3. 参考链接 🔗**

[卡颂老师的 React 技术揭秘](https://react.iamkasong.com/)

[掘金小册](https://juejin.cn/book/6945998773818490884/section/6959872072906440743)

[React 源码](https://github.com/facebook/react)
