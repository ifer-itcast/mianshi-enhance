## 代码

```javascript
function create() {
    // 获得构造函数，并把 arguments 中的第一项构造函数删除，因为后面传参时不需要这个构造函数
    let Con = [].shift.call(arguments)
    // 创建空对象并把 __proto__ 指向构造函数的原型
    let obj = Object.create(Con.prototype)
    // 执行构造函数，并绑定 this
    let result = Con.apply(obj, arguments)
    // 构造函数中明确返回了对象，就应该是对象，否则是 obj
    return typeof result === 'object' ? result : obj
}
```

## 测试

```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
}
Person.prototype.showAge = function () {
    console.log(this.age)
};

const p = create(Person, 'ifer', 18);
console.log(p.name, p.age);
p.showAge();

console.log(p.__proto__ === Person.prototype)
```
