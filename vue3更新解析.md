###  Vue3更新流程解析



### PubliInstanceProxyHandlers

更新过程中返回一个对象

 - get

   获取数据的方法

 - set

    设置数据也就是触发更新的拦截器

   传入3个参数

   - 第一个ComponentRenderContext,其实等于App实例

   - 第二个参数是key，也就是本次出发更新的属性的名称
   - 第三个参数是value，就是本次需要set的值

   const {data, setupState, ctx} = instance

   set函数结构出组件实例的data，setup，ctx（上下文中的数据）

   使用hasOwn依次判断是否存在这个数据

   判断的顺序和数据的优先级有关系

   可以概括为 setup>data>props

   找到数据对数据进行修改的时候 因为vue3的proxy，会触发一个createSetter工厂函数，返回一个set函数，用来触发副作用函数。

   同时这个函数也会判断是否是shallow也就是浅层代理，同时判断是否是Readonly也就是是否是只读的数据

 - has

	- ownKeys