## 先说结论
class 中，无论是箭头函数方法还是普通函数方法，其中的 this 都指向实例对象，即谁调用指向谁

class 中的普通函数是绑定在原型上的，箭头函数是直接挂载在构造函数中的

## 再上案例

```javascript
// 无论是箭头函数方法还是普通函数方法，方法中的 this 都指向实例对象
class A {
    method1() { // 普通函数绑定到原型上
        console.log(this);
    }
    method2 = () => { // 箭头函数绑定到构造函数上
        console.log(this)
    }
}
let a = new A();
let b = new A();
console.log(a.method1 === b.method1); // true
console.log(a.method2 === b.method2); // false

a.method1(); // 谁调用就是谁，this 就是实例，就是 a
a.method2(); // 无论是箭头函数方法还是普通函数方法，方法中的 this 都指向实例对象，记住
a.method1.bind({ test: '123' })(); // bind 优先级高，this 是 { test: '123' }
a.method2.bind({ test: '123' })(); // bind 不能改变箭头函数中的 this，this 还是实例 a
a.method1.bind({ test: '123' }).bind({ test: '456' })();

// function a() {
//     console.log(this);
// }
// // 无论使用bind绑定多少次，最终原函数的this值是由第一次绑定传的参数决定的
// // bind 多次也只会执行一次，因为 bind 每次返回的都是一个新函数，只会在最后一次调用的时候执行那一个最新的函数
// a.bind().bind().bind()();
```
