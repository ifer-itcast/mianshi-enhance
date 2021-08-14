## flex

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/flex)

flex 是 flex-grow、flex-shrink、flex-basis 的复合写法

```css
flex: 1;

/* 等价于 */

flex: 1 1 0;
```

## flex-grow

省略时默认值为 1，初始值为 0

省略的意思是：flex 相当于 flex-grow、flex-shrink、flex-basis 的符合写法，没有明确写出来的称为省略

规定子元素该如何分配父元素的剩余空间，默认值 0，表示不分配父元素的剩余空间，值越大，分配的越多。不支持负值！

## flex-shrink

省略时默认值为 1，初始值为 1

规定当父元素的宽度小于子元素的宽度之和时（即子元素超出了父元素），子元素该如何缩小自己的宽度。默认值 1，表示父元素装不下弹性盒子时，弹性盒子就会减小，值越大，减小的越厉害，设置为 0 则代表不会减小！

## flex-basis

省略时默认值为 0，初始值为 auto

用来设置弹性元素的宽度，当和 width 同时使用时，生效的是此属性！

