## 0. debounce

```javascript
const debounce = (fn, time) => {
    let timer = null;
    return function () {
        clearTimeout(timer);
        timer = setTimeout(() => {
            fn.apply(this, arguments);
        }, time);
    };
};

document.onmousemove = debounce(function (e) {
    console.log(this, e);
}, 1000);
```

## 1. throttle

```javascript
const throttle = (fn, time) => {
    let bBar = true;
    return function () {
        // if (!bBar) return; // 也能保证 bBar true 就继续，false 不继续
        if (bBar) {
            // bBar 一直是 false，持续触发的话这里就不会再次进来，但是等时间到了 bBar 就是 true 就进来了
            bBar = false;
            setTimeout(() => {
                fn.apply(this, arguments);
                // 这样写需要上面传递一下
                // fn.call(this, e);
                // 定时器到达之后再把开关打开，函数就会执行
                bBar = true;
            }, time);
        }
    };
};
```
