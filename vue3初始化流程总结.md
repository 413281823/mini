### vue3初始化流程总结

1. app.mount
2. mount
3. Render 调用mount里面的render方法，
4. patch 调用patch尝试传入根组件的虚拟Dom 并对其进行转换
5. ProcessComponent  如果当前组件类型是一个component 就会执行ProcessComponent
6. MountComponent 当前组件实例的创建 初始化了当前组件 执行下一个副作用方法
7. SetupRanderEffect 执行更新函数 update()
8. 由于改更新方法是异步的 所以会先执行run
9. 在未来的某一个时刻会执行componentUpdateFn 会向下递归根组件的子树
10. 递归调用patch
11. processElement 这次就会执行patch里面的这个方法，因为当前的子树应该是一个html对象 也就是一个element
12. mountElement 创建当前element，如果有孩子
13. mountChildren 创建所有的孩子，对每一个孩子进行patch