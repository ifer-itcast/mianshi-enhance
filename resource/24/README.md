1\. Vuex 本质是一个对象

2\. Vuex 对象有两个属性，一个是 install 方法，一个是 Store 这个类

3\. install 方法的作用是将 store 这个实例挂载到所有的组件上（Vue.mixin）

4\. Store 这个类拥有 commit，dispatch 这些方法，Store 类里将用户传入的state 包装成 data，作为 new Vue 的参数，从而实现了state 值的响应式