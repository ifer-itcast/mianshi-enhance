## 1. then

需求

```javascript
const p = new Promise((resolve, reject) => {
    reject('rejected');
});

p.then(value => {
    console.log(value);
}, reason => {
    console.log(reason);
});
```

实现

```javascript
class Promise {
    constructor(executor) {
        // 状态
        this.status = 'pending';
        // 成功的值
        this.value = undefined;
        // 失败的值
        this.reason = undefined;

        const resolve = value => {
            if (this.status === 'pending') {
                this.status = 'fulfilled';
                this.value = value;
            }
        };

        const reject = reason => {
            if (this.status === 'pending') {
                this.status = 'rejected';
                this.reason = reason;
            }
        };

        try {
            // executor 调用的是外部传递过来的函数参数
            // 调用 executor 的时候又给此外部传递过去的两个函数，这两个函数是被外部调用的，外部调用的时候会传递实参到当前 resolve、reject 定义的地方
            executor(resolve, reject);
        } catch (err) {
            reject(err);
        }
    }

    then(onFulfilled, onRejected) {
        if(this.status === 'fulfilled') {
            onFulfilled(this.value);
        }
        if(this.status === 'rejected') {
            onRejected(this.reason);
        }
    }
}
```

## 2. 支持异步

需求

```javascript
const p = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('fulfilled');
    }, 1000);
});

p.then(value => {
    console.log(value);
});
p.then(value => {
    console.log(value);
});
```

实现

```javascript
class Promise {
    constructor(executor) {
        // 状态
        this.status = 'pending';
        // 成功的值
        this.value = undefined;
        // 失败的值
        this.reason = undefined;

        // 给数组是为了支持多个实例进行 then 的情况
        this.onFulfilledCallbacks = [];
        this.onRejectedCallbacks = [];

        const resolve = value => {
            if (this.status === 'pending') {
                this.status = 'fulfilled';
                this.value = value;

                // 只要外部调用了 resolve，就循环成功数组的函数
                this.onFulfilledCallbacks.forEach(cb => cb());
            }
        };

        const reject = reason => {
            if (this.status === 'pending') {
                this.status = 'rejected';
                this.reason = reason;

                // 只要外部调用了 reject，就循环失败数组的函数
                this.onRejectedCallbacks.forEach(cb => cb());
            }
        };

        try {
            executor(resolve, reject);
        } catch (err) {
            reject(err);
        }
    }

    then(onFulfilled, onRejected) {
        if(this.status === 'fulfilled') {
            onFulfilled(this.value);
        }
        if(this.status === 'rejected') {
            onRejected(this.reason);
        }

        if(this.status === 'pending') {
            this.onFulfilledCallbacks.push(() => onFulfilled(this.value));
            this.onRejectedCallbacks.push(() => onRejected(this.reason));
        }
    }
}
```

## 3. 收工

```javascript
// 解析链式调用
function resolvePromise(x, promise2, resolve, reject) {
    if (x === promise2) {
        return reject(new TypeError('循环引用'));
    }
    let called;
    // x 是一个函数或者是一个对象，就有可能是一个 Promise
    if (x !== null && (typeof x === 'function' || typeof x === 'object')) {
        try {
            let then = x.then;
            if (typeof then === 'function') {
                
                then.call(
                    x,
                    function(y) {
                        // called 的作用是，只能调 resolve 或 reject 不能调用 2 次
                        // 没调过
                        // #1
                        if (called) return;
                        called = true;
                        // y 可能还是一个 Promise，那就递归，直到是一个常量为止
                        // resolve(y);
                        resolvePromise(y, promise2, resolve, reject);
                    },
                    function(r) {
                        if (called) return;
                        called = true;
                        reject(r);
                    }
                );
            } else {
                // x => { then: 123 }
                // if (called) return;
                // called = true;
                resolve(x);
            }
        } catch (e) {
            // #3
            if (called) return;
            called = true;
            
            // x.then 发生了异常
            reject(e);
        }
    } else {
        // 普通值的情况直接 resolve 即可
        resolve(x);
    }
}

// executor 执行出错应该进行错误捕获
function Promise(executor) {
    let self = this;
    self.value = undefined;
    self.reason = undefined;
    self.onResolvedCallbacks = [];
    self.onRejectedCallbacks = [];

    self.status = 'pending';

    function resolve(value) {
        if (self.status === 'pending') {
            self.value = value;
            self.status = 'resolved';

            self.onResolvedCallbacks.forEach(function(fn) {
                fn();
            });
        }
    }

    function reject(reason) {
        if (self.status === 'pending') {
            self.reason = reason;
            self.status = 'rejected';

            self.onRejectedCallbacks.forEach(function(fn) {
                fn();
            });
        }
    }
    try {
        executor(resolve, reject);
    } catch (e) {
        reject(e);
    }
}

Promise.prototype.then = function(onFulFilled, onRejected) {
    onFulFilled = typeof onFulFilled === 'function' ? onFulFilled : function(data) {
        return data;
    };

    onRejected = typeof onRejected === 'function' ? onRejected : function(err) {
        throw err;
    };
    let self = this;

    let promise2 = new Promise(function(resolve, reject) {
        if (self.status === 'resolved') {
            // 调用传递过来的参数，并传递 resolve 的结果
            // 如果 x 是普通值，就让这个返回的 Promise 变成成功态
            setTimeout(() => {
                try {
                    let x = onFulFilled(self.value);
                    // setTimeout 包裹的目的是为了在这里拿到 promise2
                    resolvePromise(x, promise2, resolve, reject);
                } catch (e) {
                    reject(e);
                }
            }, 0);
        }

        if (self.status === 'rejected') {
            setTimeout(() => {
                try {
                    let x = onRejected(self.reason);
                    resolvePromise(x, promise2, resolve, reject);
                } catch (e) {
                    reject(e);
                }
            }, 0);
        }

        // executor 中有异步操作，then 时处于等待
        if (self.status === 'pending') {
            self.onResolvedCallbacks.push(function() {
                // 这里不用 setTimeout 也行，因为走到这里的代码外部是通过异步调用的 resolve
                setTimeout(() => {
                    try {
                        let x = onFulFilled(self.value);
                        resolvePromise(x, promise2, resolve, reject);
                    } catch (e) {
                        reject(e);
                    }
                }, 0);
            });

            self.onRejectedCallbacks.push(function() {
                setTimeout(() => {
                    try {
                        let x = onRejected(self.reason);
                        resolvePromise(x, promise2, resolve, reject);
                    } catch (e) {
                        reject(e);
                    }
                }, 0);
            });
        }
    });

    return promise2;
};

Promise.defer = Promise.deferred = function() {
    let dfd = {};
    dfd.promise = new Promise((resolve, reject) => {
        dfd.resolve = resolve;
        dfd.reject = reject;
    });
    return dfd;
};

module.exports = Promise;

// promises-aplus-tests promise7.js
```
