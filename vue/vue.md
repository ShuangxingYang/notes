##### 具名插槽暴露内部属性

```vue
// MyComponent.vue
<template>
  <div>
    <slot name="custom" :customProp="internalProp" :customMethod="internalMethod"></slot>
  </div>
</template>


<MyComponent>
  <template v-slot:custom="{ customProp, customMethod }">
    <div>{{ customProp }}</div>
    <button @click="customMethod">Call Custom Method</button>
  </template>
</MyComponent>
```

##### 函数式调用组件

```ts
export const createPortal = (Component: VueConstructor, props: Record<any, any>) => {
  Component = Vue.extend(Component)
  const mountAt = document.createElement('div')
  const farterNode = document.getElementsByClassName('_magic__root_')?.[0]
  farterNode?.appendChild(mountAt)
  const instance = new Component({ propsData: props }).$mount(mountAt)
  console.log('Component', instance)
  return instance
}
```

```ts
import { createPortal } from '../../../libs/util'
import Comp from './index.vue'

export default (() => {
  return async (options): Promise<Record<string, any>> => {
    let instance
    return await new Promise((resolve, reject) => {
      instance || (instance = createPortal(Comp, options))
      Object.assign(instance, options, {
        resolve,
        reject,
        callback: (action, param) => {
          const type = action === 'confirm' ? 'resolve' : 'reject'
          const res = action === 'confirm' ? param : action
          instance[type](res)
        },
      })
      instance.show()
      return instance
    })
  }
})()
```



##### watch 监听复杂数据类型时，newval === oldval

```js
computed: {
    myPkgList() {
      return JSON.parse(JSON.stringify(this.pkgList));
    }
  },
watch: {
    myPkgList: {
      handler: function(val, oldVal) {
      },
      deep: true
    }
  }
```



##### Vue事件触发范围异常

原本是给按钮绑定的点击事件，但是点击按钮附近的元素也会触发，给按钮附近的元素加

```
@click.prevent
```

