![在这里插入图片描述](https://img-blog.csdnimg.cn/20210315111347799.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhbmdwdWd1aQ==,size_16,color_FFFFFF,t_70)
## 0. 目标

- Vue
    - 把 data 中的成员注入到 Vue 实例，并且把 data 中的成员转成 getter/setter
- Observer
    - 能够对数据对象的所有属性进行监听，如有变动可拿到最新值并通知 Dep
- Compiler
    - 解析每个元素中的指令/插值表达式，并替换成相应的数据
- Dep
    - 添加观察者(watcher)，当数据变化通知所有观察者
- Watcher
    - 数据变化更新视图

**目标**

```html
<div id="app">
    <h2>差值表达式</h2>
    <p>{{msg}}</p>
    <p>{{count}}</p>
    <hr>
    <h2>v-text</h2>
    <p v-text="msg"></p>
    <hr>
    <h2>v-model</h2>
    <input type="text" v-model="msg">
    <input type="text" v-model="count">
</div>
```

## 1. Vue

- 功能
    - 负责接收初始化的参数(选项)
    - 负责把 data 中的数据代理到 Vue 实例（此时使用 Vue 实例访问 data 中的第一层简单数据类型，具备劫持效果）
    - 负责调用 observer 监听 data 中所有属性的变化
    - 负责调用 compiler 解析指令/插值表达式

- 结构
    - 属性：\$options、\$el、\$data
    - 方法：_proxyData


**负责把 data 中的属性以数据劫持的形式代理到 Vue 实例**

```js
class Vue {
    constructor(options) {
        // 1. 通过属性保存选项的数据
        this.$options = options || {};
        this.$data = options.data || {};
        this.$el = typeof options.el === 'string' ? document.querySelector(options.el) : options.el;
        // 2. 数据代理（把 data 中的成员注入到 Vue 实例中）
        this._proxyData(this.$data);
        // 3. 调用 observer 对象，监听数据的变化
        // 4. 调用 compiler 对象，解析指令和差值表达式
    }
    _proxyData(data) {
        // 遍历 data 中的所有属性，注入到 vue 实例中
        Object.keys(data).forEach(key => {
            // this 就是 Vue 实例
            Object.defineProperty(this, key, {
                enumerable: true,
                configurable: true,
                get() {
                    // 数据是在 data 中的
                    return data[key];
                },
                set(newValue) {
                    if (newValue === data[key]) return;
                    data[key] = newValue;
                }
            });
        });
    }
}
```

## 2. Observer

- 功能
    - 负责把 data 选项中的属性转换成响应式数据（数据劫持）
    - data 中的某个属性值也是对象，把该属性值也转换成响应式数据（数据递归劫持）
    - 数据变化发送通知
- 结构
    - 方法：walk(data)、defineReactive(data, key, value)

**负责把 data 选项中的属性转换成响应式数据**

```js
class Observer {
    constructor(data) {
        this.walk(data);
    }
    walk(data) {
        // 1. 判断 data 是否是空置或不是对象
        if (!data || typeof data !== 'object') return;
        // 2. 遍历 data 对象的所有属性
        Object.keys(data).forEach(key => {
            this.defineReactive(data, key, data[key]);
        });
    }
    defineReactive(obj, key, val) {
        Object.defineProperty(obj, key, {
            enumerable: true,
            configurable: true,
            get() {
                return val;
            },
            set(newValue) {
                if (newValue === val) return;
                val = newValue;
                // 发送通知
            }
        });
    }
}
```

```js
// 测试
vm.msg
// 先触发 vue.js 中 _proxyData 中的 get 方法，get 方法中进行了 `data[key]`，所以会再触发 observer.js 中 defineReactive 中的 get 方法
```

为什么修改 `defineReactive(obj, key, val)` 中的局部变量 val 能影响到 obj[key] 中数据，闭包！

```js
const obj = {
    msg: 'hello'
};
/* Object.defineProperty(obj, 'msg', {
    enumerable: true,
    configurable: true,
    get() {
        return obj['msg'];
    },
    set(newValue) {
        if (newValue === obj['msg']) return;
        obj['msg'] = newValue;
    }
}); */

/* let xxx = obj.msg;
Object.defineProperty(obj, 'msg', {
    enumerable: true,
    configurable: true,
    get() {
        return xxx;
    },
    set(newValue) {
        if (newValue === xxx) return;
        xxx = newValue;
    }
}); */

defineReactive(obj, 'msg', obj['msg']);

function defineReactive(obj, key, val) {
    Object.defineProperty(obj, key, {
        enumerable: true,
        configurable: true,
        get() {
            // 形成闭包，不释放
            return val;
        },
        set(newValue) {
            if (newValue === val) return;
            val = newValue;
        }
    });
}
```

**递归递归劫持**

```js
const obj = {
    person: {
        name: 'ifer'
    }
};

walk(obj);

function walk(data) {
    if (!data || typeof data !== 'object') return;
    Object.keys(data).forEach(key => {
        defineReactive(data, key, data[key]);
    });
}

function defineReactive(obj, key, val) {
    walk(val);
    // console.log(key, val); // 'name','ifer' 和 'person', {name: 'ifer'}
    Object.defineProperty(obj, key, {
        enumerable: true,
        configurable: true,
        get() {
            // 形成闭包，不释放
            return val;
        },
        set(newValue) {
            if (newValue === val) return;
            val = newValue;
        }
    });
}
```

给**已存在的属性**重新赋值一个对象，也期望是响应式的

```js
class Observer {
    // ...
    defineReactive(obj, key, val) {
        let that = this;
        this.walk(val);

        Object.defineProperty(obj, key, {
            // ...
            set(newValue) {
                if (newValue === val) return;
                val = newValue;
                // 给已存在的属性重新赋值一个对象
                that.walk(newValue);
                // 发送通知
            }
        });
    }
}
```

```js
// 注意 vm.msg 会先触发 _proxyData 中的 set 操作
// set 操作中有进行 data[key] 取值的操作，所以下面操作即会触发 defineReactive 中的 get，也会触发 set
vm.msg = { name: 'ifer' };
```

## 3. Compiler

- 功能
    - 负责编译模板，解析指令/插值表达式
    - 负责页面的首次渲染
    - 当数据变化后重新渲染视图
- 结构
    - 属性：el、vm
    - 方法：compile(el)、compileElement(node)、compileText(node)、isDirective(attrName)、isTextNode(node)、isElementNode(node)

基本框架

```js
class Compiler {
    constructor(vm) {
        this.el = vm.$el;
        this.vm = vm;
    }
    // 编译模板，处理【文本节点】和【元素节点】
    compile(el) {

    }
    // 编译元素节点，处理指令
    compileElement(node) {}
    // 编译文本节点，处理差值表达式
    compileText(node) {

    }
    // 判断元素属性是否是指令
    isDirective(attrName) {
        return attrName.startsWith('v-');
    }
    // 判断节点是否是文本节点
    isTextNode(node) {
        return node.nodeType === 3;
    }
    // 判断节点是否是元素节点
    isElementNode(node) {
        return node.nodeType === 1;
    }
}
```

实现 compile 方法

```js
class Compiler {
    constructor(vm) {
        this.el = vm.$el;
        this.vm = vm;
        this.compile(this.el);
    }
    // 编译模板，处理【文本节点】和【元素节点】
    compile(el) {
        // childNodes => 包括元素节点和文本节点
        let childNodes = el.childNodes;
        Array.from(childNodes).forEach(node => {
            if (this.isTextNode(node)) {
                // 处理文本节点
                this.compileText(node);
            } else if (this.isElementNode(node)) {
                // 处理元素节点
                this.compileElement(node);
            }

            // 判断 node 是否有子节点，如果有，则递归调用 compile
            if (node.childNodes && node.childNodes.length) {
                this.compile(node);
            }
        });
    }
    // ...
}
```

实现 compileText 方法

```js
class Compiler {
    // ...
    // 编译文本节点，处理差值表达式
    compileText(node) {
        // 以原始对象的形式进行输出
        // console.dir(node);
        // ? 代表非贪婪，尽可能早的结束匹配
        let reg = /\{\{(.+?)\}\}/;
        let value = node.textContent; // {{msg}}
        if (reg.test(value)) {
            let key = RegExp.$1.trim(); // msg
            node.textContent = value.replace(reg, this.vm[key]);
        }
    }
    // ...
}
```

实现 compileElement 方法

```js
class Compiler {
    // ...
    // 编译元素节点，处理指令
    compileElement(node) {
        // console.log(node.attributes);
        Array.from(node.attributes).forEach(attr => {
            // 判断是否是指令
            // <div v-text="msg"></div>
            let attrName = attr.name; // 'v-text'
            if(this.isDirective(attrName)) {
                attrName = attrName.substring(2); // 'text'、'model'
                let key = attr.value; // 'msg'
                this.update(node, key, attrName);
            }
        });
    }

    update(node, key, attrName) {
        let updateFn = this[attrName+'Updater'];
        updateFn && updateFn(node, this.vm[key]);
    }
    // 处理 v-text 指令
    textUpdater(node, value) {
        node.textContent = value;
    }
    // 处理 v-model 指令
    modelUpdater(node, value) {
        node.value = value;
    }
    // ...
}
```

## 4. Dep

- 功能
    - 收集依赖，添加观察者(watcher)
    - 通知所有观察者
- 结构
    - 属性：subs
    - 方法：addSub(sub)、notify()

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210311101358366.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhbmdwdWd1aQ==,size_16,color_FFFFFF,t_70)

```js
// dep.js
class Dep {
    constructor() {
        // 存储所有的观察者（订阅者）
        this.subs = [];
    }
    // 添加观察者
    addSub(sub) {
        if(sub && sub.update) {
            this.subs.push(sub);
        }
    }
    // 发送通知
    notify() {
        this.subs.forEach(sub => sub.update());
    }
}
```

```js
// observer.js
class Observer {
    // ...
    // 负责 data 数据的递归劫持，一上来的时候就会调用
    defineReactive(obj, key, val) {
        let that = this;
        // #0 负责【收集依赖】，并【发送通知】
        let dep = new Dep();
        this.walk(val);

        Object.defineProperty(obj, key, {
            enumerable: true,
            configurable: true,
            get() {
                // #1 收集依赖
                Dep.target && dep.addSub(Dep.target);
                // obj 就是 data 对象，不能使用 obj[key]
                // 这里涉及到了闭包，来扩展了 val 的作用范围
                return val;
            },
            set(newValue) {
                if (newValue === val) return;
                val = newValue;
                // 给已存在的属性重新赋值一个对象
                that.walk(newValue);
                // #2 发送通知
                dep.notify();
            }
        });
    }
}
```

## 5. Wather

- 功能
    - 当数据变化触发依赖， dep 通知所有的 Watcher 实例更新视图
    - 自身实例化的时候往 dep 对象中添加自己
- 结构
    - 属性：vm、key、cb、oldValue
    - 方法：update()

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-xTD3sGmZ-1615428428009)(./img/Watcher.png)]

```js
class Watcher {
    constructor(vm, key, cb) {
        this.vm = vm;
        // data 的属性名
        this.key = key;
        // 回调函数负责更新视图
        this.cb = cb;

        // 把 watcher 对象记录到 Dep 类的静态属性 target 上
        Dep.target = this;
        // 触发 get 方法，在 get 方法中会调用 addSub
        this.oldValue = vm[key]; // vm[key] 触发 get，会把 wather 添加到 dep
        Dep.target = null;
    }
    // 当数据发生变化的时候更新视图
    update() {
        let newValue = this.vm[this.key];
        if (this.oldValue === newValue) return;

        this.cb(newValue);
    }
}
```

调用 Watcher

```js
// compiler.js
class Compiler {
    // ...
    // 编译文本节点，处理差值表达式
    compileText(node) {
        // 以原始对象的形式进行输出
        // console.dir(node);
        // ? 代表非贪婪，尽可能早的结束匹配
        let reg = /\{\{(.+?)\}\}/;
        let value = node.textContent;
        if (reg.test(value)) {
            let key = RegExp.$1.trim();
            node.textContent = value.replace(reg, this.vm[key]);

            // 创建 wather 对象，当数据改变更新视图
            new Watcher(this.vm, key, newValue => {
                node.textContent = newValue;
            });
        }
    }
    // ...
}
```

处理 v-text 和 v-model 的情况

```js
class Compiler {
    // ...
    update(node, key, attrName) {
        let updateFn = this[attrName+'Updater'];
        updateFn && updateFn.call(this, node, this.vm[key], key);
    }
    // 处理 v-text 指令
    textUpdater(node, value, key) {
        node.textContent = value;

        new Watcher(this.vm, key, newValue => {
            node.textContent = newValue;
        });
    }
    // 处理 v-model 指令
    modelUpdater(node, value, key) {
        node.value = value;

        new Watcher(this.vm, key, newValue => {
            node.value = newValue;
        });
    }
}
```

视图影响数据

```js
class Compiler {
    // ...
    // 处理 v-model 指令
    modelUpdater(node, value, key) {
        node.value = value;

        new Watcher(this.vm, key, newValue => {
            node.value = newValue;
        });

        // 视图影响数据
        node.addEventListener('input', () => {
            this.vm[key] = node.value;
        });
    }
    // ...
}
```




