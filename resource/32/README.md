## 0. call

```javascript
Function.prototype.myCall = function (context) {
    // arguments 传递的参数，其中第一个就是 this 指向的对象
    context = context || window;

    // 这里的 context 就是传递过来的第 1 项，this 就是需要调用的函数
    context.fn = this;

    // 截取第 1 项之后的参数
    const args = [...arguments].slice(1);

    // 执行上面缓存的函数，并传递参数，context 调用的 fn，所以 fn 中的 this 就是 context
    const result = context.fn(...args);

    delete context.fn;
	// result 是函数执行的结果，记得返回~
    return result;
}
```

## 1. apply

```javascript
Function.prototype.myApply = function (context) {
    // arguments 传递的参数，其中第一个就是 this 指向的对象
    context = context || window;

    // 这里的 context 就是传递过来的第 1 项，this 就是需要调用的函数
    context.fn = this;

    // 截取第 1 项之后的参数
    const args = [...arguments].slice(1);

    let result;
    if (arguments[1]) {
        // 注意 arguments[1] 是一个数组
        result = context.fn(...arguments[1]);
    } else {
        result = context.fn();
    }
    delete context.fn;
    return result;
}
```

## 2. bind

```javascript
Function.prototype.myBind = function (context) {
    // this 就是那个需要真正调用的函数
    const _this = this;
    // 拿到出 this 外的其他参数
    const args = [...arguments].slice(1);
    return function F() {
        // 判断能当做构造函数使用吗
        if (this instanceof F) {
            return new _this(...args, ...arguments);
        }
        // 这里才是真正的调用，context 是 this 指向，把第一个函数和第二个函数中的参数结合起来
        return _this.apply(context, args.concat(...arguments));
    }
}
```
