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

### setupRenderEffect更新渲染函数

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7271b9b72b3342fb919673c0e92817be~tplv-k3u1fbpfcp-watermark.image?)

**安装渲染函数副作用**

**建立一个更新机制，便于后续如果有数据发生更新，界面会更新**

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/75c01e6038624b5b9cf1ae6bdce1a26d~tplv-k3u1fbpfcp-watermark.image?)

我们不走第一次挂载，走更新，可以看到里面有一个effect的函数

在这里面执行了我们的componentUpdate

并且刚才那个scheduler 一个定时器任务也在这里执行

所以我们应该关心queueJob函数

可以看到queuejob其实执行了effect.run(),进入queuejob 有一个

queueFlush，对收集到的队列任务进行冲刷，也就是一个启动按钮，启动批量任务执行

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/59b82cf0dd8940728144081859bc59df~tplv-k3u1fbpfcp-watermark.image?)

而且我们可以看到最后这个代码是在一个Promise.then微任务里面执行的，那么我们无法确定执行的时间，所以他就是在未来的某一个微任务队列清空的时候才会执行的。

那么他执行之后的一切都是异步发生的 我们进入flushjobs

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/27fb080ca9494fa590123fe79909d211~tplv-k3u1fbpfcp-watermark.image?)



这里是确保了整个更新的顺序

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d869f6fa008146d79e58963d543f5a15~tplv-k3u1fbpfcp-watermark.image?)

这里就是冲刷的过程，循环队列，每次取出一个事件去执行，这里看到了job确实被执行之后，我们可以看过程queuejob回调的effect.run

来到了effect.run这个方法中，可以看到其实这个函数属于ReactiveEffect类



![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/196be4dd09224ce5a8439eaef0a10bfd~tplv-k3u1fbpfcp-watermark.image?)

看到run里面调用了this.fn(),这个fn其实在ReactiveEffect类的构造函数里，可以得知其实就是之前我们看到的new ReactiveEffect里面的componentUpdateFn，所以最终我们走的是组件更新函数。componentUpdateFn里面获取最新的vnode 虚拟dom进行patch，渲染成真实dom，那么整个更新流程就完成了

