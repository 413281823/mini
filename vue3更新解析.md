###  Vue3更新流程解析



### PubliInstanceProxyHandlers

更新过程中返回一个对象

 - get

   获取数据的方法

 - set

    设置数据也就是触发更新的拦截器

   ![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6fb37d10a40247f3b79512ce7fc2db6e~tplv-k3u1fbpfcp-watermark.image?)

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

   ![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4a85b5c0e9b247d096f58cdffcc25910~tplv-k3u1fbpfcp-watermark.image?)

   同时这个函数也会判断是否是shallow也就是浅层代理，同时判断是否是Readonly也就是是否是只读的数据

   然后才是触发他的副作用

   ![image-20220717182427523](/Users/liangqianyang/Library/Application Support/typora-user-images/image-20220717182427523.png)

   通过triggerEffects函数取出副作用函数

   ![image-20220717182722119](/Users/liangqianyang/Library/Application Support/typora-user-images/image-20220717182722119.png)

   可以看到scheduler任务列表，scheduler把当前组件实例的更新函数放到了队列中

   ![image-20220717182938161](/Users/liangqianyang/Library/Application Support/typora-user-images/image-20220717182938161.png)

   现在不知道队列中的任务在哪何时执行我们不知道

   所以我们先关心componentUpdateFn,看名字就知道是一个组件更新function

   ![image-20220717183244929](/Users/liangqianyang/Library/Application Support/typora-user-images/image-20220717183244929.png)

   一进来会判断是否第一次挂载，我们现在已经挂载了，所以我们直接去看else分支

   ![image-20220717183628016](/Users/liangqianyang/Library/Application Support/typora-user-images/image-20220717183628016.png)

继续往下看

![image-20220717184108101](/Users/liangqianyang/Library/Application Support/typora-user-images/image-20220717184108101.png)

通过prevTree方法和nextTree方法比较出变化，然后调用patch函数执行dom更新。

 - has
	- ownKeys