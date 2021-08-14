## 发布订阅

```javascript
class EventEmitter {
    constructor() {
        this.subs = Object.create(null);
    }
    $on(eventType, handler) {
        if (this.subs[eventType]) {
            this.subs[eventType].push(handler);
        } else {
            this.subs[eventType] = [handler];
        }
    }

    $emit(eventType) {
        if (this.subs[eventType]) {
            this.subs[eventType].forEach(handler => handler())
        }
    }
}
// #1 事件中心
const eventHub = new EventEmitter();

// #2 订阅者
eventHub.$on('test', () => {
    console.log('收到啦~');
});
eventHub.$on('test', () => {
    console.log('收到啦~');
});
// #3 发布者
eventHub.$emit('test');
```

## 观察者

```javascript
// Dep 类似发布者
// 观察者（Watcher）类似订阅者
class Dep {
    constructor() {
        // 存储所有的观察者（订阅者 subscribe），订阅报纸的、看报纸的人
        this.subs = [];
    }

    addSub(sub) {
        if (sub && sub.update) {
            this.subs.push(sub);
        }
    }

    notify() {
        this.subs.forEach(sub => sub.update());
    }
}

class Watcher {
    update() {
        console.log('update');
    }
}

// 发布者
const dep = new Dep();
// 订阅者
const watcher = new Watcher();

dep.addSub(watcher);
dep.notify();
```
