## 0. init

```javascript
const p1 = new Promise((resolve) => {
    setTimeout(() => {
        resolve(1);
    }, 1000);
});
const p2 = new Promise((resolve) => {
    setTimeout(() => {
        resolve(2);
    }, 1000);
});
```

## 1. Promise.all

```javascript
Promise.all2 = function (promises) {
    const len = promises.length;
    return new Promise((resolve, reject) => {
        const results = [];
        let count = 0;
        for (let i = 0; i < len; i++) {
        	// Promise.resolve 包一下是为了防止传递过来的不是个 Promise 的情况
            Promise.resolve(promises[i]).then(data => {
                count++;
                results[i] = data;
                if (count === len) resolve(results);
            }, err => {
                return reject(err)
            });
        }
    });
};

Promise.all2([p1, p2]).then((r) => {
    console.log(r);
});
```

## 2. Promise.race

```javascript
Promise.race2 = function (promises) {
    return new Promise(function (resolve, reject) {
        for (let i = 0; i < promises.length; i++) {
            Promise.resolve(promises[i]).then(data => {
                resolve(data);
            }, err => {
                return reject(err)
            });
        }
    });
};

Promise.race2([p1, p2]).then((r) => {
    console.log(r);
});
```
