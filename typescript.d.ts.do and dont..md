# Do's and Don'ts

1. General types

   - 不要使用 `Number` `String` `Boolean` `Object` 对象作为类型
   - 使用`number` `string` `boolean`

2. 回调函数类型

   1. 回调函数返回值类型

      - 不要使用`any`作为返回值类型
      - 使用`void`作为返回值类型

   2. 回调函数的选项参数

      - 因为总是允许提供一个接收较少参数的回调函数 所以就不需要再回调函数中写选项参数了

        ```typescript
        /* WRONG */
        interface Fetcher {
            getObject(done: (data: any, elapsedTime?: number) => void): void;
        }
        /* OK */
        interface Fetcher {
            getObject(done: (data: any, elapsedTime: number) => void): void;
        }
        ```

   3. 重载与回调函数

      ```typescript
      不要因为回调函数参数个数不同而写不同的重载
      /* 错误 */
      declare function beforeAll(action: () => void, timeout?: number): void;
      declare function beforeAll(action: (done: DoneFn) => void, timeout?: number): void;
      应该只使用最大参数个数写一个重载
      /* OK */
      declare function beforeAll(action: (done: DoneFn) => void, timeout?: number): void;
      ```

3. 函数重载

   1. 不要把任何一般(any number string object etc.)重载放到特定类型的重载前 一般类型的重载会被优先匹配

      ```typescript
      /* WRONG */
      declare function fn(x: any): any;
      declare function fn(x: HTMLElement): number;
      declare function fn(x: HTMLDivElement): string;
      
      var myElem: HTMLDivElement;
      var x = fn(myElem); // x: any, wat?
      /* OK */
      declare function fn(x: HTMLDivElement): string;
      declare function fn(x: HTMLElement): number;
      declare function fn(x: any): any;
      
      var myElem: HTMLDivElement;
      var x = fn(myElem); // x: string, :)
      ```

   2. 使用联合类型

      ```typescript
      /* WRONG */
      interface Moment {
          utcOffset(): number;
          utcOffset(b: number): Moment;
          utcOffset(b: string): Moment;
      }
      /* OK */
      interface Moment {
          utcOffset(): number;
          utcOffset(b: number|string): Moment;
      }
      尽可能使用联合类型代替多个重载
      ```

      