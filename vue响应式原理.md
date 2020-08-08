# vue响应式原理

## vue2.x响应式原理

##### 1：响应式原理大致分为两步

通过object.defineProperty劫持数据，收集依赖

当数据被访问或更新时，通知对应的原理去响应视图的变化

##### 2：一图理解响应式原理

![img](https://user-gold-cdn.xitu.io/2020/3/15/170dd8eb2db2de7f?imageslim)





首先明确的是三个类之间的对应关系：**`Observer`与`Dep`是一对一的关系，`Dep`与`Watcher`是多对多的关系**。
 每个类的具体功能如下：深入理解：

- **监听器 Observer**：对数据对象进行遍历，包括子属性对象的属性，利用 Object.defineProperty() 对属性都加上 setter 和 getter。这样的话，给这个对象的某个值赋值，就会触发 setter，那么就能监听到了数据变化。
- **解析器 Compile**：解析 Vue 模板指令，将模板中的变量都替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，调用更新函数进行数据更新。
- **订阅者 Watcher**：Watcher 订阅者是 Observer 和 Compile 之间通信的桥梁 ，主要的任务是订阅 Observer 中的属性值变化的消息，当收到属性值变化的消息时，触发解析器 Compile 中对应的更新函数。每个组件实例都有相应的 watcher 实例对象，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的 setter 被调用时，会通知 watcher 重新计算，从而致使它关联的组件得以更新——这是一个典型的观察者模式
- **订阅器 Dep**：订阅器采用 发布-订阅 设计模式，用来收集订阅者 Watcher，对监听器 Observer 和 订阅者 Watcher 进行统一管理。

## vue3.0响应式原理

![img](https://user-gold-cdn.xitu.io/2020/6/19/172cbd1109ad6781?imageslim)