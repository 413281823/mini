---
theme: channing-cyan
---
# 前言
  大家好！我是乾阳，最近在面试，发现外面的面试官都学聪明了，都不爱问你响应式了，喜欢问你vue3的初始化的流程。搞得我不得不🧐一下子
  ### 调试环境
  
```js
    git clone https://github.com/vuejs/core.git
```
拉取下来之后pnpm安装一下
执行

```js
pnpm serve
```
然后打开

```js
http://localhost:55018/packages/vue/examples/composition/todomvc
```
就是我们喜闻乐见的todos，打开我们的开发者调试器

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/405c943bff1149efbf1a74b99e5a5d28~tplv-k3u1fbpfcp-watermark.image?)
如果不出意外的在93行我们可以看到一个非常熟悉的东西 *createApp*,那这里就是初始化的开始，我们打上断点刷新我们的网页进入断点，在进入下一步就会进入我们的**runtime-dom/src/index.ts**

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/73775850256f473893454258a35a1691~tplv-k3u1fbpfcp-watermark.image?)

就会看到我们createApp函数，可以🎉一下了，起码我们找到了入口，接下来就是看调用栈看看这个函数执行了什么。
接下来我们开始下一步，很直接的看调用，其他我们不关心

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0ad4f1f1239b4b269441adabfa5647e0~tplv-k3u1fbpfcp-watermark.image?)

这里清晰的看到我们调用了ensureRenderer函数，结合createApp源码

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bc276071825c42b083122e6ca70fbc5a~tplv-k3u1fbpfcp-watermark.image?)

可以发现其实createApp是借助了这个函数去创建了一个app，在下面就到mount挂载了，那我们就直接进入ensureRenderer去看看发生了什么？

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/96ebaf558e0846d29fa7f5bba1019332~tplv-k3u1fbpfcp-watermark.image?)

这个函数里面我们看到了renderer并且做了逻辑判断，如果没有则创建一个renderer，既然是在挂载之前那么这个应该就是渲染器（我百度的），一开始我们是没有渲染器的，那么他就会去创建一个渲染器，渲染器把我们的虚拟dom渲染成真实的dom这个过程叫做挂载，渲染器怎么知道把真实dom挂载到哪里呢？那就是传入一个挂载点，渲染器接收一个挂载点作为参数，用来指定具体挂载的位置。那就是我们一开始代码里面的*app.mount('#app')* 这里我们就可以知道，其实他接收的挂载点其实是一个真实dom，把这个dom作为容器放入我们需要渲染的内容。

  那到现在来说流程就开始清晰了，createApp其实就是创建一个renderer并且根据我们传入的容器执行渲染。
  ### createRenderer
  接下来我们就去看看renderer是怎么个过程，我们把断点打到
  
![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a54427f508d24c1f80a2afcfe6b979fb~tplv-k3u1fbpfcp-watermark.image?)

然后发现他又返回了一个函数，没事我们接着走，然后我就进入到了一个很长的文件里面了，没关系我们不用理解其相关的实现，只要看他return的东西就好了，我们会在2359行看到

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/af23e7a064274d06b7350105544dc832~tplv-k3u1fbpfcp-watermark.image?)
又出现了一个工厂函数，我们在2362行打上断点，看看这个东西返回了个啥

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7209f81da4f04176be0f754501f2439d~tplv-k3u1fbpfcp-watermark.image?)
我们往下拉会看到很多我们非常熟悉的方法use，mixin等

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/47b1cdef7da74d50918d39452ca225e2~tplv-k3u1fbpfcp-watermark.image?)
那么毫无疑问这里才是创建我们app实例的地方，好家伙。。。
但是注意⚠️一下我们此行的目的只是为了摸清vue3的初始化过程到底经历了什么，所以我们不要跑偏，既然我们找到了创建app实例的地方，那么我们就能找到关键的mount，我们接着往下看

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1066248ba73a4d60afe71d8313e48e72~tplv-k3u1fbpfcp-watermark.image?)
这里我们看到了mount方法，我们在286打上断点，他这里写得很明显了是否挂载，我们既然是第一次进来的那么一定没有挂载，进来然后看到vnode这么多年了大家应该都知道了vnode就是虚拟dom，这里应该是创建虚拟dom的地方,然后我们会又一次来到分叉口

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f786b48171b346d1b5f01152216bfb38~tplv-k3u1fbpfcp-watermark.image?)
这里应该是判断一下我们是什么渲染模式，我们客户端渲染应该走的render，进入render函数

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/82aec20ef5c94093ae57defe88c36522~tplv-k3u1fbpfcp-watermark.image?)
这里我直接步入了，可以看到和我们上面的想法一致第二个参数传入div#app，这个patch就是一个虚拟dom转真实dom并且追加到宿主#app中。
    至此其实整个vue3实例创建，挂载的流程，我们其实已经摸清楚了，具体实现有待我去深究。