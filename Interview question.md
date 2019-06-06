### 面试题:

1. 本地存储有哪些解决方案:

   Cookie, localStorage, SessionStorage, UserData

2. ```ts
   var User = {
   	count: 1,
   	getCount: function() {
   		return this.count;
   	}
   }
   console.log(User.getCount()) // 1
   var func = User.getCount;
   console.log(func()) // undefined
   # 上面输出1是因为隐式绑定this为User
   # 下面输出undefined是因为这样调用getCount函数会将this绑定到window对象上
   ```

3. 闭包是什么, 有什么特性, 对页面有什么影响?

   闭包是在某个作用域中定义的函数, 这个函数可以访问作用域里的变量方法.

   特性:

   1. 封闭性: 外界无法访问闭包内部数据.
   2. 持久性: 闭包函数内的变量方法不会被销毁掉, 会被保存在内存中, 等待下次调用.

   优点:

   1. 减少全局变量
   2. 减少函数传参
   3. 封装

4. 对象拷贝

   *深拷贝, 浅拷贝*

   深拷贝是将对象放到一个新的内存中，两个对象的改变不会相互影响.

   浅拷贝是对象共用一个内存地址，对象的变化相互影响.比如常见的赋值引用就是浅拷贝.

   `Object.assign`

   当对象内有其他对象引用时`Object.assign`只会拷贝这个对象的引用, 所以是浅拷贝. 没有对象引用时就是一个深拷贝了.

   优点:

   1. 能实现函数拷贝.
   2. 需要拷贝的对象内没有其他对象引用时的深拷贝.

   缺点:

   1. 需要拷贝的对象内有其他对象引用时只能拷贝引用.

   `JSON.parse(JSON.stringify(srcObj))`

   这个方法可以深拷贝对象内的所有属性包括对象引用,

   但是只能拷贝能被json表示的数据结构.

   优点:

   1. 对比`Object.assign`能实现对象的深拷贝.

   缺点:

   1. 只能拷贝Json表示的数据, 无法拷贝对象的函数/函数引用.

   想使用完整的深拷贝建议使用lodash的deepClone.

   

   

   