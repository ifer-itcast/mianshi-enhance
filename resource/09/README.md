## 0. 是什么
上游中间件进行逻辑处理，调用 next 将控制流交个下游中间件，最后一层中间件处理完成后回溯执行各个中间件的后期逻辑（next 后的代码），这就是洋葱模型！

## 1. 目的

```javascript
const express = require('express');
const app = express();

app.use((req, res, next) => {
    next();
    // !洋葱模型的目的：上游可以拿到下游的数据
    console.log(req.data);
});

app.use((req, res, next) => {
    next();
    req.data = '我是从数据库拿过来的数据';
});

app.get('/', (req, res) => {
    res.send('ok');
});

// 监听端口
app.listen(3000, () => console.log('Server running on http://localhost:3000'));
```

## 2. 实现

[参考地址](https://blog.csdn.net/weixin_33978016/article/details/87985641)