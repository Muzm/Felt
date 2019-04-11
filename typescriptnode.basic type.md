# 基本类型

- `any`最好用于不确定的类型的数据上 比如运行时动态生成的内容 第三方库返回的数据等等

- `void` 被声明为这个类型的变量只能被赋值为 `null` or `undefined`

- `null` and `undefined` 有他们自己的类型 被标记为这两种类型的变量可以赋值给任何类型的变量 因为这两个类型是任何类型的子类型

  - `--strictNullChecks` 强制null检测 在ts选项加上这个`null` `undefined`只能被赋值到他们相应的类型和`void`类型上 如果需要让一个`string`变量可以为`null` 使用`string | null` (鼓励加上这个选项少点bug)

- `never` 不能被其他任何类型的变量赋值除了`never`类型

- 类型断言

  - 如果你你比ts更清楚觉得一个变量值的类型应该是什么类型时使用断言

  - "angle-bracket" syntax

    ```typescript
    let someValue: any = "this is a string";
    
    let strLength: number = (<string>someValue).length;
    ```

  - `as `syntax

    ```typescript
    let someValue: any = "this is a string";
    
    let strLength: number = (someValue as string).length;
    ```

    

