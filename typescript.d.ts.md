# Declaration File (d.ts) 声明文件

1. 使用`declare var`声明变量类型

   ```typescript
   declare var foo: number;
   ```

2. `declare let` 和 `declare const` 用法和再javascript中的用法一样

3. `declare function [funcName]([param]: [type]): [return type]` 声明函数类型

4. ```typescript
   declare namespace [objectName] {
       function MakeGreeting(s: string): string;
       let numberOfGreetings: number;
   }
   声明对象类型
   ```

5. 负荷函数 

   ```typescript
   declare function getWidget(n: number): Widget;
   declare function getWidget(s: string): Widget[];
   一个函数的参数类型个对应的返回值不同
   ```

6. 可重复使用的类型(interface)

   ```typescript
   interface GreetingSettings {
     greeting: string;
     duration?: number;
     color?: string;
   }
   
   declare function greet(setting: GreetingSettings): void;
   ```

7. 类

   ```typescript
   const myGreeter = new Greeter("hello, world");
   myGreeter.greeting = "howdy";
   myGreeter.showGreeting();
   ===================
   declare class Greeter {
       constructor(greeting: string);
   
       greeting: string;
       showGreeting(): void;
   }
   ```

   

