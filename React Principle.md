# React 框架原理

## Jsx

jsx是一种能在js中写HTML的语法扩展. 实际运行时其中的HTML会被babel插件`transform-react-jsx`转换为虚拟DOM.

```jsx
let hello = <div className="title">Hello, world!</div>;
// 转换后
let hello = React.createElement(
    'h1',
    { className: 'title' },
    'Hello, world!'
); // 返回一个表示虚拟DOM的对象
```

### React.createElement

```jsx
function createElement( tag, attrs, ...children ) {
    return {
        tag,
        attrs,
        children
    }
}
```

## 虚拟DOM

虚拟DOM其实就是一个用对象表示的DOM. 结构就像这样: 

```js
{
    tag: 'h1', // 虚拟DOM标签名
    attr: { className: 'title' }, // 元素的属性
    "Hello", // 字符节点
    ...OtherChildren  // 其他子节点子节点
}
```

标签内的字符也算做节点, 就叫做[字符节点](<https://developer.mozilla.org/zh-CN/docs/Web/API/Node/nodeType>)(text node). 

`ParentNode.children` 返回ParentNode的子元素组(**HTMLCollection**). `ParentNode.childNode` 返回ParentNode的所有子字节包括字符, 注释以及其他节点.

`ReactDOM.render`将虚拟DOM渲染成实际DOM.

## ReactDOM.render

将虚拟DOM渲染为实际DOM的方法. 具体实现参考: <https://github.com/hujiulong/blog/issues/4>

## Fucntion Component

函数组件被转换成虚拟DOM时tag属性是函数, `ReactDOM.render`渲染时会先将它扩展成类定义组件, 并调用它的render方法渲染(每个React类定义组件都有一个render方法).