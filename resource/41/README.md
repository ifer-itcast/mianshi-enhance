## 可选链

```js
const person = {
    student: {
        name: 'ifer',
        age: 18,
        info: {
            address: '豫'
        }
    }
};

// const address = person.student.info.address;

// 假如后端返回的 student 不存在或打错了
// const address = person.student && person.student.info.address;

// 假如后端返回的 info 不存在或打错了
// const address = person.student && person.student.info && person.student.info.address;

const address = person?.student?.info?.address;

console.log(address);
```

## 空合并

```js
// 0、""、false、null、undefined
const obj = {
    a: 0,
    b: false,
    c: ''
};
// 这并不是我想要的结果，因为 0、false、'' 对我是有意义的
/* let a = obj.a || 10;
let b = obj.b || true;
let c = obj.c || '1';
console.log(a, b, c); */

// null 和 undefined 才会进到后面
let a = obj.a ?? 10;
let b = obj.b ?? true;
let c = obj.c ?? '1';
let d = obj.d ?? 'xxx';
console.log(a, b, c, d);
```

## 私有属性

```js
class Person {
    #name='ifer'
    getName() {
        console.log(this.#name);
    }
};
const p = new Person();
p.getName();
```

## Promise.allSettled

```js
/* const promise1 = Promise.resolve('promise1 resolve');
const promise2 = Promise.resolve('promise2 resolve');
const promise3 = Promise.resolve('promise3 resolve');

Promise.all([promise1, promise2, promise3]).then(res => {
    console.log(res);
}).catch(err => {
    console.log(err);
}); */
/* const promise1 = Promise.resolve('promise1 resolve');
const promise2 = Promise.reject('promise2 reject');
const promise3 = Promise.resolve('promise3 resolve');

Promise.all([promise1, promise2, promise3]).then(res => {
    console.log(res);
}).catch(err => {
    console.log(err);
}); */

// 其他两个数据还是需要用的
const promise1 = Promise.resolve('promise1 resolve');
const promise2 = Promise.reject('promise2 reject');
const promise3 = Promise.resolve('promise3 resolve');

Promise.allSettled([promise1, promise2, promise3]).then(res => {
    console.log(res);
});
```

## import

index.html

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
<script>
import('./index.js').then(res => {
    res.sum(1, 3);
}).catch(err => {
    console.log(err);
});
</script>
</body>

</html>
```

index.js

```js
export const sum = (a, b) => {
    console.log('result:', a + b);
};
```

## BigInt

```js
const num = Number.MAX_SAFE_INTEGER;
// console.log(num);
// console.log(num === Math.pow(2, 53) - 1);
// console.log(9007199254740991);
/* const num1 = 9007199254740992;
const num2 = 9007199254740993;
console.log(num1 === num2); */

// bigint 类型只能和 bigint 类型相加
const num1 = 9007199254740992n + 1n;
const num2 = 9007199254740993n;
console.log(num1 === num2);
```
