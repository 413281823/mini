#### 学习vue3源码的方法

- 写上一段简单的vue3代码
- 🤔思考这段代码是如何跑起来的，如何结合源码去理解。
- createApp vue3是如何初始化
- app.mount（）vue3是如果挂载到元素上的
- 数据是何时变化的
- 更新
  - 异步更新策略
  - patch细节
  - 搭建源码调试环境
    - 拉取vue3源码 
    - 安装依赖
    - pnpm run dev
    - pnpm serve
    - 打开vu e/example/compostion/todomvc.html
    - 开发者工具source
    - 进入todos文件夹
    - 93行createApp断点
    - 刷新进入右键进入目标文件。

