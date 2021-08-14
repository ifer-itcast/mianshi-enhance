## JSON.stringify
首先要知道 `JSON.parse(JSON.stringify(obj))` 的问题

```javascript
// USNFC
// undefined、Symbol、no enumerable、function、circular reference...
const o = {
    a: undefined,
    b: Symbol('ifer'),
    c: function () { },
};
Object.defineProperty(o, 'e', {
    value: 18,
    enumerable: false
});
console.log(JSON.stringify(o));
```

## 实现深拷贝

```javascript
function deepClone(obj, hash = new WeakMap()) {
    if(obj === null) return obj;
    if(obj instanceof Date) return new Date(obj);
    if(obj instanceof RegExp) return new RegExp(obj);

    // 函数或普通值，不需要拷贝
    if(typeof obj !== 'object') return obj;

    // #1
    if(hash.get(obj)) return hash.get(obj);
    
    // #2
    let cloneObj = new obj.constructor(); // Maybe Object

    // #3
    hash.set(obj, cloneObj);

    for(let key in obj) {
        if(obj.hasOwnProperty(key)) {
            // #4 重点，cloneObj 和 hash
            cloneObj[key] = deepClone(obj[key], hash);
        }
    }
    return cloneObj;
}
```
