## BFC 是什么

BFC(Block formatting contexts)，[参考 W3C 文档](https://www.w3.org/TR/CSS2/visuren.html#block-formatting)，[参考 MDN 文档](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)

块格式化上下文，它是文档中的一块渲染区域，有自己的一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用！

## 如何触发 BFC
1、根元素（`<html>`）

2、浮动元素（元素的 float 不是 none）

3、绝对定位元素（元素的 position 为 absolute 或 fixed）

4、行内块元素（元素的 display 为 inline-block）

5、表格单元格（元素的 display 为 table-cell，HTML 表格单元格默认为该值）

6、overflow 不等于 visible

7、更多的触发方法参考 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context) 

## BFC 的特性
1、同一个 BFC 下上下外边距会合并

2、清除浮动（BFC 可以包含浮动的元素）

3、BFC 的区域不会与 float 的元素重叠