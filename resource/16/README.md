## 0. production 模式打包自带优化
`tree shaking`，把一些导入却没有用到的代码干掉

`Scope hositing`，分析出模块之间的依赖关系，尽可能的把打散的模块合并到一个函数中，体积更小

代码压缩、混淆

## 1. CSS 优化
将 CSS 提取到独立文件、自动添加 CSS 前缀、开启 CSS 压缩

## 2. JS 优化
三种常见的代码分离方法

入口起点：使用entry配置，手动的分离代码

放置重复：使用 SplitChunksPlugin 去重和分离 chunk

动态导入：通过模块的内联函数调用来分离代码

## 4. noParse
阻止 webpack 浪费精力去解析这些明知道没有依赖的库

## 5. IgnorePlugin
例如忽略所有语言包，然后按需加载！

## 6. DLLPlugin
在引入一些第三方模块时，例如Vue、React等，这些框架的文件一般都是不会修改的，而每次打包都需要去解析他们，会影响打包速度！

## 7. externals