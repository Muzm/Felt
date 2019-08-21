# Vue框架原理



Vue实现数据绑定的方式是通过劫持, 核心概念就是通过`Object.defineProperty`来劫持data函数返回的对象的每个属性.

![img2](https://github.com/DMQ/mvvm/raw/master/img/2.png)

## Obsever 监听器

对象的每个属性都可以定义`Getter`(当属性被访问时触发的函数)和`Setter`(当数据被修改时触发的函数). Vue 通过`Object.defineProperty`给需要绑定的对象的每个属性加上`Getter`和`Setter`, 当对象某个属性被修改时就能被监听到. 具体实现参考: [https://github.com/DMQ/mvvm#1%E5%AE%9E%E7%8E%B0observer](https://github.com/DMQ/mvvm#1实现observer).

### 发布-订阅模式

data中每个属性都有一个订阅队列(deps), 里面都是Watcher实例化生成的订阅者, 拥有此队列的属性发生变化时会依次调用队列中的每个订阅者(Watcher)的回调函数. Watcher实例化时需要指定绑定的属性以及回调函数. Vue 模板编译的过程中如遇到事件指令如`v-on:click`编译器就会实例化一个Watcher添加到指定属性的订阅组中, 这样相当于为属性变化注册了一个回调函数.





