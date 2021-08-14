## 0. 加载渲染过程

父 beforeCreate
父 created
父 beforeMount

子 beforeCreate
子 created
子 beforeMount
子 mounted

父 mounted

## 1. 子组件更新过程
子 beforeUpdate
子 updated

## 2. 父组件更新过程
`父组件更新的数据子组件有使用！`

父 beforeUpdate
子 beforeUpdate
子 updated
父 updated

## 3. 销毁过程
父 beforeDestroy
子 beforeDestroy
子 destroyed
父 destroyed