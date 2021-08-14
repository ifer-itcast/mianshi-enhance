## 0. 为什么用流
之所以用 stream ，是因为一次性读取、操作大文件，内存和网络是“吃不消”的，因此要让数据流动起来，一点一点的进行操作，分而治之！

## 1. 分类
readable stream

writeable stream

双工流（Duplex 和 Transform）

```javascript
// 读取流转换后再导入到写入流，Gulp 就是这个原理
read.pipe(transform).pipe(write);
```