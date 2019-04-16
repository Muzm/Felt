# Function in typescript

- 函数参数 返回值类型声明
  - `function(arr1: [type], arr2: [type], ...rest: [type]): [return type]{}`
  - `(arr1: [type], arr2: [type], ...rest: [type]) => [return type]`

- 可选参数必须放在必填参数的后面 `(arr1: string, optional?: string) => void`

- 默认值参数可以放在任何位置

- `this` 在函数被执行的时候才确定,`this`指向哪取决于函数在哪被调用(dynamic scope) , ()=>{}`箭头函数的`this`是在编写代码时就确定的 不会因为在哪调用而受影响(static scope).

- `this`作为参数

  - 在函数在直接使用`this`typescript无法检测它的类型

  - 在函数中设置一个叫`this`的参数并声明类型就有类型了

    ```typescript
    interface Card {
        suit: string;
        card: number;
    }
    interface Deck {
        suits: string[];
        cards: number[];
        createCardPicker(this: Deck): () => Card;
    }
    let deck: Deck = {
        suits: ["hearts", "spades", "clubs", "diamonds"],
        cards: Array(52),
        // NOTE: The function now explicitly specifies that its callee must be of type Deck
        createCardPicker: function(this: Deck) { // HERE!
            return () => {
                let pickedCard = Math.floor(Math.random() * 52);
                let pickedSuit = Math.floor(pickedCard / 13);
    
                return {suit: this.suits[pickedSuit], card: pickedCard % 13};
            }
        }
    }
    
    let cardPicker = deck.createCardPicker();
    let pickedCard = cardPicker();
    
    alert("card: " + pickedCard.card + " of " + pickedCard.suit);
    ```

  - 上面的代码块`desk`的`createCardPicker`方法有一个`this`参数, 类型为`Desk`  `createCardPicker`被调用的时候`this`的类型一定要是`Desk`. 上面作为例子,只能这样调用 `desk.createCardPicker() 隐式绑定` 或者 `createCardPicker.call(desk) 显式绑定`.