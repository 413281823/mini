#### vue3初始化调用栈过程

1初始化的过程中，一开始执行了mount。

2发现现在是真实的dom环境，调用app.mount找到当前的容器

### vue3首次pach



Runtime-dom/src/index.ts

定义了入口文件

导出了createApp函数，也就是我们的createApp