###  装饰器

vue-cli生成的typescript项目中使用了装饰器. 之前从来没有见过今天记录一下.

阮一峰大佬的es6手册里有介绍[装饰器](http://es6.ruanyifeng.com/?search=super&x=0&y=0#docs/decorator): 装饰器是用来修改类行为的, 并且这个修改发生在代码的编译阶段, 这说明装饰器是编译阶段就执行的函数.

```typescript
@testable
class MyTestableClass {
  // ...
}

function testable(target) {
  target.isTestable = true;
}

MyTestableClass.isTestable // true
```

上面这个装饰器为`MyTestAbleClass`类添加了一个`isTestAble`静态属性. 装饰器函数的第一个参数就是被装饰的类. 装饰器可以被传入不同的参数来给类进行不同的操作:

```typescript
function testable(isTestable) {
  return function(target) {
    target.isTestable = isTestable;
  }
}

@testable(true)
class MyTestableClass {}
MyTestableClass.isTestable // true

@testable(false)
class MyClass {}
MyClass.isTestable // false
```

装饰器也可以添加实例属性(静态属性和实例属性的区别是静态属性不需要实例话就能访问实例属性需要实例化后才能访问)通过操作目标类的`prototype`.

```typescript
function testable(target) {
  target.prototype.isTestable = true;
}

@testable
class MyTestableClass {}

let obj = new MyTestableClass();
obj.isTestable // true

//上面的操作等于

class MyTestableClass {}
MyTestableClass.prototype.isTestable = true; // or Object.assign(MyTestAbleClass.prototype, {isTestAble: true})
let obj = new MyTestAbleClass();
obj.isTestAble // true
```

### 装饰类方法

```ts
class Person {
  @nonenumerable
  get kidCount() { return this.children.length; }
}

function nonenumerable(target, name, descriptor) {
  // descriptor对象默认值如下
  // {
  //   value: kidCount,
  //   enumerable: ture,
  //   configurable: true,
  //   writable: true
  // }
  descriptor.enumerable = false;
  return descriptor;
}
```

装饰器修饰类方法的时候第一个参数是类的原型对象上例是`Person.prototype`, 第二个参数是需要修饰的属性名, 第三个参数是该属性的描述对象. 描述对象决定了这个属性是否可以遍历(enumerable 默认 true), 是否可以修改(writable 默认 true), 是否可以修改该属性的描述对象或被删除(configurable 默认 true), 该属性(方法)的值(value 默认 undefined).

例子: 添加一个`@log`装饰器, 输出日志

```ts
class Math {
  @log
  add(a, b) {
    return a + b;
  }
}

function log(target, name, descriptor) {
  var oldValue = descriptor.value;

  descriptor.value = function() {
    console.log(`Calling ${name} with`, arguments);
    return oldValue.apply(this, arguments);
  };

  return descriptor;
}

const math = new Math();

math.add(2, 4); // Calling add with 2, 4
```

这个修饰器修改了`Math.add`方法的描述对象的value所以`add`方法变成了修饰器中指定的匿名函数. ps. 描述对象的value属性控制着这个方法/属性的值. [描述对象参考页面](<https://javascript.ruanyifeng.com/stdlib/attributes.html>).

如果函数有多个装饰器会由内到外进入, 由内向外执行

```ts
function dec(id){
  console.log('evaluated', id);
  return (target, property, descriptor) => console.log('executed', id);
}

class Example {
    @dec(1)
    @dec(2)
    method(){}
}
// evaluated 1
// evaluated 2
// executed 2
// executed 1
```

装饰器不能用于函数是因为函数会被提升:

```tsx
var counter = 0;

var add = function () {
  counter++;
};

@add
function foo() {
}
```

实际代码: 

```ts
@add // 因为装饰器实在编译阶段执行, 编译阶段的add是undefined所以装饰器无法执行
function foo() {
}

var counter;
var add;

counter = 0;

add = function () {
  counter++;
};
```

因为装饰器在编译阶段执行, 实际代码中编译阶段的add是undefined所以装饰器无法执行.