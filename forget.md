## 容易被忘记的事情

### `new`操作符

- JS中没有构造函数的, 有的只是构造调用, 任何函数都可以被构造调用只要在前面加上`new`操作符
- `new`放在函数前面的会发生什么
  1. 一个新对象被创建出来
  2. 新对象的`Prototype`链接着函数
  3. 被调用函数中的`this`被设置为新创建的对象
  4. 除非函数返回自己的备用对象, 否者`new`调用将返回新创建的函数

### `[[Prototype]]`

- `[[Get]]`操作会在对象上寻找属性, 如果找不到就向上到`[[Prototype]]`链接的对象上寻找属性直到原型链顶部

- 如果到达原型链顶部还是没有需要寻找的属性那么返回`undefined`

- `for..in`会像`[[Get]]`操作一样会查找原型链

- 原型链的顶部是`Object.prototype`

- 设置和 shadowing Properties

  ```javascript
  myObject.foo = "bar";
  ```

  - 如果`myObject`已经有了一个叫`foo`的属性那么会直接修改`foo`的值
  - 如果`foo`没有直接在`myObject`中那么将在`[[Prototype]]`中寻找(类似`[[Get]]`操作),如果在整个原型链上都没有找到一个叫`foo`的属性那么直接把`foo`添加为`myObject`的属性并赋值
  - 如果`myObject`有`foo`属性而且更高层的原型链中也有`foo`属性那么高层的将被遮盖, 因为`myObject.foo`查找只会找到位于原型链最低处的`foo`
  - 如果在`myObject`中没有`foo`更高层的原型链中有那么会出现一下几种情况:
    1. 在原型链更高处找到了`foo`并且没有被标记被只读属性`read-only`那么, `myObject`会被添加一个新属性`foo`并指定值, 这样形成的了一个遮盖
    2. 在原型链更高处被找到但是被标记为只读属性了这是就不能创建一个`myObject.foo`来遮盖了. 如果运行在`strict mode`下还会报错其他时候不会报错

### 事件流

事件流有两种:

![img](https://pic1.zhimg.com/80/v2-bf3b8dbab027713a2b21b9e8a5b7a6c4_hd.jpg)

一个事件发生后传播有三种阶段:(目标节点就是发生事件的元素)

1. 事件捕获阶段: 从`window`对象传播到目标节点上.
2. 目标阶段: 当事件传播到目标节点上时触发.
3. 冒泡阶段: 事件传播到目标节点后又开始向上传播到`window`对象, 这个阶段被称为事件冒泡阶段.
4. `addEventListener`的第三个参数`useCapture`决定事件处理器在事件的捕获阶段处理还是在事件冒泡阶段处理. `true`在事件捕获阶段就处理, `false`事件冒泡时再处理, 默认值`false`.

- 事件代理

  事件会再冒泡阶段向上传播到父元素, 所以可以把事件监听设置到父元素上, 父元素处理统一处理多个子元素的事件.

  如果一个父元素下的所有1001个需要监听`click`事件的子元素有两种处理方式:

  1. 为每个子元素添加事件监听. 这样做会添加1001个事件监听消耗很多内存.
  2. 在父元素上添加事件监听等子元素事件冒泡的时候将事件代理. 仅添加了一个事件处理消耗较少内存.
  3. `evnet.stopPropagation()`可以阻止事件继续传播 捕获事件 冒泡事件都可以

- e.cancelable e.defaultPrevemted
  - `e.cancelable`返回一个布尔值表示该事件是否可以使用`e.preventDefault()`取消
  - `e.defaultPrevent`返回一个布朗值, 表示这个事件是否调用过`e.preventDefault()`

- e.currentTarget, e.target

  - `e.currentTarget`属性返回当前所在的节点, 就是执行监听函数绑定的那个点
  - `e.target`返回返回出发事件的那个节点, 就是最初发生事件的节点
  - 在事件传播的过程中`e.target`和`e.currentTarget`属性值是不一样的, 前者总是事件发生的那个节点不会改变, 后者时监听函数绑定的节点是会变的.

  参考链接

  ​	1. <https://javascript.ruanyifeng.com/dom/event.html#toc10>

## export [things] from [lib];

看echart教程时发现的新操作, 属于es6. 作用相当于:

```ts
import things from 'lib';
export things;
```

参考: <https://stackoverflow.com/questions/38077164/es6-export-from-import>.

## ES6 export import 记录

### export

- `export`必须对外提供接口, 必须与模块内部变量建立一一对应关系. 例如:

```js
let m = 1;
export m;  // 报错,m对应的是数字1并没有对应内部变量
export {m}; // 正确, {m}对应了模块内的变量m
```

- `export`输出的值是动态的, 如果内部执行了修改`export`变量的操作, `import`时会取到修改后的值.
- `export`必须在文件的顶级作用域中.

### import

- `import`变量会提升到文件顶部. 
- `from`后面的文件名的`.js`可以省略.
- `import`变量是只读变量.
- `import`是静态执行的, 不能使用表达式和变量, 这些是运行时才能得到结果的.
- `import * from 'lib'` 使用`*`把所有输出值加载到这个对象上, 或者使用 `import {fn} from 'lib'`结构赋值加载fn函数/变量.
- `import`一个`export default`的模块: `import fn from 'lib';`.