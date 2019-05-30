## Vue + Typescript 笔记

### 报错: Element implicitly has an 'any' type because type 'Set' has no index signature

- what is index Signatures(对象属性索引)

  对象的索引可以是任何字符串. 但是也可以传入一个对象作为索引, javascript会调用对象`toString`方法并将结果作为索引值. [例子](https://basarat.gitbooks.io/typescript/docs/types/index-signatures.html) : 

  ```ts
  let obj = {
    toString(){
      console.log('toString called')
      return 'Hello'
    }
  }
  
  let foo:any = {};
  foo[obj] = 'World'; // obj对象作为索引所以toString被调用 log: toString called
  console.log(foo[obj]); // toString called, World
  console.log(foo['Hello']); // World
  ```

- typescript index signature / ts中的索引

  在JS中将对象作为索引的时候会隐式的调用对象的`toString`方法, 而TS中对象直接作为索引会报错`ERROR: the index signature must be string, number ...` 这时需要显示的调用对象的`toString`方法来返回字符串作为索引: 

  ```ts
  let obj = {
    toString(){
      return 'Hello'
    }
  }
  
  let foo:any = {};
  
  // ERROR: the index signature must be string, number ...
  foo[obj] = 'World';
  
  // FIX: TypeScript forces you to be explicit
  foo[obj.toString()] = 'World';
  ```

  这样做的原因是V8引擎中对象默认的`toString`方法经常会返回`[object object]`, 你肯定不想用这样一个字符串作为索引的(awful).

  数字可以直接作为索引: 

   	1. 数组对象需要数字作为索引.
   	2. 数字的默认`toString`方法返回的是字符串数字, 比起`[object object]`好很多. `console.log((2).toString()); // "2"`

  总结: 

  > TS的index signatures(索引)必须是数字或者字符串





### 解决报错

```ts
   export default class Test extends Vue {
        set_something(name: string, value: any):void {
            this[name] = value
        }
    }
```

报错原因是TS无法推导`this`的属性的索引类型是什么, 所以需要手动指定索引类型: 

```ts
   export default class Test extends Vue {
       set_something(name: string, value: any):void {
           (this as { [index: string]: any })[name] = value
       }
   }
```

