即 Vue 在修改数据后，视图不会立刻更新，在下次 DOM 更新循环（Event Loop）结束之后执行延迟回调！在修改数据之后立即使用这个方法，获取更新后的 DOM。

nextTick 主要使用了宏任务和微任务，根据执行环境是否支持分别尝试采用 Promise、MutationObserver、setImmediate、setTimeout！