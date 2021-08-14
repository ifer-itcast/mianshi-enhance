## 0. 为什么需要 Fiber
React16 之前的版本，更新过程是同步的，可能导致性能问题，

例如更新一个组件需要 1ms，有 200 个组件需要更新那就需要 200ms，而这 200ms 期间是做不了其他事情的

例如往 input 框中输入内容你会发现没有反应，200ms 之后啪啪啪的全出来了，这就是页面卡顿！

## 1. 什么是 Fiber
React Fiber 是对 React 核心算法的一次重新实现！

Fiber 的思想就是对耗时很长的同步任务进行分片，把更新过程碎片化！每一个小片的运行时间很短，虽然总时间依然很长，但是在每个小片执行完之后，都给其他任务一个执行的机会，这样唯一的线程就不会被独占！

Fiber 每执行完一段更新过程，就把控制权交还给 React 负责任务协调的模块，看看有没有其他紧急任务要做，如果没有就继续去更新，如果有紧急任务，那就去做紧急任务。

维护每一个分片的数据结构，就是Fiber。

## 2. 生命周期
初始化：`constructor`、`getDerivedStateFromProps`、`render`、`componentDidMount`

更新：`getDerivedStateFromProps`、`shouldComponentUpdate`、`render`、`componentDidUpdate`

销毁：`componentWillUnmount`

## 3. React 17
没有新功能，支持多版本混用，为以后新版本的平滑升级做准备，承上启下的战略版本！

事件委托的机制改变了、向原生浏览器靠拢、删除事件池、useEffect 清理操作改为异步操作、JSX 不可返回 undefined、删除部分私有 API

## 4. Hooks
一类特殊的函数，为你的函数式组件注入特殊的功能！目的之一是给函数式组件加上状态，第二是代码复用！

