# Classes

- `extends` 继承

- 类方法属性默认是`public`状态的

- `private`私有属性方法不能在定义类之外地方访问(包括子类)

- `protected`保护属性方法和私有比起来就是多了一个能在子类中访问

- 构造函数`protected`

  - 这么做以后这个类只能在子类或者自己的`main()`里面实例化

    ```typescript
    class Person {
        protected name: string;
        protected constructor(theName: string) { this.name = theName; }
    }
    
    // Employee can extend Person
    class Employee extends Person {
        private department: string;
    
        constructor(name: string, department: string) {
            super(name);
            this.department = department;
        }
    
        public getElevatorPitch() {
            return `Hello, my name is ${this.name} and I work in ${this.department}.`;
        }
    }
    
    let howard = new Employee("Howard", "Sales");
    let john = new Person("John"); // Error: The 'Person' constructor is protected
    Readonly modifier
    ```

- `readonly`只读属性

- `static`静态属性 存在于类本身上而不是实例上(不需要实例化就能访问?)

- ts中的类可以直接作为类型来使用

  ```typescript
  class Gee {
      green: string;
      constractor(massage: string) {
        	this.green = massage;
      }
  }
  
  let gee: Gee; //HERE!
  greeter = new Gee("JC");
  ```

- 

