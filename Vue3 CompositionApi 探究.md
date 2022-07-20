## Vue3 CompositionApi 探究

### 包括

### 动机

### 体验

- 结合reactiveity

   ```js
   data (){
   	return {
   		counter:1
   	}
   },
   mounted(){
     setInterval(() => {
       this.counter++
     },1000)
   }
   
   // compositionapi
   import {ref, onMounted} from "vue"
   setup(){
     const counter = ref(1)
     onMounted(() => {
       setInterval(() => {
         counter.value++
       },1000)
     })
     return {
       counter
     }
   }
   
   ```

  - 属性和上下文

```js
import {ref, onMounted} from "vue"
setup(propsm {emit, slots, att}){
  const counter = ref(0)
  onMounted(() => {
    setInterval(() => {
      counter.value++
    },1000)
  })
  return { counter }
}
```

### 探究

执行的时刻？ 为什么没有created钩子

传入setup参数中，分别是props，ctx，他们从何而来，又是什么》

如果和data这些数据发生了冲突，他们能共存吗？Vue3处理的行为？



#### 为什么没有created钩子

- mount
- render
- patch
- processComponent
  - mountComponent 创建组件实例
  - 组件实例初始化setupComponent(instance)