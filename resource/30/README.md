```javascript
const arr = [
    { id: 'a', pid: '', name: '总裁办' },
    { id: 'b', pid: '', name: '行政部' },
    { id: 'c', pid: '', name: '财务部' },
    { id: 'd', pid: 'c', name: '财务核算部' },
    { id: 'e', pid: 'c', name: '税务管理部' },
    { id: 'f', pid: 'e', name: '税务管理部-分部' },
];

const fn = (list, rootId) => {
    const arr = [];
    list.forEach((item) => {
        if (item.pid === rootId) {
            let children = fn(list, item.id);
            if (children.length) {
                item.children = children;
            }
            arr.push(item);
        }
    });
    return arr;
};
```
