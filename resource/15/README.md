## DOM Diff 目的/核心

Diff 比较的核心是节点复用，但节点复用并不是无线的递归查找，而是同层比较，这样降低了一些复用性，但避免了过度优化！

只有两个新旧节点（Vnode 节点，而非 DOM）是相同节点的时候，才会去比较他们各自的子节点！

## Diff 流程

1、先找到不需要移动的相同节点，消耗最小

2、再找相同但是需要移动的节点，消耗第二小

3、最后找不到，才会去新建删除节点，保底处理