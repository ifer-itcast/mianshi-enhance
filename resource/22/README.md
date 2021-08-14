作用之一：参数复用

```javascript
function add() {
    // !第一个函数里的参数
    let args = Array.prototype.slice.call(arguments);
    let inner = function () {
        // !把第二个函数的参数加到第一个括号里面
        args.push(...arguments);
        return inner;
    };
    inner.toString = () => args.reduce((calc, cur) => calc + cur);
    return inner;
}

console.log(add(1)(2));
```
