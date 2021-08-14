## 0. Performance
1、diff 算法优化

> Vue2 中的虚拟 DOM 是进行全量对比的，Vue3 新增了静态标记（PatchFlag），在与上次虚拟节点进行对比的时候，只对比带有 patch flag 的节点，并且可以通过 flag 的信息得知当前节点要对比的具体内容

2、hoistStatic 静态提升

> 不参与更新的元素只会被创建一次

```javascript
<div>
  <p>Hello World</p>
  <p>Hello World</p>
  <p>{{msg}}}</p>
</div>
```

3、cacheHandlers 事件侦听器缓存

4、proxy

5、ssr 渲染

## 1. Tree Shaking support
按需加载，体积比 Vue2.x 更小

## 2. Composition API

### ref

useMousePosition.js

```js
import { ref, onMounted, onUnmounted } from 'vue';

export function useMousePosition() {
    const x = ref(0);
    const y = ref(0);
    function update(e) {
        x.value = e.pageX;
        y.value = e.pageY;
    }
    onMounted(() => {
        window.addEventListener('mousemove', update);
    });

    onUnmounted(() => {
        window.removeEventListener('mousemove', update);
    });
    return {
        x,y
    };
}
```

HelloWorld.vue

```html
<template>
    <div class="hello">x: {{ x }} y: {{ y }}</div>
</template>

<script>
import { useMousePosition } from './useMousePosition';
export default {
    name: 'HelloWorld',
    setup() {
        const { x, y} = useMousePosition();
        return {
            x, y
        };
    },
};
</script>
```

### reactive

useMousePosition.js

```js
import { reactive, onMounted, onUnmounted } from 'vue';

export function useMousePosition() {
    const pos = reactive({
        x: 0,
        y: 0
    });
    function update(e) {
        pos.x = e.pageX;
        pos.y = e.pageY;
    }
    onMounted(() => {
        window.addEventListener('mousemove', update);
    });

    onUnmounted(() => {
        window.removeEventListener('mousemove', update);
    });
    return pos;
}
```

HelloWorld.vue

```html
<template>
  <div class="hello">x: {{ x }} y: {{ y }}</div>
</template>

<script>
import { toRefs } from 'vue';
import { useMousePosition } from './useMousePosition';
export default {
    name: 'HelloWorld',
    setup() {
        const { x, y } = toRefs(useMousePosition());
        return {
            x,
            y,
        };
    },
};
</script>
```

or

```html
<template>
    <div class="hello">x: {{ pos.x }} y: {{ pos.y }}</div>
</template>

<script>
import { useMousePosition } from './useMousePosition';
export default {
    name: 'HelloWorld',
    setup() {
        return {
            pos: useMousePosition()
        };
    },
};
</script>
```

## 3. Better TypeScript support

## 4. Custom Renderer API

## 5. Fragment、Teleport(Protal)、Suspense
