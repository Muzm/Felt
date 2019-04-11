# 接口 (interface)

- `readony`只读属性和 `const` 如果需要让一个复杂数据保持不变用 `readony` 单纯的字符串变量之类的使用 `const`

- 索引类型(对象,数组)

  - ``` typescript
    interface StringArray {
        [index: number]: string;
    }
    ```

  - 索引的类型可以时数字或字符串 数字类型索引在索引时会被js转换成字符串索引

  - 对象内的所有数据类型必须和索引声明的一样就时 上面代码块做例子被声明为 `StringArray`类型的对象里所有的数据类型只能是`string` 

    ``` tsx
    interface NumberDictionary {
        [index: string]: number; //这里声明了所有的数据类型都是`number`
        length: number;    // ok, length is a number
        name: string;      // 因为和索引类型不符合所以 error, the type of 'name' is not a subtype of the indexer
    }
    ```

  - 再索引前面加上`readonly`里面的数据就无法被更改

- Class类型

  - `interface`只能声明类的公共属性方法 

  - `class`具有两个类型面 一个`static side` 和 `instance side` 静态面和实例面

    - `implements [interface]`只能声明类的静态面类型`[interface]`中如果有 `new` 操作符尝试声明构造函数参数弧会报错

    - 声明实例面: 

      ```typescript
      interface ClockConstructor {
        new (hour: number, minute: number);
      }
      
      interface ClockInterface {
        tick();
      }
      
      const Clock: ClockConstructor = class Clock implements ClockInterface {
        constructor(h: number, m: number) {}
        tick() {
            console.log("beep beep");
        }
      }
      ```

- `interface` 继承

  - `interface [name] extends [parent-interface]` 

- `interface extends classes` 接口继承对象

  - 如果一个接口继承了对象那么它会拥有这个对象的所有私有和公共属性 这个接口只能被`implements(实施)`到被继承的类上或者它的字类(subclass)上

  - 非子类不能`implements`这个继承了类的接口是因为 非子类(non-subclass)没有这个被接口继承类的属性或者有所有属性但是定义在不同的地方(这些属性不是被定义在被接口继承类中) 所以也不能`implements`.

    ```typescript
    class Control {
        private state: any;
    }
    
    interface SelectableControl extends Control {
        select(): void;
    }
    
    class Button extends Control implements SelectableControl {
        select() { }
    }
    
    class TextBox extends Control {
        select() { }
    }
    
    // Error: Property 'state' is missing in type 'Image'. 这玩意没有继承Control
    class Image implements SelectableControl {
        private state: any;
        select() { }
    }
    
    class Location {
    
    }
    ```

    
