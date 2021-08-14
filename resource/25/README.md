## 0. 工厂模式

```javascript
function createPerson(name, age, job) {
    let o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function () {
        console.log(this.name);
    };
    return o;
}
let person1 = createPerson("Nicholas", 29, "Software Engineer");
let person2 = createPerson("Greg", 27, "Doctor");
```

## 1. 构造函数模式
工厂模式没有解决对象标识问题，新创建的对象是什么类型

## 2. 单例模式

```javascript
const CreateSingle = (function () {
    let instance;
    return function (name, age) {
        if (instance) {
            return instance;
        }
        this.name = name;
        this.age = age;
        return instance = this;
    };
})();
CreateSingle.prototype.say = function () {
    console.log(this.name);
};

const a = new CreateSingle('ifer', 18);
const b = new CreateSingle('elser', 19);
console.log(a === b);
```

## 3. 发布订阅和观察者

