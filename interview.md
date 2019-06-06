# SEO

1. 在seo权重中 `title` > `description` > `keywords` 不同页面`title`不同 `description`概括这个页面干什么的不堆砌关键字 `keywords`列举重用关键字

2. 语义化html代码 使用恰当的html标签 class名 使用html5中的语义化标签 `header` `footer` `article` ...
3. 重要内容放在html代码最前面 seo从上至下抓取
4. js输出的内容无法被抓取
5. 不使用`iframe`
6. 图片加`alt`属性
7. 网站速度

# 跟踪方法(保持登陆状态)

1. `cookie` 2. `session` 3. `url rewrite` 在页面每个超链接中加上sessionid语句 4. `隐藏input` 使用一个用户不可见的input在form提交的时候提交信息 5. ip地址

# img的title和alt

1. `title`是所有元素都可以使用的一个标签属性 鼠标移动上去时会显示
2. `alt`是`img`特有属性 描述图片 无法加载时显示

# doctype

1. `<!doctype>`声明在文档头部在`<html>`前
2. `<!doctype>`作用是
   1. 告诉浏览器当前html版本 
   2. 使用标准模式还是兼任模式渲染
   3. HTML4.01基于SGML 声明DTD保证正确渲染 HTML5不基于SGML 不用指定DTD
3. **HTML 5**: `<!doctype html>`

# HTTP method

1. **GET**请求服务器发送某个资源

2. **HEAD**不返回请求的实体只返回头部分(response head) 检测链接可用性 检测网友是否被修改

3. **POST**向服务器输入数据 通常用于将表单中的数据发送给服务器

4. **OPTIONS**查询服务器支持哪些方法或特殊资源的访问

5. **DELETE**请求服务器删除URL指定的资源

6. TCP的三次握手

   1. 客户端发送一个TCP的**SYN=1，Seq=X**的包到服务器端口
   2. 服务器发回**SYN=1， ACK=X+1， Seq=Y**的响应包
   3. 客户端发送**ACK=Y+1， Seq=Z**

7. TCP连接建立后再发送HTTP请求 HTTP基于TCP

8. HTTP request报文结构

   1. 第一行是: **请求方法**. **URL**, **版本协议**(HTTP 1.1), **CRLF**(分隔符)

      ```http
      GET /Protocols/rfc2616/rfc2616-sec5.html HTTP/1.1
      ```

   2. 第一行之后是**请求头**:  **general-header**, **cookie**, **User-Agent**之类的东西

   3. 请求头之后是消息实体("name=qiu&age=24")这样的传送给服务器的消息

9. HTTP response结构

   1. 首行包括: **版本协议**, **状态吗**, **状态描述**, **CRLF**

      ```http
      HTTP/1.1 200 OK
      ```

   2. 首行之后是响应头: **通用头部**, **响应头**(Set-Cookie, Content-Type, Content-Length), **实体头**(实体是服务器回应的内容)

# 网站性能优化

- 前端
  - 减少HTTP请求: **合并文件** ..
  - 避免多余的重定向
  - 使用Ajax缓存
    1. 非必要组件延迟加载
    2. 必要组件提前加载
  - 减少DOM元素数量
  - 将资源放到多个域下 多服务器多个域
  - 不使用iframe
  - 不要404?
- 后端
  - 使用CDN
  - 添加Expires响应头让浏览器缓存信息
  - 对组件进行Gzip压缩
  - Ajax使用GET方法
  - 减少Cookie大小
  - 将样式表放到页面顶部
  - 不使用css表达式 不如使用js控制样式
  - 将`<script>`标签放到页面底部

# HTTP响应码

- 1XX信息状态码
  - 100 Continue 请求客户端继续发送请求  请求内容一部分被服务器接收
- 2XX 成功状态码
  - 200 OK 请求成功 请求所希望的响应头和数据返回
- 3XX 重定向
  - 302 Found
- 4XX客户端错误
- 5XX服务器错误

# CSS

- `display: none` 与 `visibility: hidden` 区别

  - `display: none`让元素从渲染树中消失 不占据任何空间 `visibility: hidden` 将元素从渲染树中消失 但是任然占据空间
  - `display: none`是非继承属性 子元素消失是因为渲染树消失 `visibility: hidden` 是继承属性 资源数消失是因为继承了 hidden
  - 修改元素的display通常造成文档重排 visibility实惠造成本元素的重绘

- `link` 与 `import`

  - `link`是HTML的方式, `import`是css的方式

  - `link`支持最大限度的并行下载, `import`嵌套过多会导致串行下载

  - `link`可以给用户使用候选样式 再浏览器查看中选择样式

    ```html
    <link rel="stylesheet" type="text/css" href="1.css" title="red">
    <link rel="alternate stylesheet" type="text/css" href="2.css" title="yellow"> // 候选样式
    ```

  - `link`对老浏览器的支持比`import`好

- 用哪个图片格式

  - GIF
    - 适合动画
  - JPEG
    - 适合照片
  - PNG
    - 适合图标, 背景, 按钮

- css继承属性

  - font-family
  - word-break
  - letter-spacing(单字间隔)
  - text-align
  - text-rendering
  - word-space(单词间隔)
  - line-height
  - color
  - visibility
  - cursor

- 容器包含浮动元素清理浮动

  - 在容器的最后添加一个额外元素并设置为`clear: both`

    ```html
    <div>
        <floatElement></floatElement>
        <div style="clear: both"></div>
    </div>
    ```

  - 设置容器元素的伪元素清除浮动

    ```css
    .container:after {
        clear: both
    }
    ```

- 块级格式化上下文(BFC)

  - BFC的作用

    - BFC不会与浮动元素重合(不会被浮动元素盖住)

    - BFC区域中的子margin box元素左边与BFC区域的左边接触
    - BFC计算高度浮动元素也计算(清除浮动)
    - 同一个BFC里面的box元素垂直margin会重叠 使用两个不同的BFC就不会重叠
    - BFC里面的元素不会影响到外部元素 外部元素也不会影响BFC里面的元素

  - 如何产生一个BFC区域

    - 根元素(`<body>`)默认是BFC
    - float属性不是none
    - position的值是absolute fixed
    - display为inline-block, table-cell, table-caption, inline-flex
    - overflow不是visible(常用于生成一个BFC区域)

  ### 元素包含块(定位基准元素)

  - 根元素的包含块叫初始包含块
  - `position`为`relative`和`static`的元素就是最近的那个祖先元素
  - `position: fixed`包含块为viewport视窗
  - `position: absolute`包含块是祖先元素中最近的一个`position:relative`元素. 如果祖先元素中没有`relative`元素, 包含块就是**初始包含块**

  ### 水平居中元素

  - 如果元素是inline元素父元素设置`text-align: center`
  - 元素是block元素
    - 为元素设置宽度
    - 设置margin为auto
    - IE6上父元素需要设置`text-align:center`
  - 绝对定位元素
    - 为元素设置宽度
    - `left: 50%`
    - `margin-left: 0.5*width`
  - 绝对定位元素2
    - 设置宽度
    - `left: 0; right: 0`
    - `margin 0 auto`

  ### DOM元素的element.getAttrbute(propName)与element.propName

  - e.getAttrbute() 返回元素上一个指定的属性值。如果指定的属性不存在，则返回  `null` 或 `""`（空字符串）
  - e.propName浏览器解析元素后生成的对象中访问属性 浏览器没有在对象中生成的属性无法访问

  ### offsetWidth/offsetHeight,clientWidth/clientHeight与scrollWidth/scrollHeight的区别

  - offsetWidth/offsetHeight包含**content + padding + border**
  - clientWidth/clinetHeight包含**content + padding**不包含滚动条
  - scrollWidth/scrollHeight包含content + padding + 溢出内容高度(子元素内容溢出的高度也被计算在内)

  ### focus/blur与focusin.focusout

  - focus不冒泡 focusin冒泡
  - focus/blur兼容性好

  ### mouseover/mouseout与mouseenter/mouseleave

  - mouseover/mouseout冒泡事件 mouseenter/mouseleave不冒泡 
  - mouseenter/mouseleave向上级的每一个元素发送一个事件可能会拖累性能  mouseover/mouseout是冒泡事件只会发送一个事件一直冒泡到处理程序或者根目录 建议使用mouseover/mouseout

  ### sessionStorage, localStorage, cookie

  - 都保持在客户端, 有大小限制, 同源限制

  - cookie在请求服务器时发送到服务器(任何同源请求). 服务器可修改cookie

  - cookie有path概念 子路径可以访问父路径cookie 父路径不能访问子路径cookie

    1. Cookie Domain

       ```domain表示cookie所在的域将限制什么域名可以访问这个cookie 如网站www.test.com/test/test.aspx，那么domain默认为www.test.com。而跨域访问，如域A为t1.test.com，域B为t2.test.com，那么在域A生产一个令域A和域B都能访问的cookie就要将该cookie的domain设置为.test.com；如果要在域A生产一个令域A不能访问而域B能访问的cookie就要将该cookie的domain设置为t2.test.com。```

    2. Cookie Path

       ```可以在设置cookie的时候同时设置path属性, path设置为/dd那么这个cookie只能在域名的/path页面下才能访问到,其他页面无法访问.path设置为/时这个域下的任何页面都能访问这个cookie```

  - cookie具有有效期超过之后浏览器自动删除

  - localStorage的修改会在其他同源(same domain)窗口出发update事件

  - cookie有secure属性要求HTTPS传输

  - 浏览器不能保存超过300个cookie, 单个服务器不能超过20个, 每个cookie不能超过4k, web storage最大5m

  ### 跨域通信

  - 同域
    1. 协议相同
    2. 域名相同
    3. 端口相同
  - 跨域通信: js执行DOM操作 通信如果与当前窗口的域不同, 浏览器为了安全会阻止跨域操作 以下是跨域操作方法
    - 简单单项通信(?)`<img>`, `<script>`, `<link>`元素通过src link设置跨域url可行
    - [JSON](https://www.cnblogs.com/dowinning/archive/2012/04/19/json-jsonp-jquery.html)跨域通通信
    - 服务器代理请求数据返回给客户端
    - 跨域请求数据再Response Header中添加`Access-Control-Allow-Origin: *`

  ### javascript数据类型

  六种基本类型

  - undefined	
  - null
  - string
  - boolean
  - number
  - symbol

  一种引用类型

  - object

  ### 闭包

  **闭包是在某个作用域中定于的函数, 这个函数可以访问这个作用域的变量** 闭包作用域链:

  1. 函数本身作用域
  2. 函数定义时的作用域
  3. 全局作用域

  闭包的用途:

  1. 创建特权方法访问特定作用域

     ```javascript
     const c = () => {
         let a = 0;
         return () => {
             a = a + 1;
             console.log(a)
         }
     }
     const returned = c();
     returned(); // 1
     returned(); // 2
     ```

  2. 事件处理程序以及回调函数(处理函数和回调函数可以访问定义作用域的变量)

  ### javascript定义函数

  1. [函数声明表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function)
  2. [function操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function)
  3. [Function 构造函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function)
  4. [ES6:arrow function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/arrow_functions)

  ### localStorage和sessionStorage

  - localStorage永久有效, sessionStorage有效期为顶层窗口关闭
  - 同域文档可以读取并修改localStorage修改后其他窗口将收到一个update事件, sessionStorage只允许一个窗口访问

  ### cookie操作

  - cookie会自动在浏览器和服务器通信时传输
  - cookie有名, 值, max-age, path, damain, secure
  - cookie未设置有效期在关闭浏览器后数据将会丢失

  ### javscript定义对象

  - 字面量 `let obj = {}`
  - 构造函数 `let obj = new Object()`(不推荐使用)
  - Object.create() `let obj = Object.create()`(不推荐使用)

  ###  ===三等运算符判断流程

  - 如果两个值类型不同不相等
  - 两个值都是null或undefined相等
  - 两个值都是ture后者false相等
  - NaN不与任何东西相等
  - 两个数字数值相等他们就相等`-0 === 0 //ture`
  - 如果他们指向相同的对象他们相等, 不同不相等

  ### ==流程

  - 值类型相同按照===进行比较
  - 一个值是null一个是undefined他们相等(先转换类型再比较)
  - 布尔类型比较时转换**ture转换为1**, **false转换为0**进行比较

  ### 对象转换为字符串

  1. 对象如果有toString方法调用它, 如果返回一个原始值(primitive value: string number boolean)这个值就是结果
  2. 如果对象没有toString或者返回不是原始值, 使用valueOf方法, 如果有调用, 返回原始值被作为结果
  3. 不能从toString和valueOf中获得原始值抛出一个`TypeError`

  ### 对象转换为数字

  1. 首先调用valueof方法返回原始值
  2. 否者调用toString
  3. 否者报错`TypeError`

  ### >, < , <= ,>=操作符规则

  比较运算符支持所有数据类型但是比较只支持数字和字符串, 比较其他类型的时候先转换类型再比较

  1. 需要转换的是对象先调用valueOf再调用toString方法返回原始值转换失败报错
  2. 转换后两个都是字符串按照字母顺序比较
  3. 如果有一个不是字符串将两个转换为数字比较

  ### + 运算符

  1. 操作的是对象先转为原始值
  2. 如果有一个被操作的数据类型是字符串 全部转为字符串
  3. 否则全部转为数字执行加法

  ### 函数arguments变量

  - argument是所有函数都包含的一个局部变量(只能再函数内部访问), 是类数组对象, 可以被覆盖
  - 转换为数组`let args = Array.prototype.slice.call(arguments, 0)`

  # 事件循环
  
  主线程首先执行同步任务, 同步任务队列全部执行完毕之后, 才去执行异步任务队列.
  
  `setTimeout(fn, 0)`会在异步队列的尾部插入一个任务, 所以他会等所有同步任务执行完再等所有现有的异步队列执行完再执行.
  
  
