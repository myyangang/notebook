# CSS

参考书籍：

- CSS权威指南（第四版）[豆瓣](https://book.douban.com/subject/33398314/)
- 从0到1 CSS进阶之旅 [豆瓣](https://book.douban.com/subject/35107626/)

# §0 前置知识

## §0.1 在HTML引入CSS

### §0.1.1 `<link>`标签

`<link>`标签必须放在`<head>`元素中。

```htaccess
<link rel="stylesheet" type="text/css" href="./index.css" media="all">
```

其中`<link>`标签涉及的属性有：

- `rel`：资源与文档的关系(Relation)
- `type`：资源的MIME类型
- `href`：资源的路径，可以是绝对URL或相对URL
- `media`：资源的应用场景。

### §0.1.2 `@import`语句

`@import`声明必须放在`<style>`的开头。

```html
<style>
    @import url(index.css)
</style>
```

### §0.1.3 `HTTP Header`

HTTP协议允许在响应头指定加载的CSS文件。

- `Apache`的分布式配置文件`.htaccess`：

  ```htaccess
  Header add Link "</index.css>;rel=stylesheet;type=text/css"
  ```

- `Apache`的全局配置文件`httpd.conf`：

  ```htaccess
  <Directory /var/www/html/site>
  	Header add Link "</index.css>;rel=stylesheet;type=text/css"
  </Directory>
  ```

### §0.1.4 行内样式

```html
<span style="color: red">Hello</span>
```

### §0.1.5 `<style>标签`

将`<style>`标签放在`<head>`内即可。

> 注意：当`<style>`标签有`contenteditable`属性时，`<style>`标签变成一个可以编辑的元素。给`<head>`设置`display: block;`使得元素显示。
>
> ```html
> <html>
>     <head>
>         <style contenteditable id="editme">
>             head {display: block;}
>             style#editme {
>                 display: block;
>                 white-space: pre-wrap;
>                 border: 0.25ch solid rgba(0,0,0,0.33);
>                 font: 1.2rem Consolas, "Courier New", Courier, monospace;
>                 max-height: 11rem;
>                 overflow: auto;
>             }
>             div {
>                 width: 10rem;
>                 height: 5rem;
>                 border: 2px black solid;
>             }
>         </style>
>     </head>
>     <body>
>         <div></div>
>     </body>
> </html>
> ```
>
> 

# §1 CSS语法

样式表由一个或多个规则组成。一个规则由前面选择符(`selector`)和后面的声明块(`declaration block`)组成。声明块之中的声明用分号`;`隔开。

```css
h1 {color: red; background: yellow}
```

## §1.1 注释

CSS支持`/* */`类型的注释，不允许注释嵌套：

```css
/*
	这是多行注释
*/
```

注释不能算作空白字符，因此下面的写法是合法的：

```css
h1 {color: red}
h1 {co/*我是突然闯入的注释*/lor: red}
```

由于历史原因，CSS注释可以使用HTML中的注释标记：

```css
h1 {color: red <!-- 让标题为红色 --> }
```

## §1.2 选择符

### §1.2.1 元素选择符

元素选择符用于选择HTML或XML文档中的元素。

```css
h1 {color: red}
```

### §1.2.2 群组选择符

群组选择符允许将一个样式应用到一组元素上，各个元素之间用`,`分隔：

```css
h1, h2, h3, h4, h5, h6 {color: red}
```

特殊地，使用`*`表示匹配所有元素，称为通用选择符。

```css
* {color: red; font: 18px}
```

> 注意：虽然一组声明的最后一处声明后不必加引号，但是工程中还是推荐加上。否则在其后面添加样式时容易忘记补上，导致新增的一行声明被解析成上一层声明的关键词。

### §1.2.3 类选择符

类选择符`.`用于匹配文档元素中的`class`属性。其之前的字符串指定匹配的元素(通配则置为`*`或空)，之后的字符串指定匹配的`class`属性。在其之后继续加类选择符进行串联，表示同时匹配到多个`class`属性，区分大小写。

```html
<h1 class="warning urgent">Notice</h1>
<h2 class="info">Idea</h2>
<style>
    .warning {font-weight: bold;}
    h2.info {font-style: italic;}
    .warning.urgent{color: red}
</style>
```

### §1.2.4 ID选择符

ID选择符`*`用于匹配文档中唯一出现的`id`属性。其语法与类选择符类似，但是不能串联，区分大小写。

```html
<div id="navbar">
    <a id="title">Welcome</a>
    <span id="currentTime">2023-08-08</span>
</div>
<style>
    #navbar {width: 100%;}
    a#title {font-weight: bold;}
    *#currentTime {color: green;}
</style>
```

### §1.2.5 属性选择符

属性选择符`[]`用于匹配函数某种属性的元素。支持串联。

```html
<div id="loginFrame">
    <input type="checkbox" checked/>
</div>
<style>
    input[checked] {color: red;} /* 筛选所有预先勾选的多选框 */
    *[id] {border: 2px solid red;} /* 筛选所有具有ID属性的元素 */
    input[type][checked] { font-weight: bold; }
</style>
```

属性选择符不仅可以匹配属性名，还可以匹配属性值。如果属性值可能会引起歧义，可以用双引号`"`或单引号`'`包裹：

```html
<a href="https://www.baidu.com">Baidu</a>
<a class="warning-1 urgent">Pending...</a>
<style>
    a[href="https://www.baidu.com"] {...} /* 精准匹配 */
	a[class|="warning"] /* 模糊匹配，匹配以warning或以warning-开头的 */
	a[class~="warning-1"] /* 精准匹配，匹配属性值被空格分割后的单词 */
    a[class*="ing urg"] /* 模糊匹配，匹配子串 */
    a[href^="https://"] /* 模糊匹配，匹配开头 */
    a[href$=".com"] /* 模糊匹配，匹配结尾 */
</style>
```

上面的语法和正则表达式比较相似。实际上，属性选择符也支持正则表达式里的“大小写敏感”模式`i`：

```html
<div id="NavBar"></div>
<style>
    div[id="navbar" i] {color: red;}
</style>
```

属性选择符本身是大小写敏感的，除非属性值本身是HTML/XML规定的关键字：

```html
<input type="checkbox"/>
<style>
    input[type="ChEcKbOx"] {color: red;} /* 匹配成功 */
</style>
```

### §1.2.6 后代选择符

后代选择符` `用于筛选目标元素的所有子元素和子元素的子元素。

```html
<div class="list"><ul>
    <li>Item 1</li>
</ul></div>
<style>
    .list ul li {color: red;}
</style>
```

### §1.2.7 子代联结符

子代联结符`>`与后代选择符` `配合使用，用于筛选目标元素的所有子元素。

```html
<div class="list"><ul>
    <li>Item 1</li>
</ul></div>
<style>
    .list > ul > li {color: red;}
</style>
```

### §1.2.8 紧邻同胞联结符

紧邻同胞联结符`+`与后代选择符` `配合使用，用于筛选目标元素的所有兄弟元素。

```html
<div class="list"><ul>
    <li type="title">Item:</li>
    <li>Item 1</li>
    <li>Item 2</li>
</ul></div>
<style>
    li[type="title"] + li {color: gray;} /* 只为非title设置样式 */
</style>
```

### §1.2.9 一般同胞联结符

一般同胞联结符`~`与后代选择符` `配合使用，用于匹配与目标元素后面的所有兄弟元素，也就是选择后续元素。

```html
<div>
    <h1>Title</h1>
    <a>Item 1</a> <!-- 变成红色 -->
    <a>Item 2</a> <!-- 变成红色 -->
</div>
<style>
    div h1 ~a {color: red;}
</style>
```

### §1.2.10 伪类选择符

伪类选择符`:`用于选择伪类。可以串联使用。关于伪类的具体内容详见[§1.3 伪类](#§1.3 伪类)。

> 注意：伪类的"串联"不同于传统意义上的链式调用。在链式调用中，每个选择符接受到的实参都是上一个选择符的输出结果，而伪类选择符的"串联"表示每一个伪类选择符的实参都是初始传入的。
>
> 例如，`div:first-child:last-child:`表示在所选中的`div`元素中，只有既是`fisrt-child`也是`last-child`的元素才能匹配成功，因此其效果等价于`:only-child`。

### §1.2.11 伪元素选择符

为实现特定的效果，伪元素和伪类都能向文档中插入虚构的元素。伪元素选择符为`::`。

## §1.3 伪类

伪类指的是文档中不一定真实存在的结构，或者某些元素的特定状态，它必须依赖于已有的元素。

### §1.3.1 结构伪类

#### §1.3.1.1 `:root`

`:root`表示文档的根元素。在HTML文档中，它指的就是`<html>`元素。

```html
<html>
	<style>
        :root {border: 1px solid red;}
        html {border: 1px solid red;}
    </style>
</html>

```

#### §1.3.1.2 `:empty`

`:empty`表示没有任何子元素（包括文本元素）的元素。

```html
<div>Hello world</div>
<div> </div>
<div>
</div>
<div><!----></div> <!-- 这个元素会被屏蔽 -->
<div></div> <!-- 这个元素会被屏蔽 -->
<style>
    div:empty {display: none;}
</style>
```

> 注意：`:empty`很容易被滥用成`*:empty`，从而会匹配`<input>`、`<img>`、`<br>`、`<textarea>`这类本身没有子元素的标签。

#### §1.3.1.3 `:only-child`

`:only-child`的作用是：当选定的元素只有一个子元素时，且子元素类型恰好为`:only-child`指定的类型时，选中该子元素。

```html
<html>
    <head>
        <style>
            a[href] img:only-child {border: 2px solid black;}
        </style>
    </head>
    <body>
        <a href="baidu.com"><img src="./avatar.png"><br/></a> <!-- 未选中 -->
        <a href="baidu.com"><img src="./avatar.png">Baidu</a> <!-- 已选中 -->
    </body>
</html>
```

#### §1.3.1.4 `:only-of-type`

`:only-child`的作用是：当选定的元素只有一个`:only-child`指定类型的子元素时，选中该子元素。它与`:only-child`的区别是：`:only-of-type`可以允许选定的元素有多个子元素，只要其中符合指定类型的子元素只有一个就可以；而`:only-child`只允许选定的元素只有一个子元素，且其类型恰好为`:only-child`指定的类型。

```html
<html>
    <head>
        <style>
            a[href] img:only-of-type {border: 2px solid black;}
        </style>
    </head>
    <body>
        <a href="baidu.com"><img src="./avatar.png"><br/></a><!-- 已选中 -->
        <a href="baidu.com"><img src="./avatar.png">Baidu</a><!-- 已选中 -->
    </body>
</html>
```

> 注意：`:only-of-type`牵扯到了父元素和子元素的概念。例如在`div a:only-of-type`中，到底谁是父元素呢？答案是`div`。因此在下面的例子中，两个子元素都不会被选中：
>
> ```html
> <style>
>     p.unique:only-of-type {color: red;}
> </style>
> <div>
>     <p class="unique">Hello</p>
>     <p class="common">World</p>
> </div>
> ```

#### §1.3.1.5 `:first-child`和`:last-child`

`:first-child`用于表示在众多同级的兄弟节点中，排在第一个的，且恰为指定类型的子元素。

`:last-child`用于表示在众多同级的兄弟节点中，排在最后一个的，且恰为指定类型的子元素。

```html
<style>
    p:first-child {color:red;}
    p:last-child {color:green;}
</style>
<p>First</p> <!-- 红色 -->
<p>Second</p>
<p>Third</p> <!-- 绿色 -->
```

#### §1.3.1.6 `:first-of-type`和`:last-of-type`

`:first-of-type`用于表示与指定类型相同的众多兄弟元素中，其中的第一个子元素。

`:last-of-type`用于表示与指定类型相同的众多兄弟元素中，其中的最后一个子元素。

```html
<style>
    p:first-of-type {color:red;}
    p:last-of-type {color:green;}
</style>
<p>First</p> <!-- 红色 -->
<p>Second</p>
<p>Third</p> <!-- 绿色 -->
```

#### §1.3.1.7 `:nth-child()`和`:nth-last-child`

`:nth-child()`用于表示在众多同级的兄弟节点中，排在第$n$个的，且恰为指定类型的子元素。此处$n$从$1$开始计数。

```html
<html>
    <head>
        <style>
            body > div:nth-child(2) {color: red;}
        </style>
    </head>
    <body>
        <div>Hello</div>
        <div>Hello</div> <!-- 红色 -->
        <div>Hello</div>
    </body>
</html>
```

`:nth-child()`还可以接收由自然数$n$构成形如`an+b`的表达式：

```html
<html>
    <head>
        <style>
            body > div:nth-child(2n+1) {color: red;}
        </style>
        <title>让奇数项全都为红色</title>
    </head>
    <body>
        <div>我是奇数</div><div>我是偶数</div>
        <div>我是奇数</div><div>我是偶数</div>
        <div>我是奇数</div><div>我是偶数</div>
    </body>
</html>
```

`:nth-child()`还可以接收`even`、`odd`这两个关键字，分别表示筛选红偶数项和奇数项：

```html
<html>
    <head>
        <style>
            body > div:nth-child(odd) {color: green;}
            body > div:nth-child(even) {color: red;}
        </style>
        <title>让奇数项全都为绿色,偶数项为红色</title>
    </head>
    <body>
        <div>我是奇数</div><div>我是偶数</div>
        <div>我是奇数</div><div>我是偶数</div>
        <div>我是奇数</div><div>我是偶数</div>
    </body>
</html>
```

`:nth-last-child()`作用是类似的，只不过是从尾到头计数。用于表示在众多同级的兄弟节点中，排在第$n$个的，且恰为指定类型的子元素。此处$n$从$1$开始计数。

> 注意：`:nth-child()`和`:nth-last-child()`本质上的区别是计数起点。灵活地选择计数起点，可以实现更灵活的功能。
>
> 例题：现在有如下由众多`<div>`组成的HTML文档，现在需要让最后一个元素的颜色为灰色，而且相邻元素要灰白交替显示。请编写CSS。
>
> ```html
> <html>
>     <body>
>         <div>好多元素</div>
>         <div>好多元素</div>
>         <div>......</div>
>         <div>好多元素</div>
>         <div>这一行必须是灰色</div>
>     </body>
> </html>
> ```
>
> 答：使用`:nth-last-child()`即可。
>
> ```css
> /* 两种方法均可 */
> div:nth-last-child(2n+1) {color: gray}
> div:nth-last-child(odd) {color: gray}
> ```

#### §1.3.1.8 `:nth-of-type`和`:nth-last-of-type()`

用法与[§1.3.1.7 `:nth-child()`和`:nth-last-child`](§1.3.1.7 `:nth-child()`和`:nth-last-child`)类似。

### §1.3.2 动态伪类

我们之前讲过，所有伪类都必须依赖于已有的元素。当HTML文档给定的一瞬间，结构伪类就一定能从HTML文档中找到已有的元素。然而对于动态伪类来说，依赖的元素可能并不存在与原生HTML文档中，必须经过浏览器渲染后才有可能产生。

#### §1.3.2.1 超链接伪类

在HTML中，我们常用`<a href="google.com">`等方法做一个超链接，而不会认为一个普通的`<a></a>`是超链接。

"未被访问过"和"未访问状态"是两个完全不同的概念。我们以这两个`<a>`标签为例，这两个概念的关系如下图所示：

```html
<a>普通标签</a>
<a href="baidu.com">百度</a>
```

```mermaid
flowchart LR
subgraph "未访问状态 :link"
	link["&lt;a href=&quot;baidu.com&quot;&gt;<br/>未被访问过"]
end
subgraph "已访问状态 :visited"
	visited["&lt;a href=&quot;baidu.com&quot;&gt;<br/>已被访问过"]
end
link--"访问"-->visited
visited--"访问"-->visited
normal["&lt;a&gt;普通标签&lt;a&gt;<br/>未被访问过"] --"访问(失败)"--> normal
```

`:link`用于指示被标记为超链接（即具有`href`属性）的，且未被访问过的元素，也就是处于未访问状态的元素。

`:visited`用于指示已被访问过的超链接。

> 注意：超链接伪类可以被用于泄露用户隐私。理论上，JS脚本可以探测与`:visited`相同样式的所有DOM节点，从而推断出用户访问过哪些网站。鉴于此，从2017年开始，几乎所有主流浏览器厂商都支持下列补救措施：
>
> 1. `:visited`伪类能操纵的CSS属性仅限于颜色，例如`color`、`background-color`、`column-rule-color`、`outline-color`、`border-color`、`border-top/bottom/left/right-color`。
> 2. `:link`伪类不再只选择未访问状态的超链接，而是选择所有超链接。除非`:visited`覆盖了已访问状态的超链接。
> 3. 浏览器的JavaScript Runtime规定，无论超链接状态如何，返回的值始终都是未访问状态的值。

#### §1.3.2.2 用户操作伪类

`:focus`用于指示当前获得输入焦点的元素，可以通过`TAB`键和鼠标获得。

`:hover`用于指示当前被鼠标指针悬停的元素。

`:active`用于指示由用户输入激活的元素，例如对超链接按下鼠标到松开鼠标的一段时间。

```html
<html>
    <head>
        <style>
            input:focus{background-color:lightgray;}
            input:hover{background-color:lightblue;}
            input:active{background-color:lightgreen;}
        </style>
    </head>
    <body>
        <input type="text" tabindex="1"/>
        <input type="text" tabindex="2"/>
        <input type="text" tabindex="3"/>
    </body>
</html>
```

> 注意：受制于特定性，伪类的顺序不可随意设置。在实际工程中，通常推荐的顺序从`:link/:visited/:hover/:active`变成了`:link/:visited/:focus/:hover/:active`。

#### §1.3.2.3 UI状态伪类

`:enabled`用于指示接受输入的元素。

`:disabled`用于指示不接受输入的元素。

`:checked`用于指示由用户或文档默认选中的单选按钮或复选框。

`:indeterminate`用于指示"既没有选中，也没有未选中"的单选按钮和复选框。（该状态只能由DOM脚本设定，不能由用户设定）

`:default`用于指示默认选中的元素，且仅特指单选按钮、复选框、选项。

`:valid`用于指示满足数据有效性语义的元素，且仅特指输入框。

`:invalid`用于指示不满足数据有效性语义的元素，且仅特指输入框。

`:in-range`用于指示当前输入的值在控件允许的最小值和最大值之间的元素，且仅特指输入框。

`:out-range`用于指示当前输入的值小于控件允许最小值或大于控件允许最大值的元素，且仅特指输入框。

`:required`用于指示用户必须输入的元素，且仅特指输入框。

`:optional`用于指示用户非必要输入的元素，且仅特指输入框。

`:read-write`用于指示用户可以编辑的元素，且仅特指输入框。

`:read-only`用于指示用户不能编辑的元素，且仅特指输入框。

```html
<html>
<head>
    <style>
        input:disabled {opacity: 0.5;}
        input:enabled {border-color: black;}
        input:checked + label {font-weight: bold;}
        input:not(:checked) + label {color: #555;}
        input:indeterminate + label {font-style: italic;}
        input:default + label {color: darkred;}
        input:required {border-style: double;}
        input:optional {border-style: dotted;}
        input:invalid {border: 2px solid red;}
        input:valid {border: 2px solid green;}
        input:in-range {border: 5px solid green;}
        input:out-of-range {border: 5px solid red;}
        textarea:read-only {border-color: aqua;}
        textarea:read-write {border-color: greenyellow;}
    </style>
</head>
<body>
    <form>
        <div>
            <span>Username:</span><input type="text" tabindex="1" required/>
        </div>
        <div>
            <span>Password:</span>
            <input disabled type="text" tabindex="2">
        </div>
        <div>
            <span>Email:</span>
            <input type="email"/>
        </div>
        <div>
            <span>Gender:</span>
            <input type="radio" name="gender" id="male" value="male"><label for="male">Male</label>
            <input type="radio" name="gender" id="female" value="female"><label for="female">Female</label>
        </div>
        <div>
            <span>Preference:</span>
            <input type="checkbox" name="preference" id="sports" checked value="sports"/><label for="sports">Sports</label>
            <input type="checkbox" name="preference" id="music" value="music"/><label for="music">Music</label>
            <input type="checkbox" name="preference" id="cooking" value="cooking"/><label for="cooking">Cooking</label>
        </div>
        <div>
            <span>Budget:</span>
            <input type="number" min="100" max="100000" step="10000"/>
        </div>
        <div>
            <span>Matio:</span><textarea></textarea>
        </div>
        <div>
            <span>Info:</span><textarea disabled></textarea>
        </div>
    </form>
</body>
</html>
```

> 注意：`:in-range`并不意味着`:valid`。HTML5为`<input type="number" min="..." max="...">`引入了`step`属性，作为右侧加减按钮的步进长度。其`value`即使位于`[min, max]`内，只要不能被`step`整除，它就是`:invalid`。

#### §1.3.2.4 `:target`

在形如`http://...#...`的URL中，井号`#`后面的字符串被称为片段标识符，用于定位HTML文档中具有相同`id`的标签。`:target`伪类用于标记`id`与片段标识符相等的目标元素。

```html
<html>
<head>
    <style>
        #catalog > a {display: block;}
        :target {border-left: 5px solid gray; background-color: lightblue; font-weight: bold;}
    </style>
</head>
<body>
    <div id="catalog">
        <h1>目录</h1>
        <a href="./#chapter1">1.摘要</a>
        <a href="./#chapter2">2.过程</a>
        <a href="./#chapter3">3.结语</a>
    </div>
    <div id="text">
        <h1>正文</h1>
        <div id="chapter1">1.摘要 这是摘要</div>
        <div id="chapter2">2.过程 这是过程</div>
        <div id="chapter3">3.结语 这是结语</div>
    </div>
</body>
</html>
```

#### §1.3.2.5 `:lang()`

浏览器可以通过[`lang`属性](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes/lang)、[`<meta>`标签](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language)和HTML响应头这三个途径推断语言来源：

```html
<p lang="fr">这是法语</p>
<meta http-equiv="Content-Language" content="de, en">
```

```http
Content-Language: de-DE
```

`:lang()`选中具有指定语言属性的元素。

```html
<html lang="zh">
<head>
    <style>
        :lang(zh) {color: red;}
        :lang(en) {color: blue;}
        :lang(fr) {color: green;}
    </style>
</head>
<body>
    <p lang="en">这是英语</p>
    <p lang="fr">这是法语</p>
    <p>这是全局的中文</p>
</body>
</html>
```

#### §1.3.2.6 `:not()`

一个很经典的例子是：在重大公祭日时，网站的所有元素都必须转为灰白，除非存在某些元素必须设为彩色。在`:not()`出现之前，普遍的做法是：先把所有元素设为灰白，然后单独给特殊元素加`!important`覆盖掉：

```html
<html lang="zh">
<head>
    <style>
        p {filter: grayscale(1);}
        .red{color: red;}
        .blue{color: blue;}
        .green{color: green;}
        .keep-colorful {filter: grayscale(0) !important;}
    </style>
</head>
<body>
    <p class="red">网站标题</p>
    <p class="blue">网站信息</p>
    <p class="green keep-colorful">特殊元素</p>
</body>
</html>
```

有了`:not()`以后就方便多了：

```html
<html lang="zh">
<head>
    <style>
        .red{color: red;}
        .blue{color: blue;}
        .green{color: green;}
        body > *:not(.keep-colorful) {filter: grayscale(1);}
    </style>
</head>
<body>
    <p class="red">网站标题</p>
    <p class="blue">网站信息</p>
    <p class="green keep-colorful">特殊元素</p>
</body>
</html>
```

## §1.4 伪元素

### §1.4.1 装饰首字母

`::first-letter`用于装饰非行内元素的首字母。

例如给定以下需求：给英文报纸排版，每篇文章`<div>`由数个`<h1>`、`<h2>`和若干个`<p>`构成，要求给第一个出现的`<p>`的首字母调成粗体大字号：

```html
<html lang="zh">
<head>
    <style>
        p, h1 {
            font-family: 'Times New Roman', Times, serif;
        }
        .article > p:first-of-type::first-letter {
            font-weight: bold;
            font-size: 200%;
        }
    </style>
</head>
<body>
    <div class="article">
        <h1>Population Arising Comes To An End In China</h1>
        <p>This is first paragragh.</p>
        <p>This is second paragragh.</p>
        <p>This is third paragragh.</p>
    </div>
</body>
</html>
```

### §1.4.2 装饰首行

`::first-line`用于装饰元素的首行文本。

> 注意：目前`::first-letter`和`::first-line`都只能应用到块级元素上，而不能应用到行内元素上。并且这两者允许使用的CSS属性也有限制：
>
> - `::first-line`允许使用的CSS属性：所有字体属性、所有背景属性、所有文本装饰属性、所有行内排版属性、所有行内布局属性、所有边框属性，`box-shadow`、`color`、`opacity`。
> - `::first-line`允许使用的CSS属性：所有字体属性、所有背景属性、所有外边距属性、所有内边距属性、所有边框属性、所有文本装饰属性、所有行内排版属性、`color`、`opacity`。

### §1.4.3 装饰前置与后置元素

CSS可以自己生成并插入内容，从而影响HTML文档建立的DOM。

例如继续完善[§1.4.1 装饰首字母](§1.4.1 装饰首字母)一节的任务：每篇文章由一个`<h1>`、多个`<h2>`和`<p>`交叉构成，要求给每个`<h2>`的标题前插入两个灰色的左方括号：

```css
<html lang="en">
<head>
    <style>
        p, h1, h2 {
            font-family: 'Times New Roman', Times, serif;
        }
        .article > h2 + p::first-letter {
            font-weight: bold;
            font-size: 150%;
        }
        .article > h2::before {
            content: "[[";
        }
    </style>
</head>
<body>
    <div class="article">
        <h1>Population Arising Ended In China</h1>
        <h2>Data analyse</h2>
        <p>Here is the population statistic released from China Constitution of Statistics, ...</p>
        <p>From the graph, we can see that ...</p>
        <h2>The reason</h2>
        <p>The first reason is that ...</p>
        <p>The second reason is that ...</p>
        <h2>Dangerous consequence</h2>
        <p>Military circuiting is more difficult ...</p>
        <p>Finacial marketing's pulse will be slower ...</p>
    </div>
</body>
</html>
```

## §1.5 属性值

CSS中的一切都是文本，我们之前已经见识过了部分CSS属性及其可能的属性值，例如`margin: 2px`、`display: none`、`color: red`等等。属性值可以大致分为关键字、字符串和其他文本值这三类。

### §1.5.1 关键字

CSS定义的关键字有很多，几乎每个CSS属性都有自己支持的一套关键字。比较常见的有`red`、`none`之类的。

CSS3定义了以下全局关键字，每个CSS属性都能用：

- `inherit`：强制继承父元素的该属性
- `initial`：把属性值设置为预定义的初始值
- `unset`：对于可继承的属性，其效果等价于`inherit`；对于不可继承的属性，其效果等价于`initial`

> 注意：`all`属性非常特殊，它表示除`direction`和`unicode-bidi`之外的所有属性，它的属性值只能取这三个全局关键字。例如可以通过该属性，让子元素继承父元素的几乎所有属性：
>
> ```css
> ... {all: inherit;}
> ```

### §1.5.2 字符串

CSS中的字符串与其他编程语言的字符串有很多相似之处：

```css
content: "这是一个字符串";
content: "这是一个带引号'的字符串";
content: "可以给引号\"转义";
```

当字符串过长，需要换行时，可以使用下列方法：

```css
content: "这是换行 \
的字符串";
```

如果真的要在字符串中插入换行符，可以使用Unicode字符`\A`：

```css
content: "这是换行\A的字符串"
```

### §1.5.3 URL

URL的格式为`url(...)`。其中的URL链接既可以是绝对路径`url(protocol://server/pathname)`，也可以是相对路径`url(./...)`。

> 注意：包含URL的CSS文航会作用到HTML文档上。相对路径所参照的根目录取决于该URL所在的文档路径，而不是作用到的HTML文档的路径。

### §1.5.4 绝对长度单位

| 绝对长度单位      | 作用                          |
| ----------------- | ----------------------------- |
| 英寸(`in`)        | 美制单位，$1$英寸=$2.54$厘米  |
| 厘米(`cm`)        | 公制单位                      |
| 毫米(`mm`)        | 公制单位                      |
| 四分之一毫米(`q`) | 公制单位                      |
| 点(`pt`)          | 印刷度量单位，$1$英寸=$72$点  |
| 派卡(`pc`)        | 印刷度量单位，$1$英寸=$6$派卡 |
| 像素(`px`)        | CSS规定$1$英寸=$96$像素       |

### §1.5.5 分辨率单位

为适应媒体查询和响应式设计，CSS增加了三个描述显示器分辨率的单位：

| 分辨率单位       | 作用                        |
| ---------------- | --------------------------- |
| 点每英寸(`dpi`)  | 在$1$英寸范围内能显示的点数 |
| 点每厘米(`dpcm`) | 在$1$厘米范围内能显示的点数 |
| 点每像素(`dppx`) | 在$1$像素范围内能显示的点数 |

分辨率单位只能应用于媒体查询：

```css
@media (min-resolution: 500dpi){
	/* 分辨率大于等于500dpi时生效 */   
}
```

### §1.5.6 相对长度单位

相对长度单位是参考其它长度而言的，例如屏幕分辨率、视区宽度、用于偏好设置等。

| 相对长度单位 | 作用                                                         |
| ------------ | ------------------------------------------------------------ |
| `em`         | 参考该元素小写字母`m`的宽度，即该元素的`font-size`值，$1$`em`=$1$`font-size` |
| `ex`         | 参考该元素小写字母`x`的高度，$1$`ex`=$1$小写字母`x`的高度    |
| `rem`        | 参考根元素小写字母`m`的宽度，即该元素的`font-size`值，$1$`em`=$1$根元素的`font-size` |

```html
<html lang="en">
<head>
    <style>
        h1 {font-size: 24px;}
        h2 {font-size: 18px;}
        p {font-size: 12px;}
        h1, h2, p {margin-left: 1em;}
        small {margin-left: 0.8em;}

    </style>
</head>
<body>
    <h1>Left margin = <small>24</small></h1>
    <h1>Left margin = <small>18</small></h1>
    <p>Left margin = <small>24</small></p>
</body>
</html>
```

| 相对长度单位 | 作用                        |
| ------------ | --------------------------- |
| `ch`         | 参考该元素字符`0`字形的进距 |

进距指的是一个字形的起点到下一个紧邻字形的起点之间的距离，等价于字形本身的宽度加上侧边的间距。除非是等宽字体，否则对于大多数西文字体而言，字体与字体间距并不一致，而且任意一个字符的进距也不一定等于字体字形的进距。

```html
<html lang="en">
<head>
    <style>
        .section > div {
            height: 1ch;
            width: 3ch;
            background-color: red;
        }
    </style>
</head>
<body>
    <div class="section" style="font-family: Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif;">
        <span>123</span>
        <div></div>
    </div>
    <div class="section" style="font-family: 'Courier New', Courier, monospace;">
        <span>123</span>
        <div></div>
    </div>
    <div class="section" style="font-family: Verdana, Geneva, Tahoma, sans-serif;">
        <span>123</span>
        <div></div>
    </div>
</body>
</html>
```

| 相对长度单位       | 作用                                                |
| ------------------ | --------------------------------------------------- |
| 视区宽度(`vw`)     | $\displaystyle\frac{当前视区的宽度除以}{100}$       |
| 视区高度(`vh`)     | $\displaystyle\frac{当前视区的高度}{100}$           |
| 视区尺寸最小值(``) | $\displaystyle\min\left(\text{vw},\text{vh}\right)$ |
| 视区尺寸最大值(``) | $\displaystyle\max\left(\text{vw},\text{vh}\right)$ |

```html
<html lang="en">
<head>
    <style>
        body {
            padding: 0;
            margin: 0;
        }
        .container {
            height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .item {
            border: 2px solid rgb(128, 128, 128);
            border-radius: 0.5em;
            padding: 20px;
            width: 60vw;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item">我的宽度可以随浏览器窗口的宽度自动改变!</div>
    </div>
</body>
</html>
```

### §1.5.7 `calc()`

CSS允许用户使用一些简单的表达式作为属性值，让浏览器动态计算属性值。这些表达式需要用`calc()`包裹，且仅支持加减乘除与括号`+-*/()`。

```css
p {width: calc(90% - 2em)}
```

> 注意：`calc()`计算的表达式，既是数学意义上的表达式，又是编程意义上的表达式。
>
> 1. 从数学层面来说，必须保证运算符两侧的单位一致。例如`2 + 1.1`是合法的，`7em - 20%`是合法的，但是`7em - 0.2`是非法的。
> 2. 从数学层面来说，必须保证运算符两侧的值至少有一个为无量纲数。例如`2 * 2em`是合法的，`2 * 2`是合法的，但是`2em * 2em`是非法的。
> 3. 从数学层面来说，必须保证出发运算符`/`的除数为无量纲数。例如`2em /3`是合法的，但是`2 / 3em`是非法的。
> 4. 从编程层面来说，加减运算符`+-`的两侧必要有空格，乘除运算符`*/`没有这一限制。
> 5. 从编程层面来说，`calc()`支持的表达式中最多只能包含20个算子（数字、百分数等）。超过20个算子的表达式无效。

### §1.5.8 `attr()`

我们知道，HTML文档的内容是固定的，后来有了SHTML作为HTML模版，允许服务器端渲染。同理，CSS文档的内容是固定的，但是有了`attr()`，我们就能根据HTML动态决定CSS的属性值。

例如可以配合伪元素，根据AJAX返回的内容添加序号：

```html
<html lang="en">
<head>
    <style>
        .item::before {content: "[" attr(id) "]";}
    </style>
</head>
<body>
    <div class="item" id="1">参考文献</div>
    <div class="item" id="2">参考文献</div>
    <div class="item" id="3">参考文献</div>
</body>
</html>
```

### §1.5.9 颜色

#### §1.5.9.1 具名颜色

HTML规定了16个[基本颜色关键字](https://zh.wikipedia.org/wiki/%E7%BD%91%E9%A1%B5%E9%A2%9C%E8%89%B2)：

| 基本颜色关键字 | 颜色代码  | 基本颜色关键字 | 颜色代码  |
| -------------- | --------- | -------------- | --------- |
| `aqua`         | `#00ffff` | `blue`         | `#0000ff` |
| `gray`         | `#808080` | `lime`         | `#00ff00` |
| `navy`         | `#000080` | `purple`       | `#800080` |
| `silver`       | `#c0c0c0` | `white`        | `#ffffff` |
| `black`        | `#000000` | `fuchsia`      | `#ff00ff` |
| `green`        | `#008000` | `maroon`       | `#800000` |
| `olive`        | `#808000` | `red`          | `#ff0000` |
| `teal`         | `#008080` | `yellow`       | `#ffff00` |

CSS4还规定了148个额外的具名颜色。

#### §1.5.9.2 RGB颜色和RGBa颜色

RGB认为所有颜色都可以分解成三个0-255之间的红色、绿色、蓝色的分量。RGBa增加一个维度`alpha`，表示颜色的不透明度，取值范围是`[0,1]`之间的任意浮点数。CSS支持以下四种方法指定RGB颜色和RGBa颜色：

1. 函数式RGB颜色

   ```css
   p {
   	color: rgb(128,128,128); /* 要么只能用整数 */
       color: rgb(50%,50%,50%); /* 要么只能用百分数 */
       color: rgb(50%,50%,128); /* 不能混用整数和百分数 */
   }
   ```

   虽然CSS名义上支持浮点数的分量，但是实际上用户代理会自动对其四舍五入，让其变为整数。如果分量为小于0，则约为0；如果大于255，则约为255。综上，函数式RGB颜色最终的输出可以表示为：
   $$
   \text{RGB}(r,g,b)=\left[\begin{matrix}
   	R\\G\\B
   \end{matrix}\right]
   =
   \left[\begin{matrix}
   	\text{round}(\min(\max(0,r),255))\\
   	\text{round}(\min(\max(0,g),255))\\
   	\text{round}(\min(\max(0,b),255))\\
   \end{matrix}\right]
   $$

2. 函数式RGBa颜色

   ```css
   p {
   	color: rgba(128,128,128,0.8);
   }
   ```

   舍入规则与函数式RGB颜色相同。

3. 十六进制RGB颜色

   可以使用两位十六进制数字表示分量，共计六位。如果只有三位，浏览器会自动将每个数字向后紧邻复制一次，扩充到六位。

   ```css
   p {
   	color: #ff0000; /* 小写字母都行 */
       color: #FF0000; /* 大写字母都行 */
       color: #123 /* 等价于#112233 */
   }
   ```

4. 十六进制RGBa颜色

   2017年，一种新的RGBa表示方法被提出了。它沿用了十六进制RGB颜色表示方法，将`alpha`分量离散化成0-255之间的离散值。

   ```css
   p {
   	color: #66ccff88;
       color: #6cf8; /* 简写 *?
   }
   ```

#### §1.5.9.3 HSL颜色和HSLa颜色

HSL颜色是由色相（Hue）、饱和度（Saturation）、明度（Lightness）构成的简称。其中$H\in[0\degree,360\degree)$，$S,L\in[0,1]$。

$RGB\rightarrow HLS$：

- $$
  H=\begin{align}\begin{cases}
  		0&,\max(R,G,B)=\min(R,G,B)\\
  		\displaystyle\frac{60\degree\times(G-B)}{\max(R,G,B)-\min(R,G,B)}\mod{360\degree}&,\max(R,G,B)=R\\
  		\displaystyle\frac{60\degree\times(B-R)}{\max(R,G,B)-\min(R,G,B)}+120\degree&,\max(R,G,B)=G\\
  		\displaystyle\frac{60\degree\times(R-G)}{\max(R,G,B)-\min(R,G,B)}+240\degree&,\max(R,G,B)=B\\
  	\end{cases}\end{align}
  $$

- $$
  L=\displaystyle\frac{\max(R,G,B)+\min(R,G,B)}{2}
  $$

- $$
  S=\begin{align}\begin{cases}
  	0&,\left|\max(R,G,B)\right|=\left|\min(R,G,B)\right|\\
  	\displaystyle\frac{\max(R,G,B)-\min(R,G,B)}{\max(R,G,B)+\min(R,G,B)}&,L\in(0,\displaystyle\frac{1}{2}]\\
  	\displaystyle\frac{\max(R,G,B)-\min(R,G,B)}{2-(\max(R,G,B)+\min(R,G,B))}&,L>\displaystyle\frac{1}{2}
  \end{cases}\end{align}
  $$

$HLS\rightarrow RGB$：

- 定义中间变量$q,p$为：
  $$
  \begin{cases}
  	q=\begin{align}\begin{cases}
  		L+LS&,L\in[0,\displaystyle\frac{1}{2})\\
  		L+S-LS&,L\in[\displaystyle\frac{1}{2},1]
  	\end{cases}\end{align}\\
  	p=2L-q\\
  \end{cases}
  $$

- 定义$t_R,t_G,t_B$分别为：
  $$
  \begin{cases}
  	t_R=\displaystyle\frac{h}{360}+\displaystyle\frac{1}{3}\mod1\\
  	t_G=\displaystyle\frac{h}{360}\mod1\\
  	t_B=\displaystyle\frac{h}{360}-\displaystyle\frac{1}{3}\mod1
  \end{cases}
  $$

- 最终结果为：
  $$
  RGB_{i(i\in\{R,G,B\})}=
  \begin{align}\begin{cases}
  	p+6(q-p)t_i&,t_i<\in[0,\displaystyle{\frac{1}{6}})\\
  	q&,t_i\in\displaystyle[\frac{1}{6},\frac{1}{2})\\
  	p+6(q-p)(\displaystyle\frac{2}{3}-t_i)&,t_i\in[\displaystyle\frac{1}{2},\displaystyle\frac{2}{3})\\
  	p&,t_i\in[\displaystyle\frac{2}{3},1]
  	
  \end{cases}\end{align}
  $$

#### §1.5.9.4 `currentColor`

该关键字表示当前元素的`color`属性值。

```html
<html>
<head>
    <style>
        .red {color: red;}
        .showBorder {
            border: 2px solid currentColor;
        }
    </style>
</head>
<body>
    <div class="showBorder red">
        这是一段文本
    </div>
</body>
</html>
```

### §1.5.10 角度

| 角度单位       | 作用              |
| -------------- | ----------------- |
| 角度(`deg`)    | 一圈为$360$度     |
| 百分度(`grad`) | 一圈为$100$百分度 |
| 弧度(`rad`)    | 一圈为$2\pi$弧度  |
| 圈数(`turn`)   |                   |

### §1.5.11 时间

| 时间单位   | 作用             |
| ---------- | ---------------- |
| 秒(`s`)    |                  |
| 毫秒(`ms`) | $1$秒=$1000$毫秒 |

### §1.5.12 频率

| 频率单位             | 作用                 |
| -------------------- | -------------------- |
| 赫兹(`hz`或`Hz`)     |                      |
| 千赫兹(`khz`或`hHz`) | $1$千赫兹=$1000$赫兹 |

### §1.5.13 自定义值

2017年，CSS引入了自定义属性。自定义属性以两个连字符开头`--`，可以在CSS中创建默认继承的属性，作为变量配合`var()`调用。从本质上来说，自定义属性就是宏。

```html
<html lang="en">
<head>
    <style>
        html {
            --base-color: #66ccff
        }
        h1 {
            color: var(--base-color);
        }
    </style>
</head>
<body>
    <h1>标题</h1>
</body>
</html>
```

众所周知，宏的使用方式是没有上限的。这里举一个例子：为不同缩进程度的无序列表设置不同的左边距长度。一般情况下，我们会考虑这样实现：

```css
ul li {margin-left: 2ch;}
ul ul li {margin-left: 4ch;}
ul ul ul li {margin-left: 6ch;}
ul ul ul ul li {margin-left: 8ch;}
```

现在使用自定义属性和`calc()`，就可以实现等价效果：

```css
html {
    --custom: 2;
}
@supports (color: var(--custom)){
    @supports (--custom: value){
        ul li {margin-left: calc(1 * var(--custom));}
        ul ul li {margin-left: calc(2 * var(--custom));}
        ul ul ul li {margin-left: calc(3 * var(--custom));}
        ul ul ul ul li {margin-left: calc(4 * var(--custom));}
    }
}

```

# §2 优先级、继承、层叠

优先级是规则的属性，可以表示为初值为`(0,0,0,0)`的四维向量`(x,y,z,t)`。如果多个规则针对同一个元素设置了冲突的样式，那么最终应该听谁的呢？答案是计算这些规则的优先级，比较时从前往后比较分量大小，率先能判断出分量高者胜出。

优先级计算规则如下所示：

1. 选择符中的每个`id`属性会让优先级增加`(0,1,0,0)`。
2. 选择符中的每个`class`属性、属性选择符`[]`、伪类会让优先级增加`(0,0,1,0)`。
3. 选择符中的每个元素和伪元素会让优先级增加`(0,0,0,1)`。
4. 连结符和通用选择符不增加优先级。

例如`html > body #answer table tr[id="total"] *.link`中出现了$1$个`id`属性，总共出现了$2$个`class`属性和属性选择符，出现了$4$个元素，因此该CSS规则的优先级为`(0,1,2,4)`。

现在我们已经知道了第二、三、四位分量的计算规则。其实第一位分量是为声明行内样式`<... style="...">`而设计的。它的第一位分量恒为`1`，有着最高的优先级。

## §2.1 重要声明

```mermaid
flowchart LR
subgraph unimportant["无重要声明"]
	declare1["声明"]
	declare2["声明"]
	declare3["声明"]
end
unimportant--"取优先级最高者"-->declare4["声明"]
subgraph important["!important"]
	declare5["声明"]
	declare6["声明"]
	declare7["声明"]
end
important--"取优先级最高者"-->declare8["声明"]
declare4 & declare8 --"取!important"--> declare9["最终生效的声明"]
```

可以使用`!important`表示该声明重要程度超过其它所有声明。`!important`本身并不会影响规则的优先级。所有规则会根据是否被`!important`声明分成两组，各自内部取优先级最高者，最后优先取`!important`声明的规则。

> 注意：从以上事实来看，我们知道，优先级并不是唯一一个决定最终效果的因素。优先级最高的规则，也可能不生效。

```css
p {
	color: red !important;
    background: white;
    font-weight: bold;
}
```

## §2.2 继承

某些样式不仅会应用到CSS所指的元素上，而且还会应用到其子元素上。这种现象被称为继承。将DOM树想象成一个从上向下生长的树，那么继承的方向为向下传播，也就是父元素传给子元素，直到子元素没有子元素为止。

> 注意：我们说继承是向下传播的，然而CSS规范中只有一处例外：当`<html>`标有规定背景，而`<body>`规定背景时，`background`属性会从`<body>`传播到`<html>`上。

真正能被允许继承的CSS属性只占一小部分。例如盒模型相关的、边框相关的属性肯定不能继承，但是`color`之类的属性可以继承。

继承的CSS属性没有优先级这一说。注意，不是零优先级，而是无优先级，可以理解为比零优先级还要低。

## §2.3 层叠

如果由两个发生冲突的规则，而且它们的优先级完全一样，那么到底选谁生效呢？这种情况下的抉择过程称为层叠。

CSS规定的层叠规则如下：

1. 按照显式权重（行内样式`important`>`!important`>无重要标记）进行排序，尝试选取最高者。
2. 按照来源（读者`!important`>创作人员>读者>用户代理）进行排序，尝试选取最高者。
   - 创作人员：引入的CSS文件或`<style>`标签内容
   - 读者：用户自行设计的CSS文件（浏览器脚本、审查元素）
   - 用户代理：浏览器预置样式（超链接样式、`<input>`样式、自带的阅读模式）
3. 按照优先级进行排序，尝试选取最高者。
4. 按照声明的先后顺序（越靠后权重越高）进行排序，选择最高者。

> 注意：在[§1.3.2.2 用户操作伪类](§1.3.2.2 用户操作伪类)一节中，我们知道实际工程中常常按照`LVFHA`顺序（`link-visited-focus-hover-active`）编写连接的样式。
>
> 经过本章的学习，我们已经知道了下列规则的优先级都是`(0,0,1,0)`：
>
> ```css
> a:link {color: red;}
> a:visited {color: red;}
> a:focus {color: red;}
> a:hover {color: red;}
> a:active {color: red;}
> ```
>
> 为了让这五个伪类实现预期的效果，`:link`和`:visited`必须放在`:hover`、`:focus`、`:active`的前面，否则后三者会被前两者覆盖，永远无法匹配。
>
> 除此之外，把伪类穿在一起，也能解决这一问题：
>
> ```css
> a:link {color: red;}
> a:visited {color: red;}
> a:link:hover {color: red;}
> a:visited:hover{color: red;}
> a:link:active {color: red;}
> a:visited:active {color: red;}
> ```

# §3 字体

## §3.1 字体族

我们知道，字体通常包含多个变体，例如粗体、斜体等等。例如`Times`字体，它的变体包括`TimesRegular`、`TimesBold`、`TimesItalic`、`TimesBoldItalic`等等。严格来说，`Times`不是一个字体，而是一个包含多个字形的字体族。

字体族使用`font-family`属性声明：

```css
body {
    font-family: sans-serif, Times;
}
```

浏览器从前向后查找计算机中是否包含声明的字体。如果不存在，则依次向后查找匹配。

CSS定义了五种通用字体族：

1. 衬线字体：有衬线，宽度不同。
2. 无衬线字体：无衬线，宽度不同。
3. 等宽字体：有衬线或无衬线，宽度相同。
4. 草书字体/手写体：有书法风格，比衬线字体更华丽。
5. 奇幻字体/装饰字体/展示字体：用于展示符号的字体。

## §3.2 `@font-face`

`@font-face`允许开发使用使用自定义的字体。通常情况下，浏览器只会从浏览器本地查找已安装的字体。现在有了`@font-face`，即使本地没有安装，浏览器也能使用服务器端的字体，而无需将其安装在本地。

```html
<html>
<head>
    <style>
        @font-face {
            font-family: CascadiaCodeRegular;
            src: url(./static/font/CascadiaCode/CascadiaCode-Regular.otf);
        }
        h1 {
            font-family: CascadiaCodeRegular;
        }
    </style>
</head>
<body>
    <h1>font-size()</h1>
</body>
</html>
```

在CSS规范中，浏览器应该采用懒加载机制——只有需要渲染指定字体的文本时才向服务器请求下载字体文件。2017年时，各大浏览器都未能实现这一机制。实测2023年Chrome已经能实现这一机制。

`@font-face`的`src`支持提供多个下载源，下载链接之间用逗号隔开。一些版本较旧的浏览器可能不支持新式字体标准，只有下载完字体文件后才能发现。为了解决这一问题，开发人员可以在CSS中用`format()`预先声明该字体使用的标准：

```css
@font-face {
	font-family: "SwitzeraADF",
    src: url("SwitzeraADF-Regular.otf") format("opentype"),
         url("SwitzeraADF-Regular.true") format("truetype");
}
```



目前CSS支持的字体格式如下所示：

| 格式                          | `format()`值        |
| ----------------------------- | ------------------- |
| EOT(Embedded OpenType)        | `embedded-opentype` |
| OTF(OpenType)                 | `opentype`          |
| SVG(Scalable Vector Graphics) | `svg`               |
| TTF(TrueType)                 | `truetype`          |
| WOFF(Web Open Font Format)    | `woff`              |

> 注意：一些旧的浏览器对`@font-face`的支持存在BUG。例如对于IE6，在`src`属性中填入多个备选`url()`，会导致只有第一个`url()`生效，其余`url()`一概返回404。
>
> 为了解决这一问题，研究人员使用IE6的另一个解析器BUG来绕过这个`url()`BUG——IE6遇到`?#iefix`会出发一个解析器BUG，从而认为该`url()`无效，转而向后寻找`url()`。
>
> ```css
> @font-face {
> 	font-family: "SwitzeraADF";
>     src: url("SwitzeraADF-Regular.eot");
>     src: url("SwitzeraADF-Regular.eot#iefix") format("embedded-opentype"),
>          url("SwitzeraADF-Regular.woff") format("woff"),
>          url("SwitzeraADF-Regular.ttf") format("truetype"),
>          url("SwitzeraADF-Regular.svg#switzera_adf_regular") format("svg");
> }
> ```

除了`font-family`和`src`这两个必需的属性外，CSS也为`@font-face`提供了额外的可选属性：

| 属性                    | 默认值       | 作用                                                         |
| ----------------------- | ------------ | ------------------------------------------------------------ |
| `font-style`            | `normal`     | 声明字形（常规、斜体、斜体                                   |
| `font-weight`           | `normal`     | 声明字重（粗体）                                             |
| `font-stretch`          | `normal`     | 声明字符宽度（紧缩、加宽）                                   |
| `font-variant`          | `normal`     | 声明字形变体（大小写字母）                                   |
| `font-feature-settings` | `normal`     | 声明OpenType底层特性（连字）                                 |
| `unicode-range`         | `U+0-10FFFF` | 声明可用字符范围。只有当前页面包含范围之内的Unicode字符时才加载。语法较灵活，支持属性值与通配符：`U+4E00-9FFFF, U+30??,U+A5` |

使用以上可选属性，我们终于可以用`@font-face`引入整个字体族，而不是单个字体了：

```html
<html>
<head>
    <style>
        @font-face {
            font-family: CascadiaCode;
            font-weight: normal;
            font-style: normal;
            font-stretch: normal;
            src: url(./static/font/CascadiaCode/CascadiaCode-Regular.otf) format("opentype");
        }
        @font-face {
            font-family: CascadiaCode;
            font-weight: bold;
            font-style: normal;
            font-stretch: normal;
            src: url(./static/font/CascadiaCode/CascadiaCode-Bold.otf) format("opentype");
        }
        @font-face {
            font-family: CascadiaCode;
            font-weight: normal;
            font-style: italic;
            font-stretch: normal;
            src: url(./static/font/CascadiaCode/CascadiaCode-Italic.otf) format("opentype");
        }
        @font-face {
            font-family: CascadiaCode;
            font-weight: bold;
            font-style: italic;
            font-stretch: normal;
            src: url(./static/font/CascadiaCode/CascadiaCode-BoldItalic.otf) format("opentype");
        }
        h1 {
            font-family: CascadiaCode;
        }
    </style>
</head>
<body>
    <h1 style="font-weight: normal; font-style: normal;">font-size()</h1>
    <h1 style="font-weight: bold; font-style: normal;">font-size()</h1>
    <h1 style="font-weight: normal; font-style: italic;">font-size()</h1>
    <h1 style="font-weight: bold; font-style: italic;">font-size()</h1>
</body>
</html>
```

## §3.3 字体属性

### §3.3.1 字重(`font-weight`)

CSS的`font-weight`属性用于控制自重，其可能的取值有：`normal`（等价于`400`）、`bold`（等价于`700`）、`bolder`、`lighter`、`100`、`200`、`300`、`400`、`500`、`600`、`700`、`800`、`900`。默认值为`normal`。

如果字体族中的字重等价少于规定的九个，则浏览器按照以下规则处理：

1. 如果`500`未定义，则将`500`定义为`400`。
2. 如果`300`未定义，则将`300`定义小于等于`200`的第一个变体
3. 如果`600`未定义，则将那个`600`定义为大于等于`700`的第一个变体。如果不存在，则将`600`定义为`500`
4. 如果`700`、`800`、`900`未定义，规则同第三条。
5. 如果`bolder`为定义，则将`bolder`定义为从`bold`向上查找遇到的第一个已定义的字形。

使用特性字重的字形有以下几种方法：

```html
<!-- 使用<strong>和标签 -->
<strong>123</strong>
<strong>粗<strong>更粗</strong></strong>

<!-- 使用CSS的font-weight属性 -->
<div style="font-weight: bold;">
    <div>粗</div>
    <div style="font-weight: bolder">
		更粗
    </div>
</div>
```

### §3.3.2 字号(`font-size`)

字号用于控制字体显示的大小。英文练习本中，一行有四条参考线，其中从上往下数的第一条线和第三条线称为基线，这个大小指的是两条基线之间的距离，所以一个字符的高度完全有可能超过字。它的默认值为`normal`，取值范围有：

1. `xx-small`、`x-small`、`small`、`medium`、`large`、`x-large`、`xx-large`、`smaller`、`larger`
2. `<length>`
3. `<percentage>`

#### §3.3.2.1 绝对大小

`font-size`支持的绝对大小关键字有：`xx-small`、`x-small`、`small`、`medium`、`large`、`x-large`、`xx-large`。

| 关键字     | CSS1       | CSS2       | CSS3       |
| ---------- | ---------- | ---------- | ---------- |
| `xx-small` | `5px`      | `9px`      | `10px`     |
| `x-small`  | `7px`      | `11px`     | `12px`     |
| `small`    | `11px`     | `13px`     | `14px`     |
| `medium`   | **`16px`** | **`16px`** | **`16px`** |
| `large`    | `24px`     | `19px`     | `19px`     |
| `x-large`  | `36px`     | `23px`     | `24px`     |
| `xx-large` | `54px`     | `28px`     | `32px`     |

需要注意的是，`medium`的值并不恒为`16px`。不同的浏览器，不同的HTML标签，甚至不同类型的字体都可能会认为`medium`不为`16px`：

```html
<html>
<head>
    <style>
        p {font-size: medium;}
        span {font-family: monospace;font-size: 1em;}
    </style>
</head>
<body>
    <p>123<span>123</span></p>
</body>
</html>
```

这是因为字号向下传递时，传递的永远是关键字。在普通字体中，`medium`默认是`16px`，而对于等宽字体来说，默认字号`medium`是`13px`。为解决这一问题，一种通用的方法是在`monospace`之后补充`,serif`。

#### §3.3.2.2 相对大小

`font-size`支持的相对大小关键字有：`larger`和`smaller`，相邻两级的字号相差`1.2`倍。

```html
<html>
<head>
    <style>
        bigger {
            font-size: larger;
        }
    </style>
</head>
<body>
    <div>
        <span>123</span>
        <bigger>
            <span>123</span>
            <bigger>
                <span>123</span>
            </bigger>
        </bigger>
    </div>
</body>
</html>
```

#### §3.3.2.3 百分数与`em`

百分数和`em`参考的对象是父元素的字号。

```html
<html>
<head>
    <style>
        bigger {
            font-size: 200%;
            font-size: 2em; /* 两种方式等价 */
        }
    </style>
</head>
<body>
    <div>
        <span>123</span>
        <bigger>
            <span>123</span>
            <bigger>
                <span>123</span>
            </bigger>
        </bigger>
    </div>
</body>
</html>
```

#### §3.3.2.4 自动调整字号(`font-size-adjust`)

给定一款字形，我们定义这个字形的高宽比值为小写字母`x`的高度除以字号。在旧版本浏览器中，当其已加载完HTML，但未加载完CSS时，就会使用默认样式来渲染。因此存在一瞬间，用户看到的是未经CSS修饰的文本。`font-size-adjust`可以用于更改字形的高宽比值，使文字更容易阅读；也可以减轻默认样式渲染差异带来的影响。

例如给定`Verdana`和`Times`两种同字号`10px`字体，我们很明显就能看出这两种字形的实际显示大小不一致。这是因为它们的高宽比值分别为`0.58`和`0.46`，值越大就说明在字号相等的前提下，字体实际显示大小越大。

`font-size-adjust`对`font-size`的影响如下所示：
$$
\text{font-size}_{\color{red}{\text{new}}}=\text{font-size}_{\color{red}{\text{raw}}}\times\frac{\text{font-size-adjust}}{高宽比值}
$$
只要我们把`Times`字体的`font-size-adjust`设置成`Verdana`的高宽比值，就能弥补高宽比值差异造成的影响：

```html
<html>
<head>
    <style>
        p {font-size: 10px;}
        p.font1 {
            font-family: Verdana, sans-serif; 
        }
        p.font2 {
            font-family: Times, serif;
            font-size-adjust: 0.58;
        }
    </style>
</head>
<body>
    <p class="font1">abc123</p>
    <p class="font2">abc123</p>
</body>
</html>
```

实际上，我们不需要知道字体的高宽比值，仅仅使用`auto`关键字，浏览器就会自动计算。

> 注意：在2017年，唯一支持`font-size-adjust`属性的浏览器只有Firefox。经2023年Chrome实测，`font-size-adjust`产生的效果近乎可以忽略不计。

### §3.3.3 字形(`font-style`)

`font-style`负责控制字形，只有常规(`normal`)、斜体(`italic`)、倾斜体(`oblique`)这三种取值。

斜体和倾斜体虽然都能让字体倾斜，但是斜体注重手写的程序，而倾斜体只是单纯的印刷体。现在字体设计界对字形的支持较差，一般将斜体和倾斜体混为一谈。

```html
<html>
<head>
    <style>
        p {font-family:'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;}
        .type1 {font-style: italic;}
        .type2 {font-style: oblique;}
    </style>
</head>
<body>
    <p class="type1">abc123</p>
    <p class="type2">abc123</p>
</body>
</html>
```

### §3.3.4 字体拉伸(`font-stretch`)

有些字体族提供了较宽或较窄的字符形式，这些变体通常被命名为`Condensed`、`Wide`、`Ultra Expanded`等标识。它的取值范围有：`normal`、`ultra-condensed`、`extra-condensed`、`condensed`、`semi-condensed`、`expanded`、`extra-expanded`、`ultra-expanded`。

```html
<html>
<head>
    <style>
        @font-face {
            font-family: "Inconsolata";
            src: url("https://fonts.gstatic.com/s/inconsolata/v31/QlddNThLqRwH-OJ1UHjlKENVzlm-WkL3GZQmAwPyya15.woff2") format("woff2");
            font-stretch: 50% 200%;
        }

        p {
            font-family: Inconsolata;
        }

        p.type1 {
            font-stretch: extra-condensed;
        }

        p.type2 {
            font-stretch: extra-expanded;
        }
    </style>
</head>
<body>
    <p class="type1">abc123</p>
    <p class="type2">abc123</p>
</body>
</html>
```

### §3.3.5 字距(`font-kerning`)

字符之间相对位置的长度称为字距。它和`letter-spacing`最大的差别在于：`font-kerning`允许字符之间真正地按照规律紧凑起来。例如我们手写`To`这个单词时，会将`o`写到`T`的右下角，而`letter-spacing`就有可能让`o`碰到`T`的横杠。

截止2023年，只有Firefox支持该特性。

### §3.3.6 字体特性(`font-feature-settings`)

`font-feature-settings`属性可以从底层控制OpenType字体(`.otf`)支持的特性，因此不能应用于其它字体格式中。

OpenType字体支持特性参见[微软帮助文档](https://learn.microsoft.com/en-us/typography/opentype/spec/featurelist)。在这些特性中，默认开启的有以下特性：

| OpenType特性 | 作用           |
| ------------ | -------------- |
| `calt`       | 根据上下文替换 |
| `ccmp`       | 组合字符       |
| `clig`       | 根据上下文连字 |
| `liga`       | 标准连字       |
| `locl`       | 本地化形式     |
| `mark`       | 基本定位标记   |
| `mkmk`       | 标记定位标记   |
| `rlig`       | 必要的连字     |

```html
<html>
<head>
    <style>
        @font-face {
            font-family: CascadiaCode;
            src: url(./static/font/CascadiaCode/CascadiaCode-Regular.otf);
        }
        p {font-family: CascadiaCode;}
        .style1 { font-feature-settings: "liga" off, "calt" off, "clig" off, "rlig" off; }
        .style2 { font-feature-settings: "liga" on , "calt" on , "clig" on , "rlig" on; }

    </style>
</head>
<body>
    <p class="style1">(()=>{1===0})();</p>
    <p class="style2">(()=>{1===0})();</p>
</body>
</html>
```

### §3.3.7 字体变形(`font-variant`)

CSS3提供了字体变形`font-variant`属性及其附属的多个子属性。字体变形包括连写、大小写、小数、`z`和`0`的贯穿线等特征。该属性需要字体本身支持。

例如使用小号大写字母，每个单词都以大写字母展示，且首字母字号稍大。

```html
<html>
<head>
    <style>
        h1 {
            font-variant: small-caps;
        }
    </style>
</head>
<body>
    <h1>Every Letter Is In Captial!</h1>
</body>
</html>
```

#### §3.3.7.1 `font-variant-ligatures`

决定英文字母（例如`fi`）是否连字。

#### §3.3.7.2 `font-variant-caps`

决定英文字母是否以小号大写方式呈现。

```html
<html>
<head>
    <style>
        .style1 {font-variant: normal;}
        .style2 {font-variant: small-caps;}
        .style3 {font-variant: all-small-caps;}
    </style>
</head>
<body>
    <h1 class="style1">Hello</h1>
    <h1 class="style2">Hello</h1>
    <h1 class="style3">Hello</h1>
</body>
</html>
```

#### §3.3.7.3 `font-variant-numeric`

决定数字显示方式。例如`0`是否有贯穿线，数字是否上下交错排布。

## §3.4 总属性`font`

总属性`font`决定着五个子属性，分为两组，第一组是`font-style`、`font-weight`、`font-variant`，第二组是`font-size[/line-height]`、`font-family`。

第一组的三个属性非常宽松，声明顺序没有限制，而且都可以缺省为`normal`。第二组的两个属性较为严格，声明顺序不能打乱，而且都不能缺省。虽然`line-height`本身不属于字体的属性，但是也可以在`font`属性中跟随`font-size`共同指定，写成类似于`24px/1.5`的格式。

```css
font: bold italic 24px Verdana, Helvetica, Arial, sans-serif;
```

除此之外，开发人员也可以不使用以上五个子属性，而是使用操作系统设置的默认值。操作系统提供的配置往往更能与系统本身达成一致性设计的效果，这些配置称为系统字体。可用的系统字体有：

| 系统字体        | 作用                       |
| --------------- | -------------------------- |
| `caption`       | 用于说明文字的控件（按钮） |
| `icon`          | 图表                       |
| `menu`          | 菜单（下拉菜单、菜单列表） |
| `message-box`   | 对话框                     |
| `small-caption` | 标注小型控件               |
| `status-bar`    | 窗口状态栏                 |

```html
<h1 style="font: caption;">text</h1>
<h1 style="font: icon;">text</h1>
<h1 style="font: menu;">text</h1>
<h1 style="font: message-box;">text</h1>
<h1 style="font: small-caption;">text</h1>
<h1 style="font: status-bar;">text</h1>
```

# §4 文本

## §4.1 行内与块级

块级方向指的是当前书写模式放置块级元素的方向。在英语中，块级方向是从上到下，也就是说一个自然段放在另一个自然段的下面。

行内元素指的是块级元素中行内元素的书写方向。在英语中，行内方向从左向右。

## §4.2 行内属性

### §4.2.1 缩进(`text-indent`)

`text-indent`用于设置第一行文本的缩进长度，可以是负值，默认向子元素继承。

```html
<div style="width: 100px; background-color: coral;">
    <p style="text-indent: 1ch;">这是第一段:</p>
    <p style="text-indent: 2ch;">这是第二段：</p>
    <p style="text-indent: 4ch;">这是第三段：</p>
</div>
```

```html
<div style="width: 100px; background-color: coral; padding-left: 4em;">
    <p style="text-indent: -1ch;">悬挂缩进Demo:</p>
    <p style="text-indent: -2ch;">悬挂缩进Demo:</p>
    <p style="text-indent: -4ch;">悬挂缩进Demo:</p>
</div>
```

### §4.2.2 横向对齐(`text-align`/`text-align-last`)

`text-align`用于设置文本各行的对齐方式。

| `text-align`属性值 | 作用                         |
| ------------------ | ---------------------------- |
| `start`            | 靠当前语言的书写起点向其对齐 |
| `end`              | 靠当前语言的书写起点向其对齐 |
| `left`             | 无论语言，一概向左对齐       |
| `right`            | 无论语言，一概向右对齐       |
| `center`           | 除最后一行以外，居中对齐     |
| `justify`          | 首行两端对齐                 |

```html
<div style="width: 80px; background-color: coral;;">
    <p style="text-align: start;">Hello world, this is a text-align demo.</p>
    <p style="text-align: end;">Hello world, this is a text-align demo.</p>
    <p style="text-align: left;">Hello world, this is a text-align demo.</p>
    <p style="text-align: right;">Hello world, this is a text-align demo.</p>
    <p style="text-align: center;">Hello world, this is a text-align demo.</p>
    <p style="text-align: justify;">Hello world, this is a text-align demo.</p>
</div>
```

`text-align-last`用于设置最后一行的对齐方式，其取值与`text-align`常用属性值相同。当文本只有一行时，`text-align-last`的优先级高于`text-align`的优先级。

## §4.3 块级属性

### §4.3.1 行高(`line-height`)

`line-height`属性指定行与行之间的基线距离。从这一点来说，行距等于行高减去字体高度。当`line-height`的属性值为距离单位时，属性值代表的是绝对距离；当属性值为无量纲数时，代表的时从父元素继承`line-height`属性值时使用的换算比例。

```html
<html>
<head>
    <style>
        p {background-color: wheat;}
    </style>
</head>
<body>
    <div style="width: 80px;">
        <p style="line-height: 5px;">123123</p>
        <p style="line-height: 10px;">123123</p>
        <p style="line-height: 15px;">123123</p>
        <p style="line-height: 20px;">123123</p>
        <p style="line-height: 25px;">123123</p>
    </div>
</body>
</html>
```

### §4.3.2 纵向对齐(`vertical-align`)

| `vertical-align`属性值 | 作用                                                         |
| ---------------------- | ------------------------------------------------------------ |
| `baseline`(默认值)     | 让元素的基线与父元素该行的基线对齐                           |
| `sub`                  | 让元素的基线低于父元素该行的基线，即放在下标处。CSS没有规定下降的距离，显示效果受限于浏览器， |
| `super`                | 让元素的基线高于父元素该行的基线，即放在上标处。CSS没有规定上升的距离，显示效果受限于浏览器， |
| `top`                  | 让元素的顶边与父元素该行的顶边对齐                           |
| `text-top`             | 让元素的基线与父元素该行的顶边对齐                           |
| `middle`               | 让元素的中线与父元素该行的中线对齐                           |
| `bottom`               | 让元素的底边与父元素该行的底边对齐                           |
| `text-bottom`          | 让元素的基线与父元素该行的底边对齐                           |

```html
<html>
<head>
    <style>
        p {
            background-color: lightgreen;
            line-height: 40px;
        }
        p > img {height: 10px; width: 10px; background-color: gold;}
    </style>
</head>
<body>
    <div style="width: 300px;">
        <p>
            <span style="vertical-align: super;">忐</span>
            <span style="vertical-align: sub;">忑</span>
            <span>不安 :)</span>
        </p>
        <p>vertical-align: baseline<img style="vertical-align: baseline;"/></p>
        <p>vertical-align: sub<img style="vertical-align: sub;"/></p>
        <p>vertical-align: super<img style="vertical-align: super;"/></p>
        <p>vertical-align: top<img style="vertical-align: top;"/></p>
        <p>vertical-align: text-top<img style="vertical-align: text-top;"/></p>
        <p>vertical-align: middle<img style="vertical-align: middle;"/></p>
        <p>vertical-align: bottom<img style="vertical-align: bottom;"/></p>
        <p>vertical-align: text-bottom<img style="vertical-align: text-bottom;"/></p>
    </div>
</body>
</html>
```

| `vertical-align`属性值 | 作用                                                         |
| ---------------------- | ------------------------------------------------------------ |
| 百分数                 | 相对于父元素的`line-height`，将元素的基线从父元素的基线抬升或下沉的百分比。负数为下降，正数为上升 |
| 绝对长度               | 从CSS2引入，指定上升或下沉的距离                             |

```html
<html>
<head>
    <style>
        p {
            background-color: lightgreen;
            line-height: 40px;
        }
        p > img {height: 10px; width: 10px; background-color: gold;}
    </style>
</head>
<body>
    <div style="width: 300px;">
        <p>vertical-align: -100%<img style="vertical-align: -100%;"/></p>
        <p>vertical-align: -50%<img style="vertical-align: -50%;"/></p>
        <p>vertical-align: 0%<img style="vertical-align: 0%;"/></p>
        <p>vertical-align: 50%<img style="vertical-align: 50%;"/></p>
        <p>vertical-align: 100%<img style="vertical-align: 100%;"/></p>
    </div>
</body>
</html>
```

## §4.4 间距

### §4.4.1 单词间距(`word-spacing`)

`word-spacing`决定了单词之间的距离。这里的“单词”指的是两侧有空白字符的仅包含非空白字符的字符串。该属性默认为`normal`，等价于绝对长度`0`。正值时加大间距，负值时缩短间距。受制于CSS对于单词的定义，该属性不适用于象形文字等非罗马文字。

```html
<p style="word-spacing: -1.0em;">Hello World!</p>
<p style="word-spacing: -0.5em;">Hello World!</p>
<p style="word-spacing: 0.0em;">Hello World!</p>
<p style="word-spacing: normal;">Hello World!</p>
<p style="word-spacing: 0.5em;">Hello World!</p>
<p style="word-spacing: 1.0em;">Hello World!</p>
```

该属性会受到`text-align: justify`的影响。

### §4.4.2 字符间距(`letter-spacing`)

`letter-spacing`决定了字符之间的距离。该属性默认为`normal`，等价于绝对长度`0`。正值时加大间距，负值时缩短间距。

```html
<p style="letter-spacing: 0.1em;">Hello, 世界!</p>
<p style="letter-spacing: 0em;">Hello, 世界!</p>
<p style="letter-spacing: normal;">Hello, 世界!</p>
<p style="letter-spacing: -0.1em;">Hello, 世界!</p>
```

## §4.5 文本转换(`text-transform`)

`text-transform`专门用于转变文本的大小写。

| `text-transform`属性值 | 作用                 |
| ---------------------- | -------------------- |
| `uppercase`            | 全大写               |
| `lowercase`            | 全小写               |
| `captialize`           | 每个单词的首字母大写 |
| `none`(缺省)           | 无转换               |

```html
<p>hello, ABC</p>
<p style="text-transform: none;">hello, ABC</p>
<p style="text-transform: uppercase;">hello, ABC</p>
<p style="text-transform: lowercase;">hello, ABC</p>
<p style="text-transform: capitalize;">hello, ABC</p>
```

## §4.6 文本装饰(`text-decoration`)

`text-decoration`属性决定文本的装饰形式。

| `text-decoration`属性值 | 作用                                         |
| ----------------------- | -------------------------------------------- |
| `none`(缺省)            | 无效果                                       |
| `underline`             | 下划线，等价于`<u></u>`                      |
| `overline`              | 上划线                                       |
| `line-through`          | 删除线，等价于`<strike></strike>`或`<S></S>` |

```html
<a href="google.com">Google</a>
<a href="google.com" style="text-decoration: none;">Google</a>
<p style="text-decoration: none;">hello, ABC.</p>
<p style="text-decoration: underline;">hello, ABC.</p>
<p style="text-decoration: overline;">hello, ABC.</p>
<p style="text-decoration: underline overline;">hello, ABC.</p>
<p style="text-decoration: line-through;">hello, ABC.</p>
```

`text-decoration`属性不参与继承，但是父元素产生的装饰线属性会覆盖在子元素上面，导致子元素无法通过声明`text-decoration: none`来去除自己身上的装饰线。但是子元素可以声明自己的装饰线，且其图层在父元素装饰线之上，起到覆盖的作用。

## §4.7 文本渲染效果(`text-rendering`)

`text-rendering`本质上不是CSS属性，而是一个SVG属性，决定了浏览器在显示文本时应该优先考虑什么方面。

| `text-rendering`属性值 | 作用                                         |
| ---------------------- | -------------------------------------------- |
| `auto`(缺省)           | 无标准，由浏览器自行决定                     |
| `optimizeSpeed`        | 优先考虑绘制速度，而不是清晰性（紧排和连字） |
| `optimizeLegibility`   | 优先考虑清晰性（紧排和连字），而不是绘制速度 |
| `geometricPrecision`   | 优先考虑SVG矢量精确性，以SVG绘制字形         |

## §4.8 文本阴影(`text-shadow`)

`text-shadow`属性可以给文本设置多个阴影，每组阴影值用逗号分隔。每个阴影由四个值定义：

1. 第一个属性值：颜色。可以为颜色关键字或函数式RGB颜色。实际上该属性可以放在首部或尾部。

   ```html
   <h1 style="text-shadow: black 0 0 4px;">123</h1> <!-- 生效 -->
   <h1 style="text-shadow: 0 black 0 4px;">123</h1> <!-- 无效 -->
   <h1 style="text-shadow: 0 0 black 4px;">123</h1> <!-- 无效 -->
   <h1 style="text-shadow: 0 0 4px black;">123</h1> <!-- 生效 -->
   ```

2. 第二个属性值：横向位置偏移距离。正数为右，负数为左。

3. 第三个属性值：纵向位置偏移距离。正数为下，负数为上。

4. 第四个属性值：模糊半径。可选。具体的模糊算法由浏览器决定。

```html
<html>
<head>
    <style>
        h1 { color: red; }
        .type1 { 
            text-shadow: 1em 0 5px black, -1em 0 5px blue; 
        }
    </style>
</head>
<body>
    <h1 class="type1">Hello</h1>
</body>
</html>
```

> 注意：大量阴影或模糊半径较大的阴影会消耗大量CPU性能。

## §4.9 空白(`white-space`)

`white-space`属性用于指定浏览器对空格、换行符、制表符的处理方式。

根据[MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/white-space)，`white-space`有以下取值：

| `white-space`属性值 | 换行符 | 空格和制表符 | 文本换行 | 行末空格 | 行末的其他空白分隔符 |
| :------------------ | :----- | :----------- | :------- | :------- | -------------------- |
| `normal`            | 合并   | 合并         | 换行     | 移除     | 挂起                 |
| `nowrap`            | 合并   | 合并         | 不换行   | 移除     | 挂起                 |
| `pre`               | 保留   | 保留         | 不换行   | 保留     | 不换行               |
| `pre-wrap`          | 保留   | 保留         | 换行     | 挂起     | 挂起                 |
| `pre-line`          | 保留   | 合并         | 换行     | 移除     | 挂起                 |
| `break-spaces`      | 保留   | 保留         | 换行     | 换行     | 换行                 |

```html
<html>
<body>
    <div style="width: 200px; background-color: gray;">
        <select>
            <option>normal</option>
            <option>nowrap</option>
            <option>pre</option>
            <option>pre-wrap</option>
            <option>pre-line</option>
            <option>break-spaces</option>
            <option>preserve nowrap</option>
        </select>
        <p>
            This is a very very very long sentence whose length is far longer than container's width.
            This is another very very very long sentence.
        </p>
    </div>
    <script>
        const selectDOM = document.querySelector("select");
        const paragraphDOM = document.querySelector("p");
        selectDOM.addEventListener("change", (e)=>{
            paragraphDOM.setAttribute("style", `white-space: ${e.target.value}`);
        });
    </script>
</body>
</html>
```

## §4.10 制表符宽度(`tab-size`)

`tab-size`用于指定制表符字符的宽度。该属性仅当`white-space`保留空格和制表符时才生效。

| `tab-size`属性值           | 作用                   |
| -------------------------- | ---------------------- |
| 整数值                     | 表示几个连续空格的长度 |
| 单位长度(例如`2px`,`1rem`) | 表示绝对长度           |

```html
<html>
<head>
    <style>
        p {white-space: pre;}
    </style>
</head>
<body>
    <div>
        <p style="white-space: normal;">&#09;This sentence start with a tab character.</p>
        <p style="tab-size: 1;">&#09;This sentence start with a tab character.</p>
        <p style="tab-size: 2;">&#09;This sentence start with a tab character.</p>
        <p style="tab-size: 4;">&#09;This sentence start with a tab character.</p>
        <p style="tab-size: 8;">&#09;This sentence start with a tab character.</p>
        <p>&#09;This sentence start with a tab character.</p>
    </div>
</body>
</html>
```

## §4.11 换行和断字

如果行宽较短，但是单词很长时，通常使用连字符。HTML提供了`&shy;`实体，Unicode也提供了`\u00ad`，两者都能供浏览器显示连字符时进行参考。`hyphens`属性决定连字符在何种情况下显示：

| `hyphens`属性值 | 作用                                     |
| --------------- | ---------------------------------------- |
| `manual`(缺省)  | 只根据手动生命的连字符显示连字符         |
| `auto`          | 自动根据语法和手动声明的连字符显示连字符 |
| `none`          | 不显示任何连字符                         |

```html
<!DOCTYPE html>
<html>

<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <style type="text/css">
        * {
            padding: 0;
            margin: 0;
        }

        #app-wrapper {
            position: absolute;
            cursor: move;
        }
        code {
            font-family:'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
            font-weight: bold;
        }
    </style>
</head>

<body>
    <div>
        <div>
            是否携带<code>&amp;shy;</code>实体:
            <select id="shy-entity">
                <option>是</option>
                <option>否</option>
            </select>
        </div>
        <div>
            <code>hyphens</code>属性值:
            <select id="hyphens">
                <option>manual</option>
                <option>auto</option>
                <option>none</option>
            </select>
        </div>
    </div>
    <div id="app-wrapper">
        <p id="paragraph" style="width: 100%;">Super&shy;cali&shy;fragi&shy;listic&shy;expi&shy;ali&shy;docious anti&shy;dis&shy;establish&shy;ment&shy;arian&shy;ism.</p>
    </div>
    <script>
        const shyDOM = document.querySelector("#shy-entity");
        const hyphensDOM = document.querySelector("#hyphens");
        const paragraphDOM = document.querySelector("#paragraph");
        shyDOM.addEventListener("change", (e)=>{
            if(e.target.value === "是"){
                paragraphDOM.innerHTML = "Super&shy;cali&shy;fragi&shy;listic&shy;expi&shy;ali&shy;docious anti&shy;dis&shy;establish&shy;ment&shy;arian&shy;ism."
            }else if(e.target.value === "否"){
                paragraphDOM.innerHTML = "Supercalifragilisticexpialidocious antidisestablishmentarianism."
            }
        });
        hyphensDOM.addEventListener("change", (e)=>{
            paragraphDOM.setAttribute("style", `width: 100%; hyphens:${e.target.value};`);
        })
    </script>
</body>
<script>
    /**
     * 创建一个可拖拽的矩形
     */
    function CreateDragRect(elm, options = {}) {
        if (!elm) throw new Error('el 必须是一个document对象');
        this.rect = elm;
        this.isLeftMove = true;
        this.isTopMove = true;
        this.rectDefaultPosition = options.position || 'fixed';
        this.rectDefaultLeft = options.x || 0;
        this.rectDefaultTop = options.y || 0;
        this.rectWidth = options.width || 100;
        this.rectHeight = options.height || 100;
        this.rectMinWidth = options.rectMinWidth || 5;  //最小宽度(超过之后不允许再缩放)
        this.rectMinHeight = options.rectMinHeight || 5;  //最小高度(超过之后不允许再缩放)
        this.rectBackgroundColor = options.background || '#ccc';
        this.dragIconSize = options.dragIconSize || '4px';
        this.dragIconColor = options.dragIconColor || '#f00';
        //主矩形是否可以移动
        this.isMove = false;
        this.initStyle();
        this.bindDragEvent(this.rect);
    }

    /**
     * 主矩形绑定move事件
     */
    CreateDragRect.prototype.bindDragEvent = function (dom, position) {
        const _this = this;
        dom.onmousedown = function (event) {
            event.stopPropagation();
            //按下矩形的时候可以移动，否则不可移动
            _this.isMove = !position;
            // 获取鼠标在wrapper中的位置
            let boxX = event.clientX - dom.offsetLeft;
            let boxY = event.clientY - dom.offsetTop;
            //鼠标移动事件(如果计算太高,有拖影)
            document.onmousemove = _this.throttle(function (moveEv) {
                let ev = moveEv || window.event;
                ev.stopPropagation();
                let moveX = ev.clientX - boxX;
                let moveY = ev.clientY - boxY;
                switch (position) {
                    case 'top-left':    //左上: 需计算top left width
                        _this.nwRectSize(moveX, moveY);
                        break;
                    case 'top-right':   //右上 计算top 和 height
                        _this.neRectSize(moveX, moveY);
                        break;
                    case 'right-bottom':  //只需计算当前鼠标位置
                        _this.seRectSize(moveX, moveY);
                        break;
                    case 'left-bottom': //计算left偏移量，计算w
                        _this.swRectSize(moveX, moveY);
                        break;
                    default:    //拖拽矩形
                        if (!_this.isMove) return null;
                        _this.moveRect(ev.clientX - boxX, ev.clientY - boxY);
                }
            }, 15);

            //鼠标松开，移除事件
            document.onmouseup = function (event) {
                document.onmousemove = null;
                document.onmouseup = null;
                // 存储当前的rect的宽高
                _this.rectWidth = _this.rect.offsetWidth;
                _this.rectHeight = _this.rect.offsetHeight;
                // 获得当前矩形的offsetLeft 和 offsetTop
                _this.rectOffsetLeft = _this.rect.offsetLeft;
                _this.rectOffsetTop = _this.rect.offsetTop;
            }
        }
    }

    /**
     * 初始化样式
     */
    CreateDragRect.prototype.initStyle = function () {
        this.rect.style.position = this.rectDefaultPosition;
        this.rect.style.width = this.rectWidth + 'px';
        this.rect.style.height = this.rectHeight + 'px';
        this.rect.style.left = this.rectDefaultLeft + 'px';
        this.rect.style.top = this.rectDefaultTop + 'px';
        this.rect.style.background = this.rectBackgroundColor;
        //依次为上 右 下 左
        let dragIcons = [
            {
                cursor: 'nw-resize',
                x: 'top',
                y: 'left'
            },
            {
                cursor: 'ne-resize',
                x: 'top',
                y: 'right'
            },
            {
                cursor: 'se-resize',
                x: 'right',
                y: 'bottom'
            },
            {
                cursor: 'sw-resize',
                x: 'left',
                y: 'bottom'
            }
        ];
        for (let i = 0, l = dragIcons.length; i < l; i++) {
            let icon = document.createElement('i');
            icon.id = Math.random().toString(36).substring(7);
            icon.style.display = 'inline-block';
            icon.style.width = this.dragIconSize;
            icon.style.height = this.dragIconSize;
            icon.style.position = 'absolute';
            icon.style.zIndex = 10;
            icon.style.cursor = dragIcons[i].cursor;
            icon.style.backgroundColor = this.dragIconColor;
            icon.style[dragIcons[i].x] = -parseInt(icon.style.width) / 2 + 'px';
            icon.style[dragIcons[i].y] = -parseInt(icon.style.height) / 2 + 'px';
            //绑定四个角的拖拽事件
            this.bindDragEvent(icon, `${dragIcons[i].x}-${dragIcons[i].y}`);
            //插入到矩形
            this.rect.appendChild(icon);
        }
    };

    /**
     * 移动主矩形
     */
    CreateDragRect.prototype.moveRect = function (x, y) {
        if (this.isTopMove && this.isLeftMove) {
            this.rect.style.left = x + 'px';
            this.rect.style.top = y + 'px';
        }
    };

    /**
     * 移动主矩形缩放 - 左上
     */
    CreateDragRect.prototype.nwRectSize = function (x, y) {
        //计算是否是最小宽度
        const { width, height, isLeftMove, isTopMove } = this.getMinSize(this.rectWidth - x, this.rectHeight - y);
        if (isTopMove) {
            this.rect.style.top = this.rectOffsetTop + y + 'px';
            this.rect.style.height = height + 'px';
        }
        if (isLeftMove) {
            this.rect.style.left = this.rectOffsetLeft + x + 'px';
            this.rect.style.width = width + 'px';
        }
    };

    /**
     * 移动主矩形缩放 - 左下
     */
    CreateDragRect.prototype.swRectSize = function (x, y) {
        //计算是否是最小宽度
        const { width, height, isLeftMove, isTopMove } = this.getMinSize(this.rectWidth - x, y);
        if (isLeftMove) {
            this.rect.style.left = this.rectOffsetLeft + x + 'px';
            this.rect.style.width = width + 'px';
        }
        if (isTopMove) {
            this.rect.style.height = height + 'px';
        }
    };

    /**
     * 移动主矩形缩放 - 右上
     */
    CreateDragRect.prototype.neRectSize = function (x, y) {
        //计算是否是最小宽度
        const { width, height, isTopMove, isLeftMove } = this.getMinSize(x, this.rectHeight - y);
        if (isTopMove) {
            this.rect.style.height = height + 'px';
            this.rect.style.top = this.rectOffsetTop + y + 'px';
        }
        if (isLeftMove) {
            this.rect.style.width = width + 'px';
        }
    };

    /**
     * 移动主矩形缩放 - 右下
     */
    CreateDragRect.prototype.seRectSize = function (x, y) {
        //计算是否是最小宽度
        const { width, height } = this.getMinSize(x, y);
        this.rect.style.width = width + 'px';
        this.rect.style.height = height + 'px';
    };

    /**
    * 节流函数
    * @param {*} fn 
    * @param {*} delay 
    */
    CreateDragRect.prototype.throttle = function (fn, delay) {
        let last = 0;
        return function () {
            let curr = Date.now();
            if (curr - last > delay) {
                fn.apply(this, arguments);
                last = curr;
            }
        }
    }

    /**
     * 获取宽高
     * @param {*} w 
     * @param {*} h 
     * @return { Object }
     */
    CreateDragRect.prototype.getMinSize = function (w, h) {
        let rectMinWidth = this.rectMinWidth;
        let rectMinHeight = this.rectMinHeight;
        //x拖拽
        this.isLeftMove = w >= this.rectMinWidth;
        //y拖拽
        this.isTopMove = h >= this.rectMinHeight;
        if (this.isLeftMove) rectMinWidth = w;
        if (this.isTopMove) rectMinHeight = h;
        return { width: rectMinWidth, height: rectMinHeight, isLeftMove: this.isLeftMove, isTopMove: this.isTopMove };
    }
    const dr = new CreateDragRect(document.getElementById('app-wrapper'), {
        x: 50,
        y: 100,
        width: 300,
        height: 100,
    });
</script>

</html>
```

我们知道，`<br>`与`\n`都是硬换行。当一个文本太长时，浏览器会自动为其换行，这种换行称为软换行。`work-brak`属性决定了软换行的行为。

| `word-break`属性值 | 作用                                   | 非CJK行为    | CJK行为      | 断字 |
| ------------------ | -------------------------------------- | ------------ | ------------ | ---- |
| `normal`(默认)     | 只在单词之间换行                       | 照常         | 照常         | 有   |
| `break-all`        | 可以在任何字符之间软换行               | 任何字符之间 | 任何字符之间 | 无   |
| `keep-all`         | 只在单词之间换行，除非单词只有一个字符 | 照常         | 序列两侧     | 有   |

`line-break`属性用于更精细地控制CJK的断字行为。

| `line-break` 属性值 | 作用                               |
| ------------------- | ---------------------------------- |
| `auto`(缺省)        | 让浏览器自主决定                   |
| `loose`             | 宽松的换行规则，用于文本较短的情况 |
| `normal`            | 常规的换行规则                     |
| `strict`            | 严格的换行规则                     |

## §4.12 书写模式(``writing-mode`)

不同的语言有着不同的书写方向。例如英语从左到右从上到下，阿拉伯语从右到左从上到下，汉语和日语从上到下，也可以从左到右，蒙古语从右到左。`writing-mode`用于控制书写模式：

| `writing-mode`属性值  | 作用               |
| --------------------- | ------------------ |
| `horizontal-tb`(缺省) | 从左到右，从上到下 |
| `vertical-rl`         | 从上到下，从右到左 |
| `vertical-lr`         | 从上到下，从左到右 |

```html
<html>
<head>
    <title>Figure</title>
    <style type="text/css">
    </style>
    <style type="text/css">
        p {
            border: 1px dotted;
            width: 15em;
            height: 6em;
        }
    </style>
</head>
<body>
    writing-mode:
    <select id="writing-mode">
        <option>horizontal-tb</option>
        <option>vertical-rl</option>
        <option>vertical-lr</option>
    </select>
    <div>
        <p>This is a paragraph of English text, largely unstyled.</p>
    </div>
    <script>
        const selectDOM = document.querySelector("#writing-mode");
        const paragraphDOM = document.querySelector("p");
        selectDOM.addEventListener("change", (e)=>{
            paragraphDOM.setAttribute("style", `writing-mode: ${e.target.value};`);
        });
    </script>
</body>
</html>
```

### §4.12.1 字符朝向(`text-orientation`)

当多语言字符混杂时，可以使用`text-orientation`属性控制字符的方向。

| `text-orientation`属性值 | 作用                           |
| ------------------------ | ------------------------------ |
| `mixed`                  | CJK字符头朝上，非CJK字符头朝右 |
| `upright`                | 字符全部头朝上                 |
| `sideways`               | 字符全部头朝右                 |

```html
<html>
<head>
    <title>Figure</title>
    <style type="text/css">
    </style>
    <style type="text/css">
        p {
            border: 1px dotted;
            width: 15em;
            height: 6em;
        }
    </style>
</head>
<body>
    writing-mode:
    <select id="text-orientation">
        <option>mixed</option>
        <option>upright</option>
        <option>sideways</option>
    </select>
    <div style="writing-mode: vertical-lr;">
        <p>この部分の日本語には英語と数字が混じっています。This section of Japanese is mixed with English and numbers.</p>
    </div>
    <script>
        const selectDOM = document.querySelector("#text-orientation");
        const paragraphDOM = document.querySelector("p");
        selectDOM.addEventListener("change", (e)=>{
            paragraphDOM.setAttribute("style", `text-orientation: ${e.target.value};`);
        });
    </script>
</body>
</html>
```

# §5 盒模型

## §5.1 元素的显示方式(`display`)

例如给定下列`<a>`超链接，使其从行内元素转变为块级元素，这样用户即使不点击文字，只需点击块级元素范围内，就能跳转。

```html
<html>
<head>
    <style>
        .navbar {
            width: 10rem;
        }
        .navbar a {
            display: block;
        }
        .navbar a:hover {
            background-color: lightgray;
        }
    </style>
</head>
<body>
    <div class="navbar">
        <a href="/home">Home</a>
        <a href="/news">News</a>
        <a href="/about">About</a>
    </div>
</body>
</html>
```

或者给定下列无序列表，使得列表中的每一项元素`<li>`都以行内方式显示：

```html
<html>
<head>
    <style>
        ul li {
            display: inline;
            border-right: 1px black solid;
            padding: 0 0.33em;
        }
        ul li:first-child {
            border-left: 1px black solid;
        }
    </style>
</head>
<body>
    <ul>
        <li>Alice.</li>
        <li>Bob.</li>
        <li>Carol.</li>
    </ul>
</body>
</html>
```

块级元素的后代可以是行内元素，但是行内元素的后代不能是块级元素。

```mermaid
graph LR
	subgraph "外边距Margin(内侧紧邻边框)"
		subgraph "内边距Padding(外侧紧邻边框)"
			subgraph "内容(默认为真正的高与宽)"
			end
		end
	end
```

## §5.2 盒模型尺寸模式(`box-sizing`)

任何盒模型都有自己的尺寸。然而盒模型包含了外边距、边框、内边距、内容这些组成部分，而`box-sizing`属性决定了盒模型的宽度和高度从哪个组成部分开始算起。

| `box-sizing`属性      | 作用                                      |
| --------------------- | ----------------------------------------- |
| `content-box`(缺省)   | `width`和`height`用于约束内容区域的尺寸   |
| `padding-box`(已弃用) | `width`和`height`用于约束内边距区域的尺寸 |
| `border-box`          | `width`和`height`用于约束边框区域的尺寸   |

```html
<html>
<head>
    <style>
        p {
            height: 150px; 
            width: 200px;
            background-color: lightgray; 
            border: 20px lightblue solid;
            padding: 10px 30px;
        }
        .box-sizing-content-box { box-sizing: content-box; }
        .box-sizing-padding-box { box-sizing: padding-box; }
        .box-sizing-border-box { box-sizing: border-box; }
    </style>
</head>
<body>
    box-sizing:
    <select>
        <option>content-box</option>
        <option>padding-box</option>
        <option>border-box</option>
    </select>
    <p>
        This is a simple sentence for showing box-sizing property.
    </p>
    <script>
        const paragraphDOM = document.querySelector("p");
        const boxsizingDOM = document.querySelector("select");
        boxsizingDOM.addEventListener("change", (e)=>{
            paragraphDOM.setAttribute("class", "box-sizing-" + e.target.value);
            console.log(paragraphDOM.attributes["class"]);
        });
    </script>
</body>
</html>
```

## §5.3 横向格式化属性

横向格式化属性指的是能影响元素在水平方向布局的属性，一共有以下七个：`margin-left`、`margin-right`、`border-left`、`border-right`、`padding-left`、`padding-right`、`width`。

这七个属性的值加在一起，必须等于父元素(容纳块)的宽度。其中`margin-left`、`margin-right`、`width`有且仅有一个的属性值设为`auto`，则浏览器会自动调整设为`auto`的属性，使得元素在水平方向上填满容纳块。

在机械制图中，我们学习过"过约束"的概念。同理，如果`margin-left`、`margin-right`、`width`都是具体的值，没有一个是`auto`时，这就是过约束状态，七个属性值相加不一定等于容纳块宽度。因此浏览器会自动将`margin-right`设为`auto`。

```html
<html>
<head>
    <style>
        body {
            padding: 0 0 0 0;
            background-color: aliceblue;
        }
        div {
            background-color: lightgray;
            margin-left: 10px;
            border-left: 10px solid lightblue;
            width: 50px;
            border-right: 10px solid lightblue;
            padding-left: 10px;
            margin-right: 10px;

            margin-bottom: 5px;
        }
        div:hover { background-color: gray; }
    </style>
</head>
<body>
    <div>Hello</div>
    <div style="width: auto;">Hello</div>
    <div style="margin-left: auto;">Hello</div>
    <div style="margin-right: auto;">Hello</div>
    <div style="width: auto; margin-left: auto;margin-right: auto;">Hello</div>
</body>
</html>
```

对于不同的`auto`属性值组合，CSS规定的处理策略如下：

| `margin-left` | `width` | `margin-right` | 作用                                    |
| ------------- | ------- | -------------- | --------------------------------------- |
| 具体值        | 具体值  | 具体值         | 过约束，右外边距设为`0`                 |
| `auto`        | 具体值  | `auto`         | 左右外边距长度相等，扩充至填满容纳块    |
| 具体值        | `auto`  | `auto`         | 右外边距设为`0`                         |
| `auto`        | `auto`  | 具体值         | 左外边距设为`0`                         |
| `auto`        | `auto`  | `auto`         | 左右外边距设为`0`，宽度扩充至填满容纳块 |

> 注意：CSS规定可以设置负外边距。此时`auto`的计算规则仍然遵从上表，只是形式上要求七个属性值相加为容纳块的宽度，实际上子元素完全有可能超出容纳块的显示范围。

## §5.4 纵向格式化属性

纵向格式化属性决定了元素在垂直方向的排版样式，包括`margin-top`、`border-top`、`padding-top`、`height`、`padding-bottom`、`border-bottom`、`margin-bottom`。

与横向格式化属性相似，这七个属性值相加的结构必须等于容纳块的高度。且其中只有三个属性可以设置为`auto`：`margin-top`、`height`、`margin-bottom`。

| `margin-top` | `height` | `margin-bottom` | 作用                                                         |
| ------------ | -------- | --------------- | ------------------------------------------------------------ |
| 具体值       | 具体值   | 具体值          | 若七个属性值相加小于等于容纳块高度，则自动扩充内容区域高度；如大于容纳块高度，则自动缩减内容区高度；若内容区高度缩减至`0`，则突破相加为定值的限制。 |
| `auto`       | 任意     | 任意            | 上外边距设为`0`                                              |
| 任意         | 任意     | `auto`          | 下外边距设为`0`                                              |
| 任意         | `auto`   | 任意            | 自动扩充内容区高度至填满容纳块                               |

```html
<html>
<head>
    <style>
        body {
            padding: 0 0 0 0;
            background-color: aliceblue;
        }
        .content {
            width: 20em;
            background-color: lightgray;
            box-sizing: border-box;
            margin-top: 10px;
            border-top: 10px solid lightblue;
            padding-top: 0.5em;
            height: 6em;
            padding-bottom:10em;
            border-bottom: 10px solid lightblue;
        }
        .content:hover { background-color: gray; }
    </style>
</head>
<body>
    <div style="height: 10rem;">
        <div class="content">This is a long sentence for testing vertical alignment properties.</div>
    </div>
</body>
</html>
```

只有相邻的纵向外边距会自动折叠，而内边距和边框不会。可以这么理解：准备两个白色卡片，每个卡片周围有一圈矩形的透明塑料。现在将这两个卡片重叠，只要白色部分之间不重叠、白色部分与透明塑料不重叠即可，透明塑料之间的重叠是允许的。

```html
<html>
<head>
    <style>
        ul li {
            margin-bottom: 15px;
            margin-top: 10px; /* 该属性似乎无效 */
        }
    </style>
</head>
<body>
    <div style="display: inline;">
        <ul> <!-- 貌似间隔25px，实际间隔max(15px,10px)=15px -->
            <li>Alice</li>
            <li>Bob</li>
            <li>Carol</li>
        </ul>
    </div>
</body>
</html>
```

同理，`margin-top`与`margin-bottom`也能设置负边距：

```html
<html>
<head>
</head>
<body>
    <div style="margin-bottom: 1em;">This is the first sentence.</div>
    <div style="margin-top: -1em;">This is the second sentence.</div>
    <br/>
    <div style="margin-bottom: 1em;">This is the first sentence.</div>
    <div style="margin-top: -1.5em;">This is the second sentence.</div>
	<div style="width: 420px; margin-right: 10px; margin-left: 10px; margin-bottom: 0; border: 3px solid black;">
    	<p style="margin-top: -10px; margin-right: 10px; margin-left: 10px; margin-bottom: 0; border: 3px solid gray;">
        	A paragraph
    	</p>
    	A  div
	</div>
</body>
</html>
```

## §5.5 宽度和高度(`width`/`height`)

在`box-sizing`属性缺省的情况下，元素的宽度`width`指的是从左内边界到右内边界的距离，元素的高度`height`指的是从上内边界到下内边界的距离。

这两个属性不能应用到行内非置换元素上，比如`<a>`。如果非要使用这两种属性，可以考虑将这种元素变为非行内元素，例如`display: block`或`display: inline-clock`。

```html
<html>
<head>
    <style>
        a:link {
            background-color: lightgray;
            height: 10px;
            width: 10px;
        }
    </style>
</head>
<body>
    <a href="/" style="display: initial;">Baidu</a>
    <br/>
    <a href="/" style="display: block;">Baidu</a>
    <br/>
    <a href="/" style="display: inline-block;" >Baidu</a>
</body>
</html>
```

## §5.6 内边距(`padding`)

`padding`的属性值可以为以下类型：

| 第一个参数 | 第二个参数 | 第三个参数 | 第四个参数 | 作用                                             |
| ---------- | ---------- | ---------- | ---------- | ------------------------------------------------ |
| 顶内边距   | 右内边距   | 下内边距   | 左内边距   |                                                  |
| 顶内边距   | 右内边距   | 下内边距   |            | 令左内边距等于右内边距                           |
| 顶内边距   | 右内边距   |            |            | 令左内边距等于右内边距，下内边距等于顶内边距     |
| 顶内边距   |            |            |            | 令左内边距等、下内边距。右内边距全部等于顶内边距 |

这幅图总结了以上表格的内容：当箭头指向的某一侧属性未指定时，CSS就会默认尝试继承源头的已存在的属性值。

```mermaid
flowchart LR
	top --> right & bottom
	right --> left
```

当属性值为百分数时，该属性参考的是父元素内容区的宽度：

```html
<html>
<head>
    <style>
        div {
            margin-top: 20%;
            margin-left: 30%;
            margin-right: 30%;
            background-color: lightgray;
            border: 1px solid black;
        }
    </style>
</head>
<body>
    <div>Hello World!</div>
</body>
</html>
```

对于行内非置换元素而言，`padding`属性在垂直方向上(`padding-top`和`padding-bottom`)不影响实际显示的效果，没有视觉效果。除非该元素有背景色，会导致背景色沿元素上下延伸。在水平方向时，则会有实际的视觉效果：

```html
<html>
<head>
    <style>
        strong { background-color: lightgray; }
        .style1 { padding-top: 0; }
        .style2 { padding-top: 0.5em; }
        .style3 {padding-left: 1em;}
    </style>
</head>
<body>
    <p>Hello <strong class="style1">World!</strong></p>
    <p>Hello <strong class="style2">World!</strong></p>
    <p>Hello <strong class="style3">World!</strong></p>
</body>
</html>
```

对于行内置换元素而言，`padding`属性在垂直和水平方向上都会产生视觉效果，甚至会影响行高：

```html
<html>
<head>
    <style>
        img {
            width: 30px;
            padding-top: 1.5rem;
            padding-bottom: 0.5rem;
            background-color: lightblue;
        }
    </style>
</head>
<body>
    <div style="width: 200px; background-color: lightgray;">
        This is a picture which has a huge padding-top: <img src="https://www.google.com/images/branding/googlelogo/2x/googlelogo_light_color_92x30dp.png"/>
    </div>
</body>
</html>
```

使用`box-decoration-box`属性，可以改变行内元素每一行的内边距行为：

```html
<html>
<head>
    <style>
        body {
            background-color: darkgray;
        }
        div.root {
            border: 1px solid black;
            width: 8rem;
            margin-bottom: 1rem;
            background-color: black;
        }
        span {
            margin: 1rem;
            line-height: 3rem;
            box-shadow: 8px 8px 10px 0 #ff1492, -5px -5px 5px 0 #00f, 5px 5px 15px 0 #ff0;
            border-style: solid;
            border-radius: 8px;
            color: white;
        }
    </style>
</head>
<body>
    <div class="root">
        <span>这段话横跨了两行。</span>
    </div>
    <div class="root">
        <span style="box-decoration-break: clone; -webkit-box-decoration-break: clone;">这段话横跨了两行。</span>
    </div>
</body>
</html>
```



## §5.7 边框(`border`)

边框时元素的内容区或内边距外部周围的一条或多条线段，由三要素组成：宽度、式样和颜色。

- 宽度(`border-width`)：缺省为`2px`
- 式样(`border-style`)：缺省为`none`
- 颜色(`border-color`)：缺省为元素自身的前景色(`color`)，如果没有则从父元素继承

### §5.7.1 边框式样(`border-style`)

`border-style`属性用于指定边框的式样。当其属性值有四个、三个、两个、一个值时，参考[§5.2.5 内边距(`padding`)](###§5.2.5 内边距(`padding`))一节的规则。当然，也可以分别使用`border-top-style`、`border-right-style`、`border-bottom-style`、`border-left-style`。

| `border-style`属性值 | 作用                             |
| -------------------- | -------------------------------- |
| `none`               | 不显示边框                       |
| `hidden`             | 除表格样式冲突之外，等价于`none` |
| `solid`              | 实线边框                         |
| `dotted`             | 点线边框                         |
| `dashed`             | 短划线边框                       |
| `double`             | 双实线边框                       |
| `groove`             | 雕刻效果，与`ridge`相对          |
| `ridge`              | 浮雕效果，与`groove`相对         |
| `inset`              | 陷入效果，与`outset`相对         |
| `outset`             | 突出效果，与`inset`相对          |

```html
<html>
<head>
    <style>
        div {
            width: 20rem;
            margin-bottom: 0.5rem;
            border-width: 3px;
        }
    </style>
</head>
<body>
    <div style="border-style: none;">border-style: none</div>
    <div style="border-style: hidden;">border-style: hidden</div>
    <div style="border-style: solid;">border-style: solid</div>
    <div style="border-style: dotted;">border-style: dotted</div>
    <div style="border-style: dashed;">border-style: dashed</div>
    <div style="border-style: double;">border-style: double</div>
    <div style="border-style: groove;">border-style: groove</div>
    <div style="border-style: ridge;">border-style: ridge</div>
    <div style="border-style: inset;">border-style: inset</div>
    <div style="border-style: outset;">border-style: outset</div>
    <div style="border-style: none;">border-style: none</div>
    <div style="border-style: solid dotted double inset;">border-style: solid dotted double inset</div>
</body>
</html>
```

### §5.7.2 边框宽度(`border-width`)

`border-width`用于指定边框的宽度。该属性只能设置一个属性值，而且应用于各个方向。如果向分别设置各个方向的边框宽度，只能使用`border-top-width`、`border-right-width`、`border-bottom-width`、`border-left-width`分别指定。

| `border-width`属性值 | 作用                                              |
| -------------------- | ------------------------------------------------- |
| 长度                 | 当`border-style`为`none`或`hidden`时，强制设为`0` |
| `thin`               |                                                   |
| `medium`(缺省)       |                                                   |
| `thick`              |                                                   |

```html
<html>
<head>
    <style>
        div {
            width: 20rem;
            margin-bottom: 0.5rem;
            border-style: outset;
        }
        .style1 {
            border-top-width: 2px;
            border-right-width: 5px;
            border-bottom-width: 10px;
            border-left-width: 20px;
            border-style: solid dashed double inset;
        }
    </style>
</head>
<body>
    <div style="border-width: 1px;">border-width: 1px</div>
    <div style="border-width: thin;">border-width: thin</div>
    <div style="border-width: 2px;">border-width: 2px</div>
    <div style="border-width: medium;">border-width: medium</div>
    <div style="border-width: 5px;">border-width: 5px</div>
    <div style="border-width: thick;">border-width: thick</div>
    <div style="border-width: 10px;">border-width: 10px</div>
    <div style="border-width: 20px;">border-width: 20px</div>
    <div style="border-width: 40px;">border-width: 40px</div>
    <div class="style1">Mixed border-width</div>
</body>
</html>
```

### §5.7.3 边框颜色(`border-color`)

`border-color`属性用于指定边框的颜色。当其属性值有四个、三个、两个、一个值时，参考[§5.2.5 内边距(`padding`)](###§5.2.5 内边距(`padding`))一节的规则。当然，也可以分别使用`border-top-color`、`border-right-color`、`border-bottom-color`、`border-color`。

```html
<html>
<head>
    <style>
        div {
            width: 10rem;
            border-style: solid;
            border-width: 10px;
            border-color: black blue red green;
        }
    </style>
</head>
<body>
    <div>Hello World!</div>
</body>
</html>
```

> 注意：之前我们说过，当`border-style`为`none`或`hidden`时，那么就算强制声明`border-width`，CSS也会将其视为`0`。如果我们就是想让边框的宽度占着位，同时不想让边框本体显示，也就是设置透明边框，可以考虑将`border-color`设为`transparent`。

对于行内非置换元素而言，垂直方向的边框并不会影响`line-height`，而水平方向的边框会推开文本。而对于行内置换元素而言，任何方向的边框都会推开文本：

```html
<html>
<head>
    <style>
        strong {
            border-top: 5px solid lightgray;
            border-bottom: 20px solid lightcoral;
            border-left: 1rem solid lightblue;
            border-right: 1rem solid lightgreen;
        }
    </style>
</head>
<body>
    <div style="width: 150px;">
        This is a long sentence which takes up <strong>many</strong> lines.
    </div>
</body>
</html>
```

### §5.7.4 圆角边框(`border-radius`)

`border-radius`属性用于设置边框的圆角半径。当其属性值有四个、三个、两个、一个值时，参考[§5.2.5 内边距(`padding`)](###§5.2.5 内边距(`padding`))一节的规则。当然，也可以分别使用`border-top-left-radius`、`border-top-right-radius`、`border-bottom-left-radius`、`border-bottom-right-radius`。

圆角的绘制过程如下所述：

1. 找到该圆角半径所描述的角落顶点
2. 以该顶点为圆心，圆角半径为半径绘制圆
3. 每个顶点重复以上步骤，得到四个圆，取其圆弧与公切线线段。

> 注意：如果某个圆的半径过大，使得公切线线段难以判断，则强制令其半径缩小到刚好不发生冲突的长度。
>
> 例如`border-radius: 9999em`的作用是：让元素边框中长度最短的两侧变成半圆形：
>
> ```html
> <html>
> <head>
>     <style>
>         div {
>             background-color: lightgray;
>             border-radius: 9999em;
>             margin-bottom: 1rem;
>         }
>     </style>
> </head>
> <body>
>     <div style="width: 100px; height: 50px;"></div>
>     <div style="width: 75px; height: 50px;"></div>
>     <div style="width: 50px; height: 50px;"></div>
>     <div style="width: 25px; height: 50px;"></div>
> </body>
> </html>
> ```

如果圆角半径为百分数，则百分数的计算参照物为相邻一边的变长。例如某矩形的尺寸为`10px*5px`，圆角半径为`20%`，则圆角的长半径为`2px`，短半径为`1px`，即为椭圆的一部分。

我们还可以分别指定圆角的横向半径和纵向半径，只需用斜线隔开即可。斜线两侧仍然可以填1~4个属性值：

```html
<html>
<head>
    <style>
        div {
            width: 200px; 
            height: 100px;
            background-color: lightgray;
            margin-bottom: 1rem;
        }
    </style>
</head>
<body>
    <div style="border-radius: 2em / 1em;"></div>
    <div style="border-radius: 1em 1.5em 2em 2.5em / 2.5em 2em 1.5em 1em;"></div>
</body>
</html>
```

### §5.7.5 图像边框(`border-image`)

到此为止，我们介绍的边框都是由浏览器根据规则绘制的矢量图，样式不够丰富。`border-image`系列属性允许我们加载外部图像作为边框。

`border-image-source`用于指示图像边框的资源地址。在以下例子中，边框的四个角都有一个圆点图片：

```
<html>
<head>
    <style>
        div {
            width: 200px; 
            height: 100px;
            background-color: lightgray;
            border-width: 25px;
            border-style: solid;
            border-image-source: url("https://meyerweb.github.io/csstdg4figs/08-padding-borders-outlines-and-margins/i/circle.png");
        }
    </style>
</head>
<body>
    <div></div>
</body>
</html>
```

> 注意：`border-image`系列属性生效的前提是`border-style`不为`none`和`hidden`。

`border-image-slice`属性在图像上放置4条裁剪线，这4条裁剪线将整个图像边框分为了九宫格，其中包括四个角、四条边和一个中心。该属性的默认值为`100%`CSS将四个角作为边框四个角使用的图像，四条边作为边框四边使用的图像，至于一个中心则默认废弃不用，除非为其属性值添加`fill`。`border-image-slice`的属性值依然可以接受1~4个值，分别指示上、右、下、左四边的偏移量，可以使用百分数表示比例或纯数字表示像素数：

> 注意：`border-image-slice`除了接受正常的百分数以外，还有以下特例：
>
> - 当`border-image-slice`检测到留给四条边的区域面积不为正，而是小于等于`0`时，就不显示四条边的图像。
> - 准确地来说，四个角使用的图像分别是由对应的两条裁剪线法向量决定的二维子平面。因此当裁剪线重叠甚至越过时，四个角使用的图像会有公共部分。

```html
<html>
<head>
    <style>
        div {
            width: 200px; 
            height: 100px;
            background-color: lightgray;
            margin-bottom: 0.5rem;
            border-width: 25px;
            border-style: solid;
            border-image-source: url("https://meyerweb.github.io/csstdg4figs/08-padding-borders-outlines-and-margins/i/circle.png");
        }
    </style>
</head>
<body>
    <div style="border-image-slice: 30%;"></div>
    <div style="border-image-slice: 30% fill;"></div>
    <div style="border-image-slice: 49.9%;"></div>
    <div style="border-image-slice: 50%;"></div>
    <div style="border-image-slice: 80%;"></div>
</body>
</html>
```

默认情况下，图像边框的长度取决于`border-width`属性。如果开发者想让图像边框的长度不同于`border-width`，可以使用`border-image-width`属性。该属性没有下属的子属性，可以接收1~4个参数。其属性值可以接受以下单位：

- 绝对长度单位：效果等价于`border-width`
- 纯数字：这一边`border-width`的倍数
- 百分数：以边框图片的这一边长度为基准
- `auto`：使用`border-image-slice`的计算结果，如果不存在则使用`border-width`

```html
<html>
<head>
    <style>
        div {
            width: 200px; 
            height: 100px;
            background-color: lightgray;
            margin-bottom: 0.5rem;
            border-width: 25px;
            border-style: solid;
            border-image-source: url("https://meyerweb.github.io/csstdg4figs/08-padding-borders-outlines-and-margins/i/circle.png");
            border-image-slice: 49.9%;
        }
    </style>
</head>
<body>
    <!-- border-image-source使用的图片为50px×50px -->
    <div style="border-image-width: 1;"></div>
    <div style="border-image-width: 25px;"></div>
    <div style="border-image-width: 10%;"></div>
    <div style="border-image-width: auto;"></div>
</body>
</html>
```

我们已经知道了`padding`属性可以防止边框与内容区挨的太近。其实除了内边距以外，还可以从边框入手，在边框区域内扩大边框的粗细程度，让原先的图像边框向外扩充。这就是`border-image-outset`属性。使用审查工具，就能看到`padding`和`border-image-outset`的作用区域并不一样。

```html
<html>
<head>
    <style>
        div {
            width: 200px; 
            height: 100px;
            background-color: lightgray;
            padding: 0.5rem;
            margin: 3rem;

            border-width: 25px;
            border-style: solid;
            
            border-image-slice: 49.9%;
            border-image-source: url("https://meyerweb.github.io/csstdg4figs/08-padding-borders-outlines-and-margins/i/circle.png");
            border-image-outset: 1rem;
        }
    </style>
</head>
<body>
    <div class="style1"></div>
    <script>
        window.onload = () => {
            const divDOM = document.getElementsByTagName("div");
            console.log(divDOM);
            for(let i = 0 ; i < divDOM.length ; ++i){
                divDOM[i].innerText = "这是一段文本。".repeat(6);
            }            
        };
    </script>
</body>
</html>
```

到目前位置，四条边的图像边框都是拉伸而成的。`border-image-repeat`属性控制着各边的边框图像处理方式。其属性值可以有1~2个关键词，分别控制纵轴和横轴：

| `border-image-repeat`属性值 | 作用                                                         |
| --------------------------- | ------------------------------------------------------------ |
| `stretch`(缺省)             | 拉伸图像                                                     |
| `repeat`                    | 沿中轴平铺图像，到边缘处直接截断                             |
| `round`                     | 沿该边重复$\displaystyle\text{round}(\frac{边框该边的长度}{图像该边的长度})$次，并辅以拉伸图像 |
| `space`                     | 沿该边重复$\displaystyle\text{floor}(\frac{边框该边的长度}{图像该边的长度})$次，并调节各段间距 |

```html
<html>
<head>
    <style>
        div {
            width: 200px; 
            height: 100px;
            background-color: lightgray;
            padding: 0.5rem;
            margin: 3rem;

            border-width: 25px;
            border-style: solid;
            
            border-image-slice: 25%;
            border-image-source: url("https://meyerweb.github.io/csstdg4figs/08-padding-borders-outlines-and-margins/i/circle.png");
        }
    </style>
</head>
<body>
    <div style="border-image-repeat: stretch;"></div>
    <div style="border-image-repeat: repeat;"></div>
    <div style="border-image-repeat: round;"></div>
    <div style="border-image-repeat: space;"></div>
    <script>
        window.onload = () => {
            const divDOM = document.getElementsByTagName("div");
            console.log(divDOM);
            for(let i = 0 ; i < divDOM.length ; ++i){
                divDOM[i].innerText = "这是一段文本。".repeat(6);
            }            
        };
    </script>
</body>
</html>
```

现在我们已经介绍了五个相关的图像边框属性：`border-image-source`、`border-image-slice`、`border-image-width`、`border-image-outset`、`border-image-repeat`。这五个子属性合在一起，可以简写为`border-image`属性：

```
border-image: <source>
border-image: <source> <slice> / <width>? / <outset>? <repeat>?
```

## §5.8 轮廓(`outline`)

`outline`属性决定元素的轮廓，可以的取值为：

```css
outline: <ouline-color>? <outline-style>? <outline-width>? 
```

轮廓与边框的区别体现在三个方面：

1. 轮廓不占空间

2. 轮廓可以不是矩形

   ```html
   <html>
   <head>
       <style>
           div {
               width: 200px;
               background-color: lightgray;
               margin-bottom: 1rem;
           }
           span.style1 {
               outline: dotted 2px black;
           }
           span.style2 {
               border: dotted 2px black;
           }
       </style>
   </head>
   <body>
       <div style="width: 200px;">
           这是一段话。
           <span class="style1">这是一段话。这是一段话。这是一段话。这是一段话。</span>
           这是一段话。
       </div>
       <div style="width: 200px;">
           这是一段话。
           <span class="style2">这是一段话。这是一段话。这是一段话。这是一段话。</span>
           这是一段话。
       </div>
   </body>
   </html>
   ```

3. 浏览器只在元素处于`:focus`状态时渲染轮廓

4. 不能给单边设置轮廓

### §5.8.1 轮廓式样(`outline-style`)

除了`hidden`换为`auto`以外，轮廓式样的取值与边框式样的取值完全一致。详见[§5.7.1 边框式样(`border-style`)](###§5.7.1 边框式样(`border-style`))一节。

```html
<html>
<head>
    <style>
        div {
            width: 20rem;
            margin-bottom: 0.5rem;
            outline-width: 3px;
        }
    </style>
</head>
<body>
    <div style="outline-style: none;">outline-style: none</div>
    <div style="outline-style: auto;">outline-style: auto</div>
    <div style="outline-style: solid;">outline-style: solid</div>
    <div style="outline-style: dotted;">outline-style: dotted</div>
    <div style="outline-style: dashed;">outline-style: dashed</div>
    <div style="outline-style: double;">outline-style: double</div>
    <div style="outline-style: groove;">outline-style: groove</div>
    <div style="outline-style: ridge;">outline-style: ridge</div>
    <div style="outline-style: inset;">outline-style: inset</div>
    <div style="outline-style: outset;">outline-style: outset</div>
</body>
</html>
```

### §5.8.2 轮廓宽度(`outline-width`)

轮廓宽度的取值与边框宽度的取值完全一致。唯一的区别是`outline-width`没有下属的子属性，不能为某一边设置特定的宽度。

### §5.8.3 轮廓颜色(`outline-color`)

轮廓颜色的取值与边框颜色的取值完全一致。

## §5.9 外边距(`margin`)

开发者也可以针对每一遍设置外边距属性，可以接收1~4个关键字使用子属性`margin-top`、`margin-right`、`margin-bottom`、`margin-left`即可。

### §5.9.1 外边距折叠

在[§5.4 纵向格式化属性](##§5.4 纵向格式化属性)一节我们已经讨论了外边距的折叠行为。

```html
<html>
<head>
    <style>
        div {
            width: 200px;
            background-color: lightgray;
            margin-bottom: 1rem; /* 发生重叠 */
        }
        div:not(:first-child) {
            margin-top: 1rem; /* 发生重叠 */
        }
    </style>
</head>
<body>
    <div style="width: 200px;">123</div>
    <div style="width: 200px;">123</div>
    <div style="width: 200px;">123</div>
</body>
</html>
```

如果子元素的外边距超出了父元素的范围，那么子元素的外边距虽然不会计入父元素的外边距，但实际上也会被当成父元素外边距的一部分。

```html
<html>
<head>
    <style>
        header { background-color: lightblue;}
        h1 { margin: 1em; }
    </style>
</head>
<body>
    <header>
        <h1>Hello World!</h1>
    </header>
</body>
</html>
```

### §5.9.2 负外边距

给子元素设置负外边距，可以让子元素从父元素中冒出来。这一特性在平面设计中非常好用：

```html
<html>
<head>
    <style>
        div {
            width: 500px; 
            background-color: lightgray;
            margin: 1rem;
            border: 1px solid; /* 很重要！ */
        }
        h1 { 
            padding: 2rem 2rem 0rem 2rem;
        }
        p { 
            margin: 0rem;
            padding: 1rem;
        }
        p.punch {
            background-color: white;
            margin-left: 5rem;
            margin-right: 0px;
            border-width: 2px;
            border-style: solid;
            border-right-style: hidden;
        }
        p.mond {
            background-color: rgba(255, 0, 0, 0.2);
            margin: 1em 3em -3em -3em;
        }
    </style>
</head>
<body>
    <div>
        <a href="https://baijiahao.baidu.com/s?id=1699590831615037098&wfr=spider&for=pc"><h1>2021年5月12日外交部发言人华春莹主持例行记者会</h1></a>
        <p>香港中评社记者：昨天中国公布第七次人口普查结果。之前《金融时报》发表所谓独家消息，......，称“中国面临人口危机”，认为中国人口出生率下降、老龄化问题加剧、劳动力人口萎缩会对中国经济造成严重影响，并预言“在可预见的未来，中国可能无法取代美国成为最大的经济体”，甚至会影响中国领导人承诺实现的“中国梦”。对此你怎么看？</p>
        <p class="punch">华春莹：......，根据国家统计局权威发布，......。这个数据表明，中国人口十年来继续保持低速增长态势，中国人口总量持续增长，仍然是世界第一人口大国。人口质量稳步提升，人口受教育程度明显提高。人口结构调整变化，性别结构改善。人口流动集聚的趋势更加明显，城镇化水平持续提高。......</p>
        <p>......，中国人口14.1178亿，不是仍然比美欧国家人口总数加起来还多吗？不知有关媒体记者如何摇身变成人口统计学专家和社会学家，从何得出“中国人口危机说”的结论？......</p>
        <p class="mond">回顾一下过去几十年来，在中国发展的几乎每一个关键阶段，西方都会对中国做出种种评判和预测，出现了各种各样的论调，如各种版本的“中国威胁论”“中国崩溃论”。但随着中国持续发展，这些论调后来都一一被事实打脸。中国已经开启全面建设社会主义现代化国家新征程，我们将继续沿着已经被证明是正确的道路坚定前行。希望热衷于炮制各种涉华论调的人也能和我们中国人民一样健康快乐地生活，共同迎接并且见证“中国梦”的实现。</p>
    </div>
</body>
</html>
```

> 注意：边框会影响内容区的范围。我们已经知道盒模型由内容区、内边距、边框、外边距四部分组成，而且`background`只包括内边距和内容区。然而，如果我们不设置边框，而且子元素的外边距为负值，想冒出父元素的`auto`高度范围，那么内容区会在垂直方向上自动扩充，将本应冒出父元素的子元素重新包含到父元素的内容区中。
>
> 要让子元素真正的从父元素中冒出，可以考虑给边框设置透明颜色：
>
> ```css
> border: 1px solid rgba(0, 0, 0, 0);
> ```

### §5.9.3 行内元素的外边距

对于行内非置换元素而言，外边距不会在垂直方向上产生影响，只会在水平方向上有影响。同理，将其设置为父外边距，则元素两端可能会与周围的内容重叠。

```html
<html>
<head>
    <style>
        span { background-color: lightblue; }
        strong {background-color: lightpink;}
        strong.style1 {
            margin-top: 25px;
            margin-bottom: 50px;
        }
        strong.style2 {
            margin-left: 25px;
            margin-right: 25px;
        }
    </style>
</head>
<body>
    <span><strong class="style2">Hello</strong>, <strong class="style1">World</strong>!</span>
</body>
</html>
```

对于行内置换元素而言，外边距在垂直和水平方向上都能产生影响。

# §6 背景与前景

CSS可以为任何元素设置前景色和背景色。

## §6.1 前景色(`color`)

`color`属性用于设置元素的前景色。其中前景指的是元素的文本和四周的边框。如果指定了`border-color`，那么边框的颜色将优先使用`border-color`。

```html
<html>
<head>
    <style>
        div {
            width: 20rem;
            margin-bottom: 1em;
            border-style: solid;
        }
    </style>
</head>
<body>
    <div style="color: red;">Hello World!</div>
    <div style="color: blue;">Hello World!</div>
    <div style="color: green;">Hello World!</div>
    <div style="color: green; border-color: blue;">Hello World!</div>
</body>
</html>
```

`color`属性也可应用于表单元素的文字上：

```html
<html>
<head>
    <style>
        select { color: red; }
        label { color: red; }
        button { color: red ;}
    </style>
</head>
<body>
    <select>
        <option>Alice</option>
        <option>Bob</option>
        <option>Carol</option>
    </select>
    <input type="checkbox" id="demo1"><label for="demo1">Hello</label></input>
    <input type="checkbox" id="demo2"><label for="demo2">World</label></input>
    <button>Click me</button>
</body>
</html>
```

## §6.2 背景 

### §6.2.1 背景色(`background-color`)

`background-color`属性用于设置元素的背景色。其中背景指的是元素的内容区、内边距区和边框区。

在实际项目中，`color`与`background-color`通常配套使用。因为浏览器的默认`color`与`background-color`千奇百怪，如果只设置其中的一个，那么浏览器的另一个可能就会发生冲突，使得文字与背景颜色难以辨认，不利于用户阅读。

```html
<html>
<head>
    <style>
        h1 {
            background-color: rgb(20%,20%,20%);
            color: white;
            padding: 0.5rem;
        }
        a:link {
            color: blue;
            background-color: lightgray;
        }
        a:visited {
            color: red;
            background-color: gray;
        }
        a:hover {
            color: greenyellow;
            background-color: gray;
        }
    </style>
</head>
<body>
    <h1>这是一段标题</h1>
    <p>
        这是一段文本。<a href="./">这是一个超链接。</a>
    </p>
</body>
</html>
```

这里有个小技巧——边框和背景颜色可以配合使用，实现"双色边框"的效果：

```html
<html>
<head>
    <style>
        h1 {
            background-color: skyblue;
            padding: 0.5rem;
            border: 0.5rem solid lightblue;
        }
        div {
            padding: 0.5rem;
            background-color: rgb(30%, 30%, 30%);
            color: white;
        }
    </style>
</head>
<body>
    <h1>
        <div>这个黑色&lt;div&gt;被"双色边框"覆盖了</div>
    </h1>
</body>
</html>
```

### §6.2.2 裁剪背景(`background-clip`)

我们知道，`background`的范围默认为内容区、内边距区和边框区。`background-clip`属性可以更精细的控制背景的范围。如果设置了`border-radius`，就等边框渲染完毕后，根据圆角边框的范围再做一次裁剪。

| `background-clip`属性 | 作用                                                         |
| --------------------- | ------------------------------------------------------------ |
| `border-box`          | 背景的范围为内容区、内边距区、边框区                         |
| `padding-box`         | 背景的范围为内容区、内边距区                                 |
| `content-box`         | 背景的范围为内容区                                           |
| `text`                | 背景的范围仅为文字，相当于背景与文字取交集(前提是`color: transparent`) |

> 注意：根据[MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-clip#%E6%B5%8F%E8%A7%88%E5%99%A8%E5%85%BC%E5%AE%B9%E6%80%A7)，截止到2023年，Chromium系浏览器仍不支持`background:  text`属性值。该属性值仅被`-webkit-background-clip: text`支持。

```html
<html>
<head>
    <style>
        div {
            width: 40rem;
            padding: 0.5rem;
            margin-bottom: 1rem;
            border: 1rem dashed black;
            background-color: royalblue;
        }
    </style>
</head>
<body>
    <div style="background-clip: border-box;">background-clip: border-box</div>
    <div style="background-clip: padding-box;">background-clip: padding-box</div>
    <div style="background-clip: content-box;">background-clip: content-box</div>
    <div style="color: transparent; -webkit-background-clip: text; background-clip: text;">background-clip: content-box</div>
</body>
</html>
```

### §6.2.3 背景图(`background-image`)

`background-image`属性允许加载外部图像作为背景，是一个非继承属性。

```html
<html>
<head>
    <style>
        div {
            width: 30rem;
            height: 8rem;
            padding: 0.5rem;
            padding-left: 9rem;
            margin-bottom: 1rem;
            border: 1px solid black;

            background-color: lightgray;
            background-repeat: no-repeat;
        }
    </style>
</head>
<body>
    <div style="background-image: url('https://cdn-icons-png.flaticon.com/128/1164/1164651.png');">Hello World!</div>
</body>
</html>
```

在实际工程中，`background-image`与`background-color`通常配套使用，防止`background-image`资源失效而导致排版混乱。

### §6.2.4 背景定位(`background-position`)

`background-position`属性决定了背景的具体位置。该属性接受1~2个参数，分别表示横向坐标与纵向坐标。当然也可以使用`top`、`right`、`bottom`、`left`关键字强行指定：

```html
<html>
<head>
    <style>
        div {
            width: 20rem;
            height: 15rem;
            padding: 0.5rem;
            margin-bottom: 1rem;
            border: 1px solid black;

            background-image: url("https://meyerweb.github.io/csstdg4figs/09-colors-backgrounds-and-gradients/i/radio-warn-fade.png");
            background-color: lightgray;
            background-repeat: no-repeat;
        }
    </style>
</head>
<body>
    <div style="background-position: center;">Hello World!</div>
    <div style="background-position: 10% 25%;">Hello World!</div>
    <div style="background-position: right bottom;">Hello World!</div>
    <div style="background-position: right 10% bottom 10%;">Hello World!</div>
</body>
</html>
```

当`background-position`的属性值为百分数时，其规则较为复杂。以横轴为例，横轴的百分比参照的基准是$\text{width}-2\times背景图像的宽度$，操控的是背景图像中点的位置，其变化范围为$[背景图像的宽度, \text{width}-背景图像的宽度]$。纵轴同理。

| 等效的单参数关键字 | 等效的双参数关键字               | 等效的百分数关键字 |
| ------------------ | -------------------------------- | ------------------ |
| `center`           | `center center`                  | `50%`与`50% 50%`   |
| `right`            | `center right`与`right center`   | `100%`与`100% 50%` |
| `left`             | `center left`与`left center`     | `0%`或`0% 50%`     |
| `top`              | `top center`与`center top`       | `50% 0%`           |
| `bottom`           | `bottom center`与`center bottom` | `50% 100%`         |
|                    | `top left`与`left top`（缺省）   | `0% 0%`            |
|                    | `top right`与`right top`         | `100% 0%`          |
|                    | `bottom left`与`left bottom`     | `100% 100%`        |
|                    | `bottom right`与`right bottom`   | `0% 100%`          |

### §6.2.5 改变定位框(`background-origin`)

`background-origin`属性决定背景图像中点活动范围的计算方式。乍一看，该属性与`background-clip`属性的取值非常相近，但是两者承担的职能还是不同的。`background-clip`决定的是背景的绘制区域，而`background-origin`决定的是背景相对位置的参考基准位置。

| `background-origin`属性值 | 作用                                 |
| ------------------------- | ------------------------------------ |
| `border-box`              | 背景图像左上角默认在边框区的左上角   |
| `padding-box`             | 背景图像左上角默认在内边距区的左上角 |
| `content-box`             | 背景图像左上角默认在内容区的左上角   |

```html
<html>
<head>
    <style>
        div {
            width: 20rem;
            height: 20rem;
            padding: 1rem;
            margin-bottom: 1rem;
            border: 20px dotted black;

            background-image: url("https://meyerweb.github.io/csstdg4figs/09-colors-backgrounds-and-gradients/i/yinyang.png");
            background-color: lightgray;
            background-repeat: no-repeat;
        }
    </style>
</head>
<body>
    <div style="background-origin: border-box;">Hello World</div>
    <div style="background-origin: padding-box;">Hello World</div>
    <div style="background-origin: content-box;">Hello World</div>
</body>
</html>
```

现在考虑`background-clip`与`background-origin`组合作用的情景：

```html
.style1 {
    background-clip: content-box;
    background-origin: padding-box;
}
.style2 {
    background-clip: padding-box;
    background-origin: content-box;
}
<div class="style1">Hello World</div>
<div class="style2">Hello World</div>
```

在上面的例子中，`.style1`将背景图片的左上角移动到内边距区的左上角，然后只允许内容区内显示背景图片，从而造成背景图像被裁剪；`.style2`将背景图片的左上角移动到内容区的左上角，然后只允许内边距区内显示背景图像，这一步不会产生任何影响。

### §6.2.6 背景重复方式(`background-repeat`)

`background-repeat`属性决定了背景图片的重复方式。其属性值的正则表达式可以表示为`[repeat-x | repeat-y] | [repeat | space | round | no-repeat]{1,2}`。

| `background-repeat`属性值 | 作用                                                         |
| ------------------------- | ------------------------------------------------------------ |
| `repeat-x`                | 沿横轴无限平铺图像                                           |
| `repeat-y`                | 沿纵轴无限平铺图像                                           |
| `repeat`                  | 沿横轴和纵轴无限平铺图像                                     |
| `space`                   | 在横轴和纵轴重复$\lfloor\displaystyle\frac{元素该边长度}{背景图像该边长度}\rfloor$次，并自动调整间距。如果该值小于$1$，则向上取整到$1$，使其在该轴方向上至少出现一次 |
| `round`                   | 在横轴和纵轴重复$\text{round}(\displaystyle\frac{元素该边长度}{背景图像该边长度})$次，并自动调整背景图像的尺寸 |
| `no-repeat`               | 不重复背景图                                                 |

| 等效单个关键词 | 等效两个关键词        |
| -------------- | --------------------- |
| `repeat-x`     | `repeat no-repeat`    |
| `repeat-y`     | `no-repeat repeat`    |
| `repeat`       | `repeat repeat`       |
| `no-repeat`    | `no-repeat no-repeat` |
| `space`        | `space space`         |
| `round`        | `round round`         |

```html
<html>
<head>
    <style>
        h1 {
            border: 2px solid black;
            padding: 0.5rem;
            margin-bottom: 0.5rem;
            background-image: url("https://meyerweb.github.io/csstdg4figs/09-colors-backgrounds-and-gradients/i/yinyang-sm.png");
            background-color: lightgray;
        }
    </style>
</head>
<body>
    <h1 style="background-repeat: repeat-x;">background-repeat: repeat-x</h1>
    <h1 style="background-repeat: repeat-y;">background-repeat: repeat-y</h1>
    <h1 style="background-repeat: repeat;">background-repeat: repeat</h1>
    <h1 style="background-repeat: no-repeat;">background-repeat: no-repeat</h1>
    <h1 style="background-repeat: space;">background-repeat: space</h1>
    <h1 style="background-repeat: round;">background-repeat: round</h1>
</body>
</html>
```

> 习题：给一个矩形元素设置波浪线边框。
>
> 在[§5.7.5 图像边框(`border-image`)](###§5.7.5 图像边框(`border-image`))一节中，我们学过可以使用预先设计好的波浪环素材绘制波浪线边框。经过本节学习，我们可以使用`background-repeat: repeat-x`在上下两边界绘制波浪线。
>
> ```html
> <html>
> <head>
>     <style>
>         h1 {
>             background-image: url("https://meyerweb.github.io/csstdg4figs/09-colors-backgrounds-and-gradients/i/wavybord.gif");
>             background-repeat: repeat-x;
>             background-color: lightgray;
>         }
>     </style>
> </head>
> <body>
>     <h1>这是一个标题</h1>
> </body>
> </html>
> ```

### §6.2.7 背景粘附(`background-attachment`)

之前我们学习的背景都是粘附与含有`background`相关属性的元素上的。这意味这背景图像的位置会跟随者元素位置而变化，也就是说文档视图滚动时，背景图像会随之一起滚动。

`background-attachment`属性用于调整背景粘附的行为。

| `background-attachment` | 作用                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `scroll`(缺省)          | 当该元素本身有滚动条时，背景图像只受本身滚动条的影响，不受外界滚动条的影响 |
| `fixed`                 | 把背景图像固定在视区中，不受滚动的影响。此时背景图像对齐所参考的原点是**视区的左上角** |
| `local`                 | 把背景图像固定在元素中，受滚动的影响                         |

`background-attachment: fixed`很特殊的一点在于背景图像对齐所参考的原点是**视区的左上角**。这就允许我们实现某些背景图像素材的完美对齐。例如在下面的示例中，白底黑框的`<h1>`长框灰色网格与背景白色网格都是从视图的左上角开始排列的，因此对齐得十分整齐。而且从视觉效果上，就好像是背景是白色网格，保持不动，而`<h1>`是透明的灰色框体。以下两种方式实现的视觉效果完全相同，均如上所述：

1. 使用`background-attachment: fixed;`修饰`<h1>`

   ```html
   <html>
   <head>
       <style>
           body {
               /* 灰色背景格子图像 */
               background-image: url("https://meyerweb.github.io/csstdg4figs/09-colors-backgrounds-and-gradients/i/grid1.gif");
               background-repeat: repeat;
               background-attachment: fixed;
           }
           h1 {
               /* 白色背景格子图像 */
               background-image: url("https://meyerweb.github.io/csstdg4figs/09-colors-backgrounds-and-gradients/i/grid2.gif");
               background-repeat: repeat;
               background-attachment: fixed;
               margin: 3rem;
               border: 5px solid ;
           }
       </style>
   </head>
   <body>
       <span style="font-size: xx-large; font-weight: bold;">&lt;body&gt; background-attachment: fixed;</span>
       <h1 style="background-attachment: fixed;">&lt;h1&gt; background-attachment: fixed</h1>
       <h1 style="background-attachment: fixed;">&lt;h1&gt; background-attachment: fixed</h1>
       <h1 style="background-attachment: fixed;">&lt;h1&gt; background-attachment: fixed</h1>
       <h1 style="background-attachment: fixed;">&lt;h1&gt; background-attachment: fixed</h1>
   </body>
   </html>
   ```

2. 使用`background-color: rgba(0,0,0,0.2);`修饰`<h1>`

   ```html
   <html>
   <head>
       <style>
           body {
               /* 白色背景格子图像 */
               background-image: url("https://meyerweb.github.io/csstdg4figs/09-colors-backgrounds-and-gradients/i/grid2.gif");
               background-repeat: repeat;
               background-attachment: fixed;
           }
           h1 {
               /* 灰色透明背景 */
               background-color: rgba(0, 0, 0, 0.2);
               margin: 3rem;
               border: 5px solid ;
           }
       </style>
   </head>
   <body>
       <span style="font-size: xx-large; font-weight: bold;">&lt;body&gt; background-attachment: fixed;</span>
       <h1 >&lt;h1&gt; background-color: rgba(0,0,0,0.2)</h1>
       <h1 >&lt;h1&gt; background-color: rgba(0,0,0,0.2)</h1>
       <h1 >&lt;h1&gt; background-color: rgba(0,0,0,0.2)</h1>
       <h1 >&lt;h1&gt; background-color: rgba(0,0,0,0.2)</h1>
   </body>
   </html>
   ```

### §6.2.8 背景尺寸(`background-size`)

`background-size`属性用于控制背景的尺寸。其属性值的正则表达式为`[[<length>|<percentage>|auto]{1,2}|cover|contain]`。如果有两个关键字，则前一个是横向尺寸，后一个是纵向尺寸。

| `background-size`属性值 | 作用                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `<length>`              |                                                              |
| `<percentage>`          | 参照基准为背景定位区的尺寸，由`background-origin`决定，而非`background-clip` |
| `auto`(缺省)            |                                                              |
| `cover`                 | 将图像等比例缩放，使得背景图片能包含整个元素（当然背景图片的可视区域只有元素，会进行裁剪） |
| `contain`               | 讲图像等比例缩放，使得整个元素恰好能包含背景图片             |

`auto`关键词的规则较为复杂：

1. 给定一个具体的背景图像，如果一个轴的尺寸为`auto`，另一个轴的尺寸为具体值，则`auto`为保留背景图像宽高比计算得到的数值。
2. 如果第一步计算失败，则`auto`被解析为背景图片的固有绝对尺寸。
3. 如果第二步失败了(例如矢量图没有宽高比信息)，则`auto`被解析为`100%`。

```html
<html>
<head>
    <style>
        div {
            width: 400px;
            height: 150px;
            border: 2px solid black;
            margin-bottom: 0.5rem;
            background-image: url("https://meyerweb.github.io/csstdg4figs/09-colors-backgrounds-and-gradients/i/yinyang-200.png");
            background-repeat: no-repeat;
            background-position: center;
        }
    </style>
</head>
<body>
    <div style="background-size: 100px 100px;">Hello, World!</div>
    <div style="background-size: 100px 200px;">Hello, World!</div>
    <div style="background-size: 200px 100px;">Hello, World!</div>
</body>
</html>
```

### §6.2.9 背景(综合属性)(`background`)

`background`属性本质上是对所有与背景相关属性的整合。例如下面的两种写法产生的效果完全一致：

```css
body {
    background-color: white;
    background-image: url();
    background-position: top left;
    background-repeat: repeat-y;
    background-attachment: fixed;
    background-origin: padding-box;
    background-clip: border-box;
    background-size: 50% 50%;
}
body {
    background-color: white url() repeat-y top left / 50% 50% fixed padding-box border-box;
}
```

`background`对属性值的顺序有以下限制：

1. `background-size`必须跟在`background-position`后面，而且二者之间必须用正斜杠`/`隔开。
2. 横向值在前，纵向值在后。
3. 我们知道`background-origin`和`background-clip`使用的关键词很相近。如果`background`属性值出现了两次类似的关键词，则最先出现的记为`background-origin`，后出现的记为`background-clip`。

### §6.2.10 多个背景

到目前为止，除了`background-color`之外，我们学过的所有背景属性，都支持设置多个值，每个值之间用个逗号隔开：

```html
<html>
<head>
    <style>
        div {
            width: 40rem;
            height: 15rem;
            background-image: url("https://meyerweb.github.io/csstdg4figs/09-colors-backgrounds-and-gradients/i/bg01.png"),
                url("https://meyerweb.github.io/csstdg4figs/09-colors-backgrounds-and-gradients/i/bg02.gif"),
                url("https://meyerweb.github.io/csstdg4figs/09-colors-backgrounds-and-gradients/i/bg03.jpg");
            background-position: top right, left center, 50% 100%;
            background-repeat: no-repeat, no-repeat, repeat-x;
            text-align: center;
        }
    </style>
</head>
<body>
    <div>多个背景</div>
</body>
</html>
```

> 注意：绘制的多个背景中，先声明的处于较上的图层。

假如`background-image`设置了7个值，而`background-repeat`只设置了3个值，两者不能一一对应，那么CSS将会自动按顺序重复这3个值，直到重复出7个值为止。

## §6.3 渐变

渐变指的是一种颜色到另一种颜色的平滑过渡。

CSS支持两种类型的渐变：线性渐变和径向渐变。每种渐变又可细分为循环渐变和不循环渐变。

### §6.3.1 线性渐变(`linear-gradient`)

线性渐变的基本语法为：`[[角度|to 哪一侧|to 哪个四分之一角],]? [色标 [,中色点]?]#, 中色点`。其中`#`表示前面的值可以重复一次及以上，但是每次重复之间都要加逗号`,`分隔。

```html
<html>
<head>
    <style>
        div {
            border: 2px solid black;
            width: 20rem;
            height: 5rem;
            margin-bottom: 0.5rem;
        }
    </style>
</head>
<body>
    <div style="background-image: linear-gradient(purple, gold);"></div>
    <div style="background-image: linear-gradient(90deg, purple, gold);"></div>
    <div style="background-image: linear-gradient(to left, purple, gold);"></div>
    <div style="background-image: linear-gradient(to bottom left, purple, gold);"></div>
    <div style="background-image: linear-gradient(to bottom left, purple, gold, navy);"></div>
</body>
</html>
```

中色点的定义为：`颜色 [绝对长度|百分比]?`。前面的`颜色`表示了该点的颜色，后面的表示了该点相对起始位置的距离（注意，不是相对上一个中色点的距离）。

```html
<div style="background-image: linear-gradient(purple 10%, gold 50%);"></div>
<div style="background-image: linear-gradient(purple 1rem, gold 6rem);"></div>
```

如果带有距离信息的和没有距离信息的中色点混用，那么如何确定没有距离信息的中色点的位置呢？答案是：选取两侧邻近的有距离信息的中色点，让没有距离信息的中色点在其中均匀分布。

```html
<div style="background-image: linear-gradient(purple 10%, green, blue, gold 90%);"></div>
```

如果两个中色点的位置发生重合，那么颜色在这一点会发生突变。与高等数学中的左极限和右极限相类比，虽然位置重合，但声明顺序一定有先后之分。首先声明的颜色就是左极限，后声明的就是右极限，而左极限不一定等于右极限。

这种方法经常用于实现颜色相间的"条纹"：

```html
<html>
<head>
    <style>
        div {
            border: 2px solid black;
            width: 20rem;
            height: 5rem;
            margin-bottom: 0.5rem;

            background-image: linear-gradient(90deg,
                gray 0%, gray 25%,
                transparent 25%, transparent 50%, 
                gray 50%, gray 75%,
                transparent 75%, transparent
            );
        }
    </style>
</head>
<body>
    <div></div>
</body>
</html>
```

到此，我们都默认中色点的位置随着声明的顺序而增大。如果一个中色点的位置很靠前，但是声明顺序很落后，那么这个中色点的声明顺序是不正常的。CSS对此的处理方法是：令该中色点的位置与前一个声明的中色点位置重合，使该中色点的作用位置靠后。

```html
<html>
<head>
    <style>
        div {
            border: 2px solid black;
            width: 20rem;
            height: 5rem;
            margin-bottom: 0.5rem;

            background-image: linear-gradient(90deg,
                red 10%, blue 90%, green 50%
            ); /* 实际上green的位置在90% */
        }
    </style>
</head>
<body>
    <div></div>
</body>
</html>
```

在讲解`角度`之前，首先要明确梯度线的概念。在二维平面内确定一条直线需要两个变量，下面给定确定该梯度线的两个条件：

1. 当角度为$x$`deg`时，渐变的方向向量的极坐标角度$\theta=90\degree-x$

2. 沿渐变方向向量最贴近的一条对角线，这条对角线对应矩形上的两个端点。这两点到梯度线的距离应该相等。过这两点向梯度线做垂线，得到的两个垂足分别记为起点和终点。

   该约束条件等价于“梯度线必须通过矩形中心”。

![梯度线示例](https://developer.mozilla.org/en-US/docs/Web/CSS/gradient/linear-gradient/linear-gradient.png)

以下是带有`角度`的`linear-gradient`颜色计算方式：

1. 如果点$(x,y)$恰好在梯度线上，则令起点和中点分别对应`linear-gradient`中色点的起点和终点，对颜色进行线性插值。
2. 如果点$(x,y)$不在梯度线上，则过改点向梯度线做垂足，令该点的颜色为垂足的颜色。

带有`to 哪个四分之一角`的行为比较特殊。例如`to top right`，你可能猜测该梯度线指向的方向恰为$45\degree$。然而实际是，CSS会过矩形中心，做一条主对角线的垂线，令其为梯度线。

```html
<html>
<head>
    <style>
        div {
            border: 2px solid black;
            margin-bottom: 0.5rem;
            background-image: linear-gradient(to top right, red, blue);
        }
    </style>
</head>
<body>
    <div style="width: 10rem; height: 2rem;"></div>
    <div style="width: 10rem; height: 5rem;"></div>
    <div style="width: 10rem; height: 10rem;"></div>
    <div style="width: 5rem; height: 10rem;"></div>
    <div style="width: 2rem; height: 10rem;"></div>
</body>
</html>
```

### §6.3.2 径向渐变(`radial-gradient`)

径向渐变的语法为：

```
radial-gradient(
	[[<shape>||<size>] [at <position>]?, | at <position>,]?
	[<color-stop> [, <color-hint>]?] [,<color-stop>]+
)
```

其中：

- `[x||y]`表示可以取其中的一个值或多个值，而且顺序任意。
- `+`表示重复一次或多次

对于径向渐变而言，梯度线的方向不再是一个定值，而是一个包含约束的极坐标点$(r,\theta)$的区域集合。这里我们令左上角、右上角、右下角、左下角对应的角度组成一个集合，即$\Theta=\{\theta_1,\theta_2,\theta_3,\theta_4\}$。

`<shape>`指明径向渐变的形状，只有两种取值：`circle`圆和`ellipse`椭圆

`<size>`指明径向渐变的尺寸，可以接受1~2个尺寸值，描述为`50px`或`50px 100px`；也可以接受以下四种关键词：

| `<shape>`关键词         | 圆形时的作用                                                 | 椭圆时的作用                                                 |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `closest-side`          | 梯度线的末端为$(\underset{r,\theta\in[0,2\pi)}{\text{argmin}(\theta)},r_{min})$ | 梯度线横轴的末端为$(\underset{r,\theta\in\{90\degree,270\degree\}}{\text{argmin}(\theta)},r_{min})$，纵轴的末端为梯度线的末端为$(\underset{r,\theta\in\{0\degree,180\degree\}}{\text{argmin}(\theta)},r_{min})$ |
| `farthest-side`         | 梯度线的末端为$(\underset{r,\theta\in[0,2\pi)}{\text{argmax}(\theta)},r_{max})$ | 梯度线横轴的末端为$(\underset{r,\theta\in\{90\degree,270\degree\}}{\text{argmax}(\theta)},r_{max})$，纵轴的末端为梯度线的末端为$(\underset{r,\theta\in\{0\degree,180\degree\}}{\text{argmax}(\theta)},r_{max})$ |
| `closest-corner`        | 梯度线的末端为$(\underset{r,\theta\in\Theta}{\text{argmin}(\theta)},r_{min})$ | 与圆形时相同，渐变宽高比与`closest-side`一样                 |
| `farthest-corner`(缺省) | 梯度线的末端为$(\underset{r,\theta\in\Theta}{\text{argmax}(\theta)},r_{max})$ | 与圆形时相同，渐变宽高比与`farthest-side`一样                |

```html
<html>
<head>
    <style>
        div {
            border: 2px solid black;
            margin-bottom: 0.5rem;
            width: 10rem; 
            height: 5rem;
            color: white;
        }
    </style>
</head>
<body>
    <div style="background-image: radial-gradient(closest-side at 33% 66%, yellow, purple);">closest-side</div>
    <div style="background-image: radial-gradient(farthest-side at 33% 66%, yellow, purple);">farthest-side</div>
    <div style="background-image: radial-gradient(closest-corner at 33% 66%, yellow, purple);">closest-corner</div>
    <div style="background-image: radial-gradient(farthest-corner at 33% 66%, yellow, purple);">farthest-corner</div>
</body>
</html>
```

| `<shape>`取值\\`<size>`数值 | 无   | 单属性值     | 多属性值   |
| --------------------------- | ---- | ------------ | ---------- |
| 无                          |      | 圆形         | 圆形或椭圆 |
| `circle`                    | 圆形 | 不能填百分比 |            |
| `ellipse`                   | 椭圆 |              |            |

`at <position>`用于定位径向渐变，可以把径向渐变的中心放在默认中央以外的位置，属性值与`background-position`完全一致，其中第一个参数如果是长度的话，表示渐变中心距离左边界的距离；第二个参数同理，表示距离上边界的距离：

```html
<html>
<head>
    <style>
        div {
            border: 2px solid black;
            margin-bottom: 0.5rem;
            width: 10rem; 
            height: 5rem;
            color: white;
        }
    </style>
</head>
<body>
    <div style="background-image: radial-gradient(at bottom left, yellow, purple);">at bottom left</div>
    <div style="background-image: radial-gradient(at center right, yellow, purple);">at center right</div>
    <div style="background-image: radial-gradient(at top center, yellow, purple);">at top center</div>
    <div style="background-image: radial-gradient(at center left, yellow, purple);">at center left</div>
</body>
</html>
```

`<color-stop>`中色点的取值与线性渐变的规则完全一致：

1. 首个色标的位置默认为`0%`，最后一个色标的位置默认为`100%`。例如`green, red`等价于`green 0%, red 100%`。
2. 如果某个中色点为显示声明位置，则其位置为最临近的首尾两个位置确定的中色点的线性插值。例如`red, green, blue`等价于`red, green 50%, blue`。

考虑`<size>`为`0px`时的极端情况。此时CSS会将`0px`视为一个非常小的值，类似于数学上的$\displaystyle\lim_{\epsilon\rightarrow0}{\epsilon}$。在Chrome上，这一点$\epsilon$是不可见的。

### §6.3.3 循环线性渐变(`repeating-linear-gradient`)

在之前的渐变中，首尾两个色标决定了颜色渐变的范围，超出这个范围都是纯色。而循环渐变允许色标和中色点也不断重复排列。

> 注意：循环渐变要求所有色标和中色点的位置均为**绝对长度**，而不能是**相对长度（例如百分数）**，否则循环渐变的行为将退化为非循环渐变，在整个背景显示区域内只重复一次。
>
> ```html
> <html>
> <head>
>     <style>  
>         div {
>             width: 20rem;
>             height: 40px;
>             border: 2px solid black;
>             color: white;
>             font-size: large;
>             font-weight: bold;
>             margin-bottom: 1rem;
>         }
>     </style>
> </head>
> <body>
>     <div style="background: repeating-linear-gradient(-45deg, black 0, black 25px,  25px, darkgoldenrod 50px) top left repeat;">这才应该是警戒线的样式</div>
>     <div style="background: repeating-linear-gradient(-45deg, black 0, black 50%,  50%, darkgoldenrod 100%) top left repeat;">这条警戒线只循环了一次，不正常</div>
> </body>
> </html>
> ```

> 注意：之前的非循环渐变默认首尾色标的位置分别为`0%`和`100%`，但是循环渐变要求末尾色标的位置必须显式声明，用以确定一个循环的长度。

当首尾色标的颜色不一样时，会出现“急停”现象，颜色在两个循环的交界处发生突变。这对于平滑渐变是不利的，但是有时我们可以故意利用这种现象绘制图案，例如下面的警戒线示例：

```html
<html>
<head>
    <style>  
        div {
            width: 20rem;
            height: 40px;
            border: 2px solid black;
            color: white;
            font-size: large;
            font-weight: bold;
            margin-bottom: 1rem;
        }
    </style>
</head>
<body>
    <div style="background: linear-gradient(-45deg, black 0, black 25px,  25px, darkgoldenrod 50px) top left/40px 40px repeat;">这条警戒线不正常</div>
    <div style="background: repeating-linear-gradient(-45deg, black 0, black 25px,  25px, darkgoldenrod 50px) top left repeat;">这才应该是警戒线的样式</div>
</body>
</html>
```

### §6.3.4 循环径向渐变(`repeating-radial-gradient`)

循环径向渐变`repeating-radial-gradient`的语法与径向渐变基本一致。

```html
<html>
<head>
    <style>  
        div {
            width: 20rem;
            height: 10rem;
            border: 2px solid black;
            color: black;
            font-size: large;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div style="background: repeating-radial-gradient(100px 50px, lightblue 20px, skyblue 50px, lightblue 80px);">这是循环镜像渐变</div>
</body>
</html>
```

> 注意：虽然循环线性渐变要求所有色标和中色点的位置不能为百分数，但是在循环径向渐变中却可以。

> 注意：如果要让循环径向渐变真正地循环起来，就必须显式声明循环径向渐变的尺寸。这是因为循环渐变尺寸的缺省值为`farthest-corner`。如果渐变中心恰好在容纳块中心，那么它等价于`50% 50%`。这会导致一次循环就会充满整个容纳块，导致渐变只会循环一次。

### §6.3.5 渐变设计技巧

渐变的本质就是图像，这意味着我们可以像图像那样，控制渐变、尺寸和控制方式。以下示例使用渐变创建了图像，作为`background`属性的`<image>`，作为背景图像进行平铺：

```html
<html>
<head>
    <style>
        div {
            border: 2px solid black;
            width: 10rem; 
            color: white;
            background: darkcyan center / 25px 25px repeat radial-gradient(
                circle at center, rgba(0,0,0,0.2), rgba(0,0,0,0.2) 10px, transparent 10px 
            );
        }
    </style>
</head>
<body>
    <div>使用渐变创建图像，作为background属性的&lt;image&gt;，地位等价于url(...)</div>
</body>
</html>
```

利用多个渐变绘制多个图像，应用到多个背景中，可以实现很惊艳的效果。以下示例使用了多个深红-浅红-深红的渐变，绘制了舞台的幕布：

```html
<html>
<head>
    <style>  
        div {
            border: 2px solid black;
            width: 80%;
            height: 10rem;
            margin: 0.5rem auto 0.5rem auto;
            color: #eeeeee;
            text-shadow: 1px 1px 2px black;
            font-size: larger;
            text-align: center;
            padding-top: 1rem;
        }
        div.layer1 {
            background: linear-gradient(0deg, rgba(255, 128, 128, 0.25), transparent 75%);
        }
        div.layer2 {
            background: linear-gradient(89deg, transparent, transparent 30%, #510A0E 35%, #510A0E 40%, #61100F 43%, #B93F3A 50%,
                    #4B0408 55%, #6A0F18 60%, #651015 65%, #510A0E 70%,
                    #510A0E 75%, rgba(255, 128, 128, 0) 80%, transparent);
        }
        div.layer3 {
            background: linear-gradient(92deg,
                    #510A0E, #510A0E 20%, #61100F 25%, #B93F3A 40%, #4B0408 50%,
                    #6A0F18 70%, #651015 80%, #510A0E 90%, #510A0E);
        }
        div.final {
            background:
                linear-gradient(0deg, rgba(255, 128, 128, 0.25), transparent 75%),
                linear-gradient(89deg, transparent, transparent 30%, #510A0E 35%, #510A0E 40%, #61100F 43%, #B93F3A 50%,
                    #4B0408 55%, #6A0F18 60%, #651015 65%, #510A0E 70%,
                    #510A0E 75%, rgba(255, 128, 128, 0) 80%, transparent),
                linear-gradient(92deg,
                    #510A0E, #510A0E 20%, #61100F 25%, #B93F3A 40%, #4B0408 50%,
                    #6A0F18 70%, #651015 80%, #510A0E 90%, #510A0E);
        }
    </style>
</head>
<body>
    <div class="layer1">第一层<br/>background: linear-gradient(0deg, rgba(255, 128, 128, 0.25), transparent 75%);</div>
    <div class="layer2">第二层<br/>background: linear-gradient(89deg, transparent, transparent 30%, #510A0E 35%, #510A0E 40%, #61100F 43%, #B93F3A 50%,
        #4B0408 55%, #6A0F18 60%, #651015 65%, #510A0E 70%,
        #510A0E 75%, rgba(255, 128, 128, 0) 80%, transparent);</div>
    <div class="layer3">第三层<br/>、background: linear-gradient(92deg,
        #510A0E, #510A0E 20%, #61100F 25%, #B93F3A 40%, #4B0408 50%,
        #6A0F18 70%, #651015 80%, #510A0E 90%, #510A0E);</div>
    <div class="final">三层叠加的结果</div>
</body>
</html>
```

## §6.4 盒子投影(`box-shadow`)

`box-shadow`属性用于给元素所在的框体创建投影。其语法与`text-shadow`大致一致。`box-shadow`接受1~4个距离参数与1个颜色参数，其中前两个距离参数代表横轴与纵轴的偏移量，第三个参数表示阴影半径，第四个参数表示阴影扩散距离。但是`box-shadow`支持在属性值开头声明`inset`属性，表示这是个内阴影。

```html
<html>
<head>
    <style>  
        body {
            background-image: repeating-linear-gradient(45deg, #eee, #eee 4px, #ccc 4px, #ccc 8px);
        }
        div {
            width: 12rem;
            height: 6rem;
            border: 2px solid black;
            border-radius: 1rem;
            font-weight: bold;
            margin: 0.5rem;
            padding: 0.5rem;
            background-color: lightblue;
            float: left;
        }
        div:nth-child(1){
            box-shadow: 10px 10px gray;
        }
        div:nth-child(2){
            box-shadow: inset 10px 10px gray;
        }
        div:nth-child(3){
            box-shadow: 0 0 5px 5px rgba(0, 0, 0, 0.5);
        }
        div:nth-child(4){
            box-shadow: inset 0 0 5px 5px rgba(0, 0, 0, 0.5);
        }
    </style>
</head>
<body>
    <div>这是一个box-shadow: 10px 10px gray;的盒子，是外阴影</div>
    <div>这是一个inset 10px 10px gray;的盒子，是内阴影</div>
    <div>这是一个box-shadow: 0 0 5px 5px rgba(0, 0, 0, 0.5);的盒子，是向外扩展的渐变阴影</div>
    <div>这是一个box-shadow: inset 0 0 5px 5px rgba(0, 0, 0, 0.5);的盒子，是向内扩展的渐变阴影</div>
</body>
</html>
```

乍一看，这个投影仿佛就是原框体的复制品，形状上一模一样，只不过被挡住了而已。但实际上并不是这样。做个实验，我们给框体设置透明背景，会发现框体的正下方根本没有盒子投影。这说明盒子投影的形状是不完整的。

```html
<html>
<head>
    <style>  
        body {
            background-image: repeating-linear-gradient(45deg, #eee, #eee 4px, #ccc 4px, #ccc 8px);
        }
        div {
            width: 20rem;
            height: 5rem;
            border: 2px solid black;
            font-size: large;
            font-weight: bold;
            
            background-color: rgba(255, 0, 0, 0.3);
            box-shadow: 10px 10px gray;
        }
    </style>
</head>
<body>
    <div>这是一个有投影的盒子</div>
</body>
</html>
```

与`text-shadow`一样，`box-shadow`也可以应用多个投影，用逗号`,`隔开即可。

# §7 浮动

“浮动”指的是元素的对齐方向。CSS标准规定：浮动通过`float`属性实现。有趣的是，在浮动的标准确定以前，就已经有了浮动的实现了。例如`<img align="right"/>`：

```html
<html>
<head>
    <style>
        img {
            background-color: gray;
            border: 1px solid;
        }
    </style>
</head>
<body>
    <img src="https://www.google.com/images/branding/googlelogo/1x/googlelogo_light_color_272x92dp.png" align="right"/>
</body>
</html>
```

在浏览器的审查元素中打开，会看到`<img>`中的`align="right"`被转换成了现代的`float: right`CSS属性。

| `float`属性值 | 作用           |
| ------------- | -------------- |
| `left`        | 元素浮动到左侧 |
| `right`       | 元素浮动到右侧 |
| `none`(缺省)  |                |

## §7.1 浮动的影响

1. 浮动的元素脱离了常规的文档流。元素浮动后，原来所占的位置空了出来，新位置参与实际的占位。

   ```html
   <html>
   <head>
       <style>
           div {
               border: 1px solid black;
               width: 20rem;
           }
           p {
               margin: 0.5rem;
           }
           p.aside {
               width: 6rem;
               float: right;
               border: 1px solid black;
               background-color: lightgrey;
           }
       </style>
   </head>
   <body>
       <div>
           <p>这是标题</p>
           <p class="aside">我是侧栏说明。我脱离了正常的文本布局，所以外层的&lt;div&gt;不会管我。</p>
           <p>这是正文。右面是一个&lt;p class="aside"&gt;，设置了float:right的CSS属性。</p>
       </div>
   </body>
   </html>
   ```

2. 元素浮动后，无论元素原先是行内框还是块级框，都会变成块级框。也就是说，CSS给浮动的元素自动加上`display: block`属性。

3. 浮动元素的左、右、上边界不能超过容纳块的左、右、上边界，却可以超过下边界。

   > 注意：如果浮动元素太宽，超出了左边界或右边界（却绝育浮动方向），则CSS不得不渲染，

   > 注意：如果给浮动元素设置负外边距，从视觉效果上来说，确实会发生越界。但是从规范上来说，这依然不矛盾。因为越界的判定标准是“子元素的外边界超出了父元素的容纳框”，而负外边距相当于覆盖了自己的外边界，使得外边界向容纳框中移动，因此外边界仍然在容纳框中。

4. 如果前面声明浮动的元素靠左，且后一个浮动元素的顶边**不在**前一个浮动元素底边的下边，那么后面元素的左边界必须在前面元素的右边界的右侧。同理，如果前面声明浮动的元素靠右，且同样后一个浮动元素**不在**下边，则后面元素的右边界必须在前面元素左边界的左侧。这条规则能避免浮动元素相互覆盖。

   > 注意：前一个浮动元素下边界在后一个浮动元素的上边界，有可能是因为两个浮动元素间相隔了若干个非浮动元素，也有可能是两个浮动元素的宽度之和超过了容纳块的宽度，使得CSS不得不换行。

   ```html
   <html>
   <head>
       <style>
           div {
               border: 1px solid black;
               width: 20rem;
           }
           p {
               margin: 0.5rem;
           }
           p.aside {
               float: left;
               border: 1px solid black;
               background-color: lightgrey;
           }
           p.rightFloat {
               width: 3rem;
               float: left;
               border: 1px solid black;
               background-color: lightgrey;  
           }
       </style>
   </head>
   <body>
       <div>
           <p>这是标题</p>
           <p class="aside" style="width: 6rem;">我是靠左的侧栏说明。</p>
           <p class="aside" style="width: 6rem;">我是另一个靠左的侧栏说明。 </p>
           <p>这是正文。本页面设置了两个带有float属性的元素，都是靠左浮动。</p>
       </div>
   </body>
   </html>
   ```

5. 浮动元素的顶边不能比前方任何浮动元素或块级元素的顶边高。

6. 在不违反以上规则的前提下，浮动元素必须尽可能的放在高的位置上。

   ```html
   <html>
   <head>
       <style>
           div {
               border: 1px solid black;
               width: 20rem;
           }
           p {
               margin: 0.5rem;
           }
           p.leftFloat {
               float: left;
               border: 1px solid black;
               background-color: lightgrey;
           }
           p.rightFloat {
               float: right;
               border: 1px solid black;
               background-color: lightgrey;  
           }
       </style>
   </head>
   <body>
       <div>
           <p>这是标题</p>
           <p class="leftFloat" style="width: 3rem;">我是靠左的侧栏说明。</p>
           <p class="leftFloat" style="width: 4rem;">我是另一个靠左的侧栏说明。 </p>
           <p class="rightFloat" style="width: 4rem;">我是靠右的侧栏说明，因为横向空间足够，所以没有另起一行。 </p>
           <p>这是正文。本页面设置了三个带有float属性的元素，前两个都是靠左浮动，后一个是靠右浮动。</p>
       </div>
   </body>
   </html>
   ```

7. 在满足以上规则的前提下，左浮动元素应该尽量靠左，右浮动元素应该尽量靠右。

8. 若浮动元素的后代中存在浮动元素，且子元素尺寸较大时，才扩大父元素的尺寸。反之如果是非浮动元素的后代，则不会扩大父元素的尺寸。

   为了防止浮动元素被遮盖，浮动元素的背景永远在非浮动元素之上。

   ```html
   <html>
   <head>
       <style>
           .box {
               border: 1px solid black;
               width: 16rem;
               background-color: lightcoral;
           }
           .float_picture {
               width: 2rem;
               height: 7rem;
               float: right;
               background: url('https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/down-arrow.png') repeat-y, lightgreen;
           }
       </style>
   </head>
   <body>
       <div class="box">
           <p>
               <span class="float_picture"></span>
               这是一个非浮动元素，右面的背景图片是靠右浮动的。
           </p>
       </div>
       <p style="background-color: lightcyan;">这是一段文本，但是被上面浮动元素隔开了，导致上半段间距较小，下半段间距较大。而且浮动元素的背景永远在非浮动元素之上。</p>
       <div class="box" style="float: left">
           <p>
               <span class="float_picture"></span>
               这是一个浮动元素，图片是靠右浮动的。<strong>注意到背景图像撑大了父元素的尺寸。</strong>
           </p>
       </div>
   </body>
   </html>
   ```

9. 当浮动元素与行内元素重叠时，两者位于同一图层渲染。当浮动元素与块级元素重叠时，块级元素的内容覆盖在浮动元素之上，块级元素的背景和边框在浮动元素之下。

## §7.2 清除浮动(`clear`)

有时我们想把文档划分为多个区域，每个区域相互独立。但是`float`的出现使得该规则被打破。在[§7.1 浮动的影响](##§7.1 浮动的影响)第八条规则中，我们能观察到这一异常行为。当时我们是将父元素也设为浮动元素，才解决了问题。实际上，CSS提供了另一种方法，能够确保父元素跟随子浮动元素自动增长，防止影响到其它区域。这就是`clear`属性。

| `clear`属性值 | 作用                           |
| ------------- | ------------------------------ |
| `left`        | 让左浮动元素的右侧不出现该元素 |
| `right`       | 让右浮动元素的左侧不出现该元素 |
| `both`        | 同时启用`left`和`right`        |
| `none`(缺省)  |                                |

下面的例子中，父元素的下面是一个同级`<p>`元素，父元素包含一个子浮动元素`<img>`：

```html
<html>
<head>
    <style>
        .box {
            border: 1px solid black;
            width: 16rem;
            background-color: lightcoral;
        }
        .float_picture {
            width: 2rem;
            height: 7rem;
            float: right;
            background: url('https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/down-arrow.png') repeat-y, lightgreen;
        }
    </style>
</head>
<body>
    <div class="box">
        <p>
            <span class="float_picture"></span>
            这是一个非浮动元素，右面的背景图片是靠右浮动的。
        </p>
    </div>
    <p style="background-color: lightcyan; clear: right;">这是一段文本，启用了clear: right，不会受到上面浮动元素的影响了</p>
</body>
</html>
```

> 注意：`clear`的实现方式发生过改变。CSS2及之前，`clear`的实现方式是增加元素的`margin-top`属性值，这会导致原先摄者的上外边距被覆盖掉。CSS2.1及之后引入了间距(`clearance`)，负责将元素向下移动，从而不再影响上外边距。

还有一个问题：我们看到子浮动元素与外级`<p>`元素是贴在一起的。如果我们要让这两者保持一点间隔，应该给这三者之中的谁添加`margin`属性呢？**答案是子浮动元素**。经过实践可知，另两者都不行。使用审查元素可知，CSS认为两个同级的元素根本没有挨在一起，所以给父元素是无效的；如果给另一个同级元素添加`margin`属性，就会发现它的外边距对浮动元素没有任何作用。

## §7.3 浮动形状

CSS3增加了新模块CSS Shapes，使得浮动框的形状可以不再局限于矩形。

### §7.3.1 定义形状(`shape-outside`)

`shape-outside`属性用于定义内容流动的形状。该属性计算图像中的透明部分，使得内容流入与其直接接触的那一侧的透明部分。也就是说，内容要么流向浮动元素的左侧，要么是右侧，不可能同时是左右侧。

| `shape-outside`属性值          | 作用 |
| ------------------------------ | ---- |
| `none`(缺省)                   |      |
| `<basic-shape> || <shape-box>` |      |
| `<image>`                      |      |

对于`<image>`，CSS将提取图片资源（带有Alpha通道的PNG、GIF）中的透明部分。

```html
<html>
<head>
    <style>
        .root {
            border: 1px solid black;
            width: 11rem;
            margin-bottom: 0.5rem;
        }
        img {
            float: left;
            width: 5rem;
        }
        img.float {
            shape-outside: url("https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/moon.png");
        }
    </style>
</head>
<body>
    <div class="root">
        <img class="float" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/moon.png"/>
        这是一个包含向左浮动图片的div。这段文字被图片的形状隔开了。
    </div>
    <div class="root">
        <img src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/moon.png"/>
        这是一个包含向左浮动图片的div。图像被当成一个矩形，而不是弯的。
    </div>
</body>
</html>
```

`<basic-shape>`可以是下面类型中的某一个：

- `inset()`([MDN文档](https://developer.mozilla.org/en-US/docs/Web/CSS/basic-shape/inset))：接受1~4个参数与质心位置，作为与容纳框的上右下左的边距绘制矩形
- `circle()`([MDN文档](https://developer.mozilla.org/en-US/docs/Web/CSS/basic-shape/circle))：接受半径参数与圆心位置，绘制圆形
- `ellipse()`([MDN文档](https://developer.mozilla.org/en-US/docs/Web/CSS/basic-shape/ellipse))：接受2个参数或四个关键词与圆心位置，绘制椭圆
- `polygen()`([MDN文档](https://developer.mozilla.org/en-US/docs/Web/CSS/basic-shape/polygon))：接受若干个端点位置，绘制多边形

```html
<html>
<head>
    <style>
        .root {
            border: 1px solid black;
            width: 18rem;
            margin-bottom: 1rem;
        }
        img {
            float: left;
            width: 2rem;
            background-color: lightblue;
            border: 1rem solid lightskyblue;
            padding: 1rem;
            margin: 1rem;
        }
        p {
            margin-top: 2rem;
            margin-bottom: 3rem;
        }
    </style>
</head>
<body>
    <div class="root">
        <img style="shape-outside: inset(30px);" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/chrome.jpg"/>
        <p>矩形：shape-outside: inset(30px);</p>
    </div>
    <div class="root">
        <img style="shape-outside: circle(15px);" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/chrome.jpg"/>
        <p>圆形：shape-outsise: border-box</p>
    </div>
    <div class="root">
        <img style="shape-outside: ellipse(50px 20px);" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/chrome.jpg"/>
        <p>竖椭圆：shape-outside: ellipse(50px 20px);</p>
    </div>
    <div class="root">
        <img style="shape-outside: polygon(50% 0%, 100% 50%, 50% 100%, 0% 50%);" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/chrome.jpg"/>
        <p>扁棱形：shape-outside: polygon(50% 0%, 100% 50%, 50% 100%, 0% 50%);</p>
    </div>
</body>
</html>
```

`<shape-box>`可以是下面类型中的某一个：

- `margin-box`：以外边距区的边界作为流动流入的区域
- `border-box`：以边框区的边界作为流动流入的区域
- `padding-box`：以内边距区边界作为流动流入的区域
- `content-box`：以内容区的边界作为流动流入的区域

```html
<html>
<head>
    <style>
        .root {
            border: 1px solid black;
            width: 15rem;
            margin-bottom: 0.5rem;
        }
        img {
            float: left;
            width: 2rem;
            background-color: lightblue;
            border: 1rem solid lightskyblue;
            padding: 1rem;
            margin: 1rem;
        }
        p {
            margin-top: 3rem;
            margin-bottom: 3rem;
        }
    </style>
</head>
<body>
    <div class="root">
        <img style="shape-outside: margin-box;" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/chrome.jpg"/>
        <p>shape-outsise: margin-box</p>
    </div>
    <div class="root">
        <img style="shape-outside: border-box;" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/chrome.jpg"/>
        <p>shape-outsise: border-box</p>
    </div>
    <div class="root">
        <img style="shape-outside: padding-box;" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/chrome.jpg"/>
        <p>shape-outsise: padding-box</p>
    </div>
    <div class="root">
        <img style="shape-outside: content-box;" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/chrome.jpg"/>
        <p>shape-outsise: content-box</p>
    </div>
</body>
</html>
```

### §7.3.2 形状透明阈值(`shape-image-threshold`)

前面我们提到过，图像的透明部分允许内容流入。至于像素点要怎么才算透明，Alpha值要怎么高才算符合要求，可以使用`shape-image-threshold`属性设置阈值。

```html
<html>
<head>
    <style>
        .root {
            border: 1px solid black;
            width: 18rem;
            margin-bottom: 1rem;
        }
        img {
            float: left;
            width: 5rem;
            background-color: lightblue;
            shape-outside: url("https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/sit-gradient.png");
        }
        p {
            margin-top: 0.5rem;
            margin-bottom: 0.5rem;
        }
    </style>
</head>
<body>
    <div class="root">
        <img style="shape-image-threshold: 0.1;" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/sit-gradient.png"/>
        <p>这是一段文本，shape-image-threshold: 0.1;这是一段文本，shape-image-threshold: 0.1;</p>
    </div>
    <div class="root">
        <img style="shape-image-threshold: 0.5.;" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/sit-gradient.png"/>
        <p>这是一段文本，shape-image-threshold: 0.5;这是一段文本，shape-image-threshold: 0.5;</p>
    </div>
    <div class="root">
        <img style="shape-image-threshold: 0.9;" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/sit-gradient.png"/>
        <p>这是一段文本，shape-image-threshold: 0.9;这是一段文本，shape-image-threshold: 0.9;</p>
    </div>
</body>
</html>
```

### §7.3.3 形状外边距(`shape-margin`)

`shape-margin`属性将形状沿各个边的法向方向向外或向内移动，也就是为形状添加"外边距"。

```html
<html>
<head>
    <style>
        .root {
            border: 1px solid black;
            width: 15rem;
            margin-bottom: 1rem;
        }
        img {
            float: left;
            width: 10rem;
            shape-outside: url("https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/star.svg");
        }
        p {
            margin-top: 0.5rem;
            margin-bottom: 0.5rem;
        }
    </style>
</head>
<body>
    <div class="root">
        <img src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/star.svg"/>
        <p>这个元素中包含了一个向左浮动的图片和一个p标签，其中图片没有设置了shape-margin</p>
    </div>
    <div class="root">
        <img style="shape-margin: 1.4rem;" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/star.svg"/>
        <p>这个元素中包含了一个向左浮动的图片和一个p标签，其中图片设置了shape-margin: 1.4rem;</p>
    </div>
    <div class="root">
        <img style="shape-margin: 2rem;" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/star.svg"/>
        <p>这个元素中包含了一个向左浮动的图片和一个p标签，其中图片设置了shape-margin: 2rem;。超出容纳块的形状会被裁剪。</p>
    </div>
</body>
</html>
```

我们注意到最后一个`<div>`设置的`shape-margin`过大，导致文字没有按照预期的那样让形状周围排列。这是因为形状外边距生效的范围只在父元素容纳块中，否则过大的外边距会被容纳块边缘裁剪。为了解决这一问题，我们可以同步扩大`margin`的范围：

```html
<html>
<head>
    <style>
        .root {
            border: 1px solid black;
            width: 15rem;
            margin-bottom: 1rem;
        }
        img {
            float: left;
            width: 10rem;
            shape-outside: url("https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/star.svg");
        }
        p {
            margin-top: 0.5rem;
            margin-bottom: 0.5rem;
        }
    </style>
</head>
<body>
    <div class="root">
        <img style="shape-margin: 2rem; margin: 0 2rem 2rem 0;" src="https://meyerweb.github.io/csstdg4figs/10-floating-and-shapes/i/star.svg"/>
        <p>这个元素中包含了一个向左浮动的图片和一个p标签，其中图片设置了shape-margin: 2rem;。超出容纳块的形状会被裁剪。</p>
    </div>
</body>
</html>
```

# §8 定位

CSS一共提供了五种定位类型，由`position`属性指定：

| `position`属性值 | 中文名称 | 作用                                               | 非根元素的容纳块                                             |
| ---------------- | -------- | -------------------------------------------------- | ------------------------------------------------------------ |
| `static`(缺省)   | 静态定位 | 块级元素生成矩形框，行内元素生成若干行框           | 父块级元素容纳块                                             |
| `relative`       | 相对定位 | 元素框的位置偏移一定的距离，形状与`static`一样不变 | 父块级元素容纳块                                             |
| `sticky`         | 粘性定位 | 当且仅当达到触发粘滞的条件是，元素框才脱离文档流   | 如果父元素是块级元素，则为父元素的内边距块（即边框区以内）；如果父元素是行内元素，则为父元素内容区的外切矩形。 |
| `absolute`       | 绝对定位 | 元素框完全脱离文档流，位置参照物依然是父元素容纳块 |                                                              |
| `fixed`          | 固定定位 | 元素框完全脱离文档流，位置参照物是视图容纳块       |                                                              |

## §8.1 偏移属性

在相对定位、绝对定位、粘滞定位和固定定位中，需要声明指定定位元素的各边相对容纳块的偏移距离，使用的属性成为偏移属性：`top`、`right`、`bottom`、`left`。

```html
<html>
<head>
    <style>  
        div.container {
            background-color: lightgray;
            width: 20rem;
            height: 10rem;
            position: relative;
        }
        div.item {
            background-color: lightblue;
            position: absolute;
            top: 50%;
            left: 50%;
            bottom: 0;
            right: 0;
        }
    </style>
</head>
<body>
    <div class="container">
        这是一个容纳框
        <div class="item">这是一个定位元素</div>
    </div>
</body>
</html>
```

在定位元素中，宽度和高度不一定非要用`width`和`height`属性所定义，我们还可以用`top`、`right`、`bottom`、`left`四个便宜属性完全确定。

## §8.2 限制高度和宽度

CSS可以限制一个元素的高度范围与宽度范围，可以通过`min-width`和`max-width`指定宽度的范围，通过`min-height`和`max-height`指定高度的范围。这也是许多响应式设计、移动端视图的基础。

```html
<html>
<head>
    <style>  
        div.container {
            background-color: lightgray;
            width: 80%;
            height: 10rem;
            position: relative;
        }
        div.item {
            background-color: lightblue;
            position: absolute;
            min-width: 20rem;
            top: 50%;
            left: 50%;
            bottom: 0;
            right: 0;
        }
    </style>
</head>
<body>
    <div class="container">
        这是一个容纳框
        <div class="item">为了保证这个元素能容纳其中的内容，只有当页面足够宽时，这个定位元素的宽度才是父元素的50%。</div>
    </div>
</body>
</html>
```

## §8.3 内容溢出(`overflow`)

`overflow`属性决定了内容溢出容纳框时的行为：

| `overflow`属性值 | 作用                             |
| ---------------- | -------------------------------- |
| `visiable`(缺省) | 超出元素的内容是可见的           |
| `hidden`         | 超出元素的内容是隐形的           |
| `scroll`         | 超出元素的内容以滚动条的形式展示 |
| `auto`           | 由浏览器自行决定                 |

```html
<html>
<head>
    <style>  
        div.container {
            background-color: lightgray;
            width: 20rem;
            height: 5rem;
            position: relative;
            margin-bottom: 1rem;
        }
    </style>
</head>
<body>
    <div class="container" style="overflow: visible;">
        <p>这个容纳框包含着多行文本</p>
        <p>这个容纳框的样式为overflow: visible;</p>
        <p>这意味着溢出的内容是可见的</p>
    </div>
    <div class="container" style="overflow: hidden;">
        <p>这个容纳框包含着多行文本</p>
        <p>这个容纳框的样式为overflow: hidden;</p>
        <p>这意味着溢出的内容是隐形的</p>
    </div>
    <div class="container" style="overflow: scroll;">
        <p>这个容纳框包含着多行文本</p>
        <p>这个容纳框的样式为overflow: scroll;</p>
        <p>这意味着溢出的内容以滚动条的形式展示</p>
    </div>
    <div class="container" style="overflow: auto;">
        <p>这个容纳框包含着多行文本</p>
        <p>这个容纳框的样式为overflow: auto;</p>
        <p>这意味着溢出的内容由浏览器自行决定</p>
    </div>
</body>
</html>
```

## §8.4 可见性(`visibility`)

`visibility`属性决定着元素的可见性

| `visibility`属性值 | 作用                                                   |
| ------------------ | ------------------------------------------------------ |
| `visible`(缺省)    | 元素可见                                               |
| `hidden`           | 语速不可见，但仍然影响布局                             |
| `collapse`         | 对于渲染表格来说负责折叠，对于非表格元素等价于`hidden` |

> 注意：虽然`display: none`也能让元素隐形，但是实际上该属性将元素完全从文档流中移除，不再占据位置。而`visibility: hidden`不会影响页面布局，甚至边框也依然保留，从视觉效果来说等价于`opacity: 0`。

`visibility`属性是继承的，但是给子元素设置`visibility: visible`，可以突破父元素`visibility: none`的限制。

## §8.5 绝对定位(`position:absolute`)

绝对定位的元素完全从文档流中移除，其位置由相对容纳块的距离确定。绝对定位元素的容纳块是**`position`属性不是`static`的最近的祖辈元素**。我们直到`position`属性的默认值就是`static`。所以我们选中容纳块后，一般将其属性改为`position: relative`，而且不设置偏移。

```html
<html>
<head>
    <style>
        body {
            width: 40rem;
            background-color: lightgray;
            border: 1px solid black;
            position: relative;
        }
        div {
            width: 20rem;
            margin-bottom: 1rem;
            background-color: gray;
        }
        .contain {
            position: relative;
        }
        b {
            position: absolute;
            top: auto;
            right: 0;
            bottom: 0;
            left: auto;
            border: 1px solid black
        }
    </style>
</head>
<body>
    <div>This is a <b>paragraph.</b></div>
    <div class="contain">This is a <b>paragraph.</b></div>
</body>
</html>
```

接下来我们重点讨论绝对定位中的偏移属性的`auto`行为。设想这个绝对定位元素的改为默认值`position: static`，那么它肯定会回到文档流中，并占据一定的位置。这个位置所处的`top`/`right`/`left`即为`position: absolute`与偏移属性值`auto`所采用的值。

```html
<html>
<head>
    <style>
        div {
            width: 13rem;
            border: 1px solid black;
            margin-left: 1rem;
        }
        div > span {
            position: absolute; /* 偏移属性相对于body */
            top: auto;
            left: 0;
        }
    </style>
</head>
<body>
    <div>
        <span>[1]</span>
        This is a item for reference of some books and articles.
    </div>
</body>
</html>
```

我们在[§5.3 横向格式化属性](##§5.3 横向格式化属性)一节已经知道了盒模型的横向尺寸必须满足七个属性相加恰好为为容纳块横向距离。现在我们根据学习的定位知识，完善这一条件：
$$
\begin{cases}\begin{align}
	&\text{\textcolor{red}{left}} +
	\text{margin\_left} + 
	\text{border\_left} + 
	\text{padding\_left} + \\
	&\text{width} + \\
	&\text{\textcolor{red}{right}} + 
	\text{margin\_right} +
	\text{border\_right} +
	\text{padding\_right}
\end{align}\end{cases} = 容纳块宽度
$$

1. 当`width`为`auto`时，当宽度剩余空间足够时，宽度会跟随着内容自动缩放，尽量取得最小值；当宽度剩余空间不足，导致必须换行时，宽度会尽量取得最大值。

2. 当`left`/`right`/`top`为`auto`时，参照本节中偏移属性值`auto`的行为。

   ```html
   <html>
   <head>
       <style>
       </style>
   </head>
   <body>
       <div style="
           position: relative; 
           width: 15em; 
           border: 1px solid black;"
       >
           这句话的一部分被绝对定位元素遮盖住了。
           <span style="
               position: absolute; 
               top: 0; 
               left: 0; 
               right: auto; 
               width: auto;
               background: silver;"
           >
               绝对定位元素
           </span> 
           它本应在文档流的中间，但是被因为被设定为绝对定位元素，脱离了文档流。
       </div>
   </body>
   </html>
   ```

3. 当`margin-left`/`margin-right`均为`auto`时，元素将水平居中：

   ```html
   <html>
   <head>
       <style>
       </style>
   </head>
   <body>
       <div style="
           position: relative; 
           width: 15em; 
           border: 1px solid black;"
       >
           这句话的一部分被绝对定位元素遮盖住了。
           <span style="
               position: absolute; 
               top: 0; 
               left: 0;
               margin-left: auto;
               margin-right: auto;
               width: auto;
               background: silver;"
           >
               绝对定位元素
           </span> 
           它本应在文档流的中间，但是被因为被设定为绝对定位元素，脱离了文档流。
       </div>
   </body>
   </html>
   ```

4. 如果产生过约束，那么对于从左向右书写的语言，`right`属性将失效，其属性值自动变为`auto`。反之对于从右向左的书写的语言，`left`属性将失效。

   ```html
   <html>
   <head>
       <style>
       </style>
   </head>
   <body>
       <div style="
           position: relative; 
           width: 15em; 
           border: 1px solid black;"
       >
           这句话的一部分被绝对定位元素遮盖住了。
           <span style="
               position: absolute; 
               top: 0; 
               left: 1rem;
               right: 1rem; /*右边缘距离容纳框显然不足1rem*/
               width: 8rem;
               background: silver;"
           >
               绝对定位元素
           </span> 
           它本应在文档流的中间，但是被因为被设定为绝对定位元素，脱离了文档流。
       </div>
   </body>
   </html>
   ```

到目前为止，我们已经讲完了水平方向的定位行为。垂直方向的定位行为非常相似，只有以下两点不同：

1. `bottom: auto`将会被无条件忽略，一切以`top`为准。
2. 当产生过约束时，`top`具体属性值永远不会被重置。

## §8.6 固定定位(`position:fixed`)

在[§8.5 绝对定位](##§8.5 绝对定位)一节中，我们直到绝对定位参照的容纳块是从父级向上找到的第一个`position`属性非`static`的最近元素。但是固定定位参照的容纳块恒为视区。两者都会将元素从文档流中完全移除。

固定定位经常被用于后台管理页面的页眉、侧边栏、内容区和页脚的排版中：

```html
<html>
<head>
    <style>
        body { margin: 0; }
        body > div { 
            position: fixed; 
            text-align: center;
        }
        div.header {
            background-color: lightgray;
            top: 0;
            left: 0;
            width: 100%;
            height: 2rem;
        }
        div.footer {
            background-color: lightgray;
            bottom: 0;
            left: 0;
            width: 100%;
            height: 2rem;
        }
        div.sidebar {
            background-color: antiquewhite;
            top: 2rem;
            left: 0;
            width: 10rem;
            height: calc(100% - 4rem);
        }
        div.container {
            background-color: aquamarine;
            top: 2rem;
            left: 10rem;
            width: calc(100% - 10rem);
            height: 100%;
        }

    </style>
</head>
<body>
    <div class="header">我是页眉</div>
    <div class="sidebar">我是侧边栏</div>
    <div class="container">我是内容</div>
    <div class="footer">我是页脚</div>
</body>
</html>
```

## §8.7 相对定位(`position:relative`)

相对定位较为特殊，因为它不会让元素脱离文档流，而且身上的偏移属性强调的是变化量：

```html
<html>
<head>
    <style>
        strong {
            background-color: lightcoral;
            position: relative;
            top: 5px;
            left: 5px;
        }
    </style>
</head>
<body>
    <p>This is a <strong>paragraph</strong>.</p>
</body>
</html>
```

如果相对定位产生了过约束，从CSS2.1开始，强行令`bottom`为`-top`，或者`right`为`-left`。

## §8.8 粘滞定位(`position:sticky`)

粘滞定位指的是某个元素可以在固定定位和绝对定位之间切换。例如在某些面向移动端的无序列表中，`<ul>`刚出现时是绝对定位；当视区顶部已经没过第一个`<li>`时，`<ul>`改为固定定位，固定在视区顶部；当视区顶部没过最后一个`<li>`时，`<ul>`改为绝对定位。

```html
<html>

<head>
    <style>
        div.container {
            width: 15rem;
            height: 20rem;
            border: 1px solid black;
            overflow: scroll;
            position: relative;
        }
        .container>h2.start {
            margin: 0;
            padding: 0.5rem 0.25rem 0.25rem;
            background-color: lightgray;
            position: sticky;
            top: 0;
        }
        .container>h2.end {
            margin: 0;
            padding: 0.5rem 0.25rem 0.25rem;
            background-color: lightgray;
            position: sticky;
            bottom: 0;
        }
        .container>ol {
            margin: 0;
            padding: 0;
        }
        .container>ol>li {
            padding-top: 0.5rem;
            padding-bottom: 0.5rem;
        }
    </style>
</head>
<body>
    <div class="container">
        <h2 class="start">A start</h2>
        <ol>
            <li>abandon</li>
            <li>ability</li>
            <li>able</li>
            <li>abnormal</li>
            <li>aboard</li>
            <li>above</li>
            <li>abroad</li>
            <li>absence</li>
        </ol>
        <h2 class="start">B start</h2>
        <ol>
            <li>Baddd Spellah</li>
            <li>Bif Naked</li>
        </ol>
        <h2 class="start">C start</h2>
        <ol>
            <li>Cake</li>
            <li>Chemical Brothers</li>
            <li>Crystal Method</li>
        </ol>
        <h2 class="start">D start</h2>
        <ol>
            <li>Deee-Lite</li>
            <li>Die Kreuzen</li>
            <li>DJ Z-Trip</li>
            <li>Django Reinhardt</li>
        </ol>
        <h2 class="end">Contact us: ...</h2>
        <ol>
            <li>Address: ...</li>
            <li>Phone: ...</li>
        </ol>
    </div>
</body>
</html>
```

## §8.9 Z轴位置(`z-index`)

当多个元素重叠在一起时，`z-index`负责控制图层覆盖。该属性可以设置为任意整数（包括负数）。

```html
<html>
<head>
    <style>
        .container {
            width: 30rem;
            height: 15rem;
            position: relative;
            background-color: lightgoldenrodyellow;
            z-index: 1;
        }
        .container > div { 
            position: absolute;
            padding-top: 0.5rem;
            text-align: center;
        }
        .container > div:nth-child(1) {
            top: 1rem;
            bottom: 1rem;
            left: 2rem;
            width: 15rem;
            background-color: lightblue;
            z-index: 2;
        }
        .container > div:nth-child(2) {
            top: 2rem;
            height: 2rem;
            left: 15rem;
            width: 10rem;
            background-color: lightcoral;
            z-index: 3;
        }
        .container > div:nth-child(3) {
            top: 5rem;
            height: 2rem;
            left: 15rem;
            width: 10rem;
            background-color: lightgreen;
            z-index: 4;
        }
    </style>
</head>
<body>
    <div class="container">
        <div>这是书</div>
        <div>这是书签</div>
        <div>这是书签</div>
    </div>
</body>
</html>
```

`z-index: auto`完全等价于`z-index: 0`。

这里的`z-index`虽然会继承给子元素，但是严格来说继承的不是属性值，而是堆叠上下文。这有点类似于字典序：如果事先给定了`a`和`z`这两个"`z-index`"，那么无论后面的`z-index`多么高，也就像`azzzz`和`zaaaa`一样，`a`开头的始终大于`z`开头的。

确定元素间的堆叠顺序，需要按照如下规则：

1. 如果两个元素互为父子关系，那么无论子元素的`z-index`属性值有多低，它始终在父元素的前面。
2. 如果两个元素不为父子关系，则向上寻找它们的共同祖辈元素，根据祖辈元素的两个下属父元素的`z-index`判断。

# §9 弹性盒布局

弹性盒依赖于父子关系。在父元素上声明`display:flex`或`display:inline=flex`就能让父元素变成弹性容器，其子元素成为弹性元素。弹性盒布局尤其适用于响应式设计，可以让元素根据可用空间的大小改变尺寸。

> 注意：新版本CSS可以将`flex`和`inline-flex`分别拆成两个关键字，记为`display: flex block`和`display: flex inline`。

弹性盒布局最初的目的是实现一维方向上的布局。栅格布局实现的才是二维布局。

## §9.1 弹性容器

### §9.1.1 主轴方向(`flex-direction`)

`flex-direction`属性控制排布弹性元素的主轴方向。

| `flex-direction`属性值 | 作用               |
| ---------------------- | ------------------ |
| `row`                  | 沿语言水平书写方向 |
| `row-reverse`          | 沿语言水平书写逆向 |
| `column`               | 沿语言垂直书写方向 |
| `column-reverse`       | 沿语言垂直书写逆向 |

```html
<html>
<head>
    <style>
        .nav {
            display: flex;
            border-bottom: 1px solid black;
            margin: 1rem;
        }
        .nav:nth-child(1) { flex-direction: row; }
        .nav:nth-child(2) { flex-direction: row-reverse; }
        .nav:nth-child(3) { flex-direction: column; }
        .nav:nth-child(4) { flex-direction: column-reverse; }
        a {
            margin: 0 5px;
            padding: 5px 15px;
            border-radius: 3px 3px 0 0;
            background-color: lightgray;
            text-decoration: none;
            color: black;
        }
        a:hover, a:focus, a:active {
            background-color: lightblue;
        }
    </style>
</head>
<body>
    <div class="nav">
        <a href="#">Home</a>
        <a href="#">About</a>
        <a href="#">Contact</a>
    </div>
    <div class="nav">
        <a href="#">Home</a>
        <a href="#">About</a>
        <a href="#">Contact</a>
    </div>
    <div class="nav">
        <a href="#">Home</a>
        <a href="#">About</a>
        <a href="#">Contact</a>
    </div>
    <div class="nav">
        <a href="#">Home</a>
        <a href="#">About</a>
        <a href="#">Contact</a>
    </div>
</body>
</html>
```

> 注意：遇到从右向左书写的预言时，在实践中不推荐使用`flex-direction`来改变弹性盒布局的主轴方向，而是使用`<div dir="">`或`writing-mode`属性来指定。
>
> ```html
> <html>
> <head>
>     <style>
>         .nav {
>             display: flex;
>             border-bottom: 1px solid black;
>             margin: 1rem;
>         }
>         a {
>             margin: 0 5px;
>             padding: 5px 15px;
>             border-radius: 3px 3px 0 0;
>             background-color: lightgray;
>             text-decoration: none;
>             color: black;
>         }
>         a:hover, a:focus, a:active {
>             background-color: lightblue;
>         }
>     </style>
> </head>
> <body>
>     <div class="nav" dir="rtl" style="flex-direction: row;">
>         <a href="#">א</a>
>         <a href="#">ב</a>
>         <a href="#">ג</a>
>     </div>
>     <div class="nav" style="writing-mode: vertical-rl; flex-direction: row;">
>         <a href="#">一</a>
>         <a href="#">二</a>
>         <a href="#">三</a>
>     </div>
> </body>
> </html>
> ```

### §9.1.2 换行(`flex-wrap`)

`flex-wrap`属性决定了弹性元素的换行行为。

| `flex-wrap`属性值 | 作用                                         |
| ----------------- | -------------------------------------------- |
| `nowrap`(缺省)    | 不允许弹性元素换行                           |
| `wrap`            | 允许弹性元素换行，溢出的内容在第一行之前显示 |
| `wrap-reverse`    | 允许弹性元素换行，溢出的内容在第一行之后显示 |

```html
<html>
<head>
    <style>
        body > div {
            margin-bottom: 1rem;
        }
        div.container {
            display: flex;
            background-color: lightgray;
            width: 15rem;
        }
        div.container > * {
            background-color: lightblue;
            margin: 1rem;
            padding: 0.5rem;
        }
    </style>
</head>
<body>
    <div>
        flex-wrap: nowrap;
        <div class="container" style="flex-wrap: nowrap;">
            <div>A</div>
            <div>B</div>
            <div>C</div>
            <div>D</div>
            <div>E</div>
        </div>
    </div>
    <div>
        flex-wrap: nowrap;
        <div class="container" style="flex-wrap: wrap;">
            <div>A</div>
            <div>B</div>
            <div>C</div>
            <div>D</div>
            <div>E</div>
        </div>
    </div>
    <div>
        flex-wrap: nowrap;
        <div class="container" style="flex-wrap: wrap-reverse;">
            <div>A</div>
            <div>B</div>
            <div>C</div>
            <div>D</div>
            <div>E</div>
        </div>
    </div>
</body>
</html>
```

### §9.1.3 弹性流(`flex-flow`)

`flex-flow`属性是`flex-direction`和`flex-wrap`的两个属性的简写形式。例如`flex-flow: row nowrap`就同时定义了`flex-direction: row`和`flex-wrap: nowrap`两个属性。

`flex-flow`属性最后同时接受两个关键字。若其中一个未指定，则采用对应属性的缺省值。

### §9.1.4 调整内容(`justify-content`)

`justify-content`属性控制弹性容器在主轴上为弹性元素排版的行为。该属性应用于弹性容器上，而非弹性元素上。

| `justify-content`属性值 | 内容                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `flex-start`(缺省)      | 居左紧排。若溢出，则只从主轴终边溢出                         |
| `flex-end`              | 居右紧排。若溢出，则只从主轴起边溢出                         |
| `center`                | 居中紧排。若溢出，则从主轴起边和终边溢出相等距离             |
| `space-between`         | 间隔比例为`0:1:1:...:1:1:0`。如果只有一个弹性子元素，则向主轴起边对齐。若溢出，则只从主轴终边溢出 |
| `space-around`          | 间隔比例为`1:2:2:...:2:2:1`。如果只有一个子元素，则居中排列。若溢出，则从主轴起边和终边溢出相等距离 |
| `space-evenly`          | 间隔比例为`1:1:1:...:1:1:1`。如果只有一个子元素，则居中排列。若溢出，则从主轴起边和终边溢出相等距离 |

```html
<html>
<head>
    <style>
        body > div {
            margin-bottom: 1rem;
        }
        div.container {
            display: flex;
            background-color: lightgray;
            width: 15rem;
        }
        div.container > * {
            background-color: skyblue;
            margin: 0.2rem;
            padding: 0.2rem;
        }
        div.container:nth-child(1) { justify-content: flex-start; }
        div.container:nth-child(2) { justify-content: flex-end; }
        div.container:nth-child(3) { justify-content: center; }
        div.container:nth-child(4) { justify-content: space-between; }
        div.container:nth-child(5) { justify-content: space-around; }
        div.container:nth-child(6) { justify-content: space-evenly; }
    </style>
</head>
<body>
    justify-content: flex-start;
    <div class="container">
        <div>A</div><div>B</div><div>C</div>
    </div>

    justify-content: flex-end;
    <div class="container">
        <div>A</div><div>B</div><div>C</div>
    </div>

    justify-content: center;
    <div class="container">
        <div>A</div><div>B</div><div>C</div>
    </div>

    justify-content: space-between;
    <div class="container">
        <div>A</div><div>B</div><div>C</div>
    </div>

    justify-content: space-around;
    <div class="container">
        <div>A</div><div>B</div><div>C</div>
    </div>

    justify-content: space-evenly;
    <div class="container">
        <div>A</div><div>B</div><div>C</div>
    </div>
</body>
</html>
```

### §9.1.5 对齐元素(`align-items`)

`align-items`属性决定弹性元素在垂轴上的对齐方式。该属性只能应用于弹性容器上，而不能用在单个弹性元素上。

| `align-items`属性值 | 作用                                                         |
| ------------------- | ------------------------------------------------------------ |
| `flex-start`        | 将所有弹性元素沿垂轴起边对齐                                 |
| `flex-end`          | 将所有弹性元素沿垂轴终边对齐                                 |
| `center`            | 让所有弹性元素的中心线重叠                                   |
| `baseline`          | 将所有元素的第一条基线重叠在一起，让基线最低的元素保持不动（受`margin-top`和`font-size`影响），其他元素移动位置 |
| `stretch`(缺省)     | 将所在行中的弹性元素拉伸至与最高弹性元素一样的水平，除非设置了`width`/`min-width`/`max-width`/`height`/`min-height`/`max-height` |

```html
<html>
<head>
    <style>
        body > div.container {
            display: flex;
            flex-wrap: wrap;
            background-color: lightgray;
            width: 15rem;
            margin-bottom: 1rem;
        }
        body > div.container > * {
            background-color: lightblue;
            border: 1px solid black;
            margin: 0.2rem;
            width: 4rem;
            text-align: center;
        }
        .larger {
            font-size: x-large;
            font-weight: bold;
        }
        body > div.container:nth-child(1) { align-items: flex-start; }
        body > div.container:nth-child(2) { align-items: flex-end; }
        body > div.container:nth-child(3) { align-items: center;}
        body > div.container:nth-child(4) { align-items: baseline; }
        body > div.container:nth-child(5) { align-items: stretch; }
    </style>
</head>
<body>
    align-items: flex-start;
    <div class="container"></div>

    align-items: flex-end;
    <div class="container"></div>

    align-items: center;
    <div class="container"></div>

    align-items: baseline;
    <div class="container"></div>

    align-items: stretch;
    <div class="container"></div>

    <script>
        const containerDOMs = document.querySelectorAll('.container');
        containerDOMs.forEach(element => {
            element.innerHTML = "<div>1</div><div>2<br>2</div><div class='larger'>3<br>3<br>3</div><div>4<br>4<br>4<br>4</div><div>5<br>5<br>5<br>5<br>5</div>";
        });
    </script>
</body>
</html>
```

### §9.1.6 对齐元素(`align-self`)

前面在[§9.5 对齐元素(`align-items`)](##§9.5 对齐元素(`align-items`))一节我们提到，`align-items`属性只能用在弹性容器身上，从而作用于所有弹性元素。`align-self`的作用与取值与`align-items`完全一致，唯一不同的是`align-self`只能用在弹性元素身上，而且可以覆盖父元素的`align-items`。

```html
<html>
<head>
    <style>
        body > div.container {
            display: flex;
            flex-wrap: wrap;
            background-color: lightgray;
            width: 30rem;
            margin-bottom: 1rem;
        }
        body > div.container > * {
            background-color: lightblue;
            border: 1px solid black;
            margin: 0.2rem;
            width: 4rem;
            text-align: center;
        }
        body > div.container:nth-child(1) { align-items: flex-start; }
        body > div.container > *:nth-child(1) { align-self: stretch; } /* 单独的stretch */
        body > div.container > *:nth-child(2) { align-self: center; } /* 单独的center */
        body > div.container > *:nth-child(3) { align-self: flex-end; } /* 单独的center */
    </style>
</head>
<body>
    align-items: stretch;
    <div class="container"></div>
    <script>
        const containerDOMs = document.querySelectorAll('.container');
        containerDOMs.forEach(element => {
            element.innerHTML = "<div>1</div><div>2<br>2</div><div>3<br>3<br>3</div><div>4<br>4<br>4<br>4</div><div>5<br>5<br>5<br>5<br>5</div>";
        });
    </script>
</body>
</html>
```

### §9.1.7 对齐内容(`align-content`)

`align-content`属性决定弹性容器空间充足时如何在垂轴方向对齐每一行元素，也决定空间不足时让弹性元素从哪一个方向溢出。该属性可以看作是`justify-content`的垂轴版，只不过调整的不再是各个弹性元素的横向间隔，而是各个行的纵向间隔。

| `align-content`属性值 | 作用                                                         |
| --------------------- | ------------------------------------------------------------ |
| `flex-start`          | 所有的弹性元素行都靠弹性容器顶部对齐                         |
| `flex-end`            | 所有的弹性元素行都靠弹性容器底部对齐                         |
| `center`              | 最上元素行与弹性容器顶部，和最下元素行与弹性容器底部之间的距离相等 |
| `space-between`       | 间隔比例为`0:1:1:...:1:1:0`。如果只有一个弹性子元素，则向主轴起边对齐。若溢出，则只从主轴终边溢出 |
| `space-around`        | 间隔比例为`1:2:2:...:2:2:1`。如果只有一个弹性子元素，则向主轴起边对齐。若溢出，则只从主轴终边溢出 |
| `space-evenly`        | 间隔比例为`1:1:1:...:1:1:1`。如果只有一个弹性子元素，则向主轴起边对齐。若溢出，则只从主轴终边溢出 |
| `stretch`(缺省)       | 调整行高填满弹性容器的整个高度                               |

```html
<html>
<head>
    <style>
        body > div.container {
            display: flex;
            flex-wrap: wrap;
            background-color: lightgray;
            width: 30rem;
            height: 15rem;
        }
        body > div.container > * {
            background-color: lightblue;
            border: 1px solid black;
            width: 6rem;
            text-align: center;
        }
        body > div.container:nth-child(1) { align-content: flex-start; }
        body > div.container:nth-child(2) { align-content: flex-end; }
        body > div.container:nth-child(3) { align-content: center; }
        body > div.container:nth-child(4) { align-content: space-between; }
        body > div.container:nth-child(5) { align-content: space-around; }
        body > div.container:nth-child(6) { align-content: space-evenly; }
        body > div.container:nth-child(7) { align-content: stretch; }
    </style>
</head>
<body>
    align-content: flex-start;
    <div class="container"></div>
    align-content: flex-end;
    <div class="container"></div>
    align-content: center;
    <div class="container"></div>
    align-content: space-between;
    <div class="container"></div>
    align-content: space-around;
    <div class="container"></div>
    align-content: space-evenly;
    <div class="container"></div>
    align-content: stretch;
    <div class="container"></div>
    <script>
        const containerDOMs = document.querySelectorAll('.container');
        containerDOMs.forEach(element => {
            element.innerHTML = "<div>1</div><div>2<br>2</div><div>3<br>3<br>3</div><div>4<br>4<br>4<br>4</div><div>5<br>5<br>5<br>5<br>5</div>";
        });
    </script>
</body>
</html>
```

## §9.2 弹性元素

### §9.2.1 弹性元素的特点

我们知道，被`display: flex`或`display: flex-inline`修饰的元素是弹性容器，其直接的子元素称为弹性元素。弹性元素的"弹性"一词，指的是弹性元素能在主轴方向上灵活地改变尺寸。

弹性元素有以下特点：

1. 弹性元素的外边距(`margin`)不折叠。

2. 弹性元素不会受到浮动(`float`)和`clear`属性的影响，给弹性元素设置这些元素会被忽略，因此不会脱离文档流。

   ```html
   <html>
   <head>
       <style>
           .container {
               display: flex;
               background-color: lightgray;
           }
           .container > img {
               float: left; /* 这个属性没有生效 */
           }
       </style>
   </head>
   <body>
       <div class="container">
           <h1>标题</h1>
           <img src="https://www.google.com/images/branding/googlelogo/2x/googlelogo_light_color_272x92dp.png" style="width: 10rem;height: 4rem;">
           <span>内容</span>
       </div>
   </body>
   </html>
   ```

3. 所有弹性元素都会转变为块级元素，除非它被声明为`position: absolute`，这会导致弹性元素脱离文档流，因此不会再参与弹性布局。但是弹性布局的相关属性（例如`justify-content`和`align-self`）会影响`top`/`left`的默认值。

### §9.2.2 弹性增长因子(`flex-grow`)

`flex-grow`表示弹性元素为了占满可用空间可以增加多少距离。其属性值是一个非负实数，默认值为0。

`flex-grow`的对元素宽度的计算算法如下：

1. 如果$\displaystyle\sum_{i}{\text{width}(i)}\geq\text{width}(弹性容器)$，则退出算法。
2. 记$\Delta\text{width}=\text{width}(弹性容器)-\displaystyle{\sum_{i}{\text{width}(i)}}>0$。也就是说所有弹性元素的宽度加起来还能增加$\Delta\text{width}$。而每个元素的$\text{flex-grow}(i)\times\text{width}(i)$就是按比例瓜分$\Delta\text{width}$的权重。综上所述，各个元素增长后的宽度为$\text{width}'(i)=\text{width}(i)+\displaystyle\frac{\text{flex-grow}(i)\times\text{width}(i)}{\displaystyle\sum_{i}{(\text{flex-grow}(i)\times\text{width}(i))}}\times\Delta\text{width}$。

```html
<html>
<head>
    <style>
        .container {
            display: flex;
            background-color: lightgray;
            border: 1px solid black;
            width: 60rem;
            margin-bottom: 0.5rem;
        }
        .container > * {
            height: 3rem;
            border: 1px solid black;
            margin: 0.5rem 0.2rem;
        }
        body > .container:nth-child(1) > *:nth-child(1){ width: 10rem; flex-grow: 0; }
        body > .container:nth-child(1) > *:nth-child(2){ width: 15rem; flex-grow: 0; }
        body > .container:nth-child(1) > *:nth-child(3){ width: 20rem; flex-grow: 0; }

        body > .container:nth-child(2) > *:nth-child(1){ width: 10rem; flex-grow: 1; }
        body > .container:nth-child(2) > *:nth-child(2){ width: 15rem; flex-grow: 2; }
        body > .container:nth-child(2) > *:nth-child(3){ width: 20rem; flex-grow: 5; }

        body > .container:nth-child(3) > *:nth-child(1){ width: 10rem; flex-grow: 0.125; }
        body > .container:nth-child(3) > *:nth-child(2){ width: 15rem; flex-grow: 0.250; }
        body > .container:nth-child(3) > *:nth-child(3){ width: 20rem; flex-grow: 0.625; }
    </style>
</head>
<body>
    <div class="container">
        <div>flex-grow: 0<br>width: 10rem</div>
        <div>flex-grow: 0<br>width: 15rem</div>
        <div>flex-grow: 0<br>width: 20rem</div>
    </div>
    <div class="container">
        <div>flex-grow: 1<br>width: 10rem</div>
        <div>flex-grow: 2<br>width: 15rem</div>
        <div>flex-grow: 5<br>width: 20rem</div>
    </div>
    <div class="container">
        <div>flex-grow: 0.125<br>width: 10rem</div>
        <div>flex-grow: 0.250<br>width: 15rem</div>
        <div>flex-grow: 0.625<br>width: 20rem</div>
    </div>
</body>
</html>
```

### §9.2.3 弹性缩减因子(`flex-shrink`)

`flex-shrink`表示为了防止溢出可以减少多少距离。该属性值是一个非负实数，初始值为1。

`flex-shrink`的对元素宽度的计算算法如下：

1. 如果$\displaystyle\sum_{i}{\text{width}(i)}\leq\text{width}(弹性容器)$，则退出算法。
2. 记$\Delta\text{width}=\displaystyle{\sum_{i}{\text{width}(i)}}-\text{width}(弹性容器)>0$。也就是说所有弹性元素的宽度加起来还能缩减$\Delta\text{width}$。而每个元素的$\text{flex-shrink}(i)\times\text{width}(i)$就是按比例瓜分$\Delta\text{width}$的权重。综上所述，各个元素增长后的宽度为$\text{width}'(i)=\text{width}(i)-\displaystyle\frac{\text{flex-shrink}(i)\times\text{width}(i)}{\displaystyle\sum_{i}{(\text{flex-shrink}(i)}\times\text{width}(i))}\times\Delta\text{width}$。

```html
<html>
<head>
    <style>
        .container {
            display: flex;
            flex-wrap: nowrap;
            background-color: lightgray;
            border: 1px solid black;
            width: 40rem;
            margin-bottom: 0.5rem;
        }
        .container > * {
            height: 3rem;
            border: 1px solid black;
            margin: 0.5rem 0.2rem;
        }
        body > .container:nth-child(1) > *:nth-child(1){ width: 10rem; flex-shrink: 0; }
        body > .container:nth-child(1) > *:nth-child(2){ width: 15rem; flex-shrink: 0; }
        body > .container:nth-child(1) > *:nth-child(3){ width: 20rem; flex-shrink: 0; }

        body > .container:nth-child(2) > *:nth-child(1){ width: 10rem; flex-shrink: 1; }
        body > .container:nth-child(2) > *:nth-child(2){ width: 15rem; flex-shrink: 1; }
        body > .container:nth-child(2) > *:nth-child(3){ width: 20rem; flex-shrink: 1; }

        body > .container:nth-child(3) > *:nth-child(1){ width: 10rem; flex-shrink: 10; }
        body > .container:nth-child(3) > *:nth-child(2){ width: 15rem; flex-shrink: 10; }
        body > .container:nth-child(3) > *:nth-child(3){ width: 20rem; flex-shrink: 10; }
    </style>
</head>
<body>
    <div class="container">
        <div>flex-shrink: 0<br>width: 10rem</div>
        <div>flex-shrink: 0<br>width: 15rem</div>
        <div>flex-shrink: 0<br>width: 20rem</div>
    </div>
    <div class="container">
        <div>flex-shrink: 1<br>width: 10rem</div>
        <div>flex-shrink: 1<br>width: 15rem</div>
        <div>flex-shrink: 1<br>width: 20rem</div>
    </div>
    <div class="container">
        <div>flex-shrink: 10<br>width: 10rem</div>
        <div>flex-shrink: 10<br>width: 15rem</div>
        <div>flex-shrink: 10<br>width: 20rem</div>
    </div>
</body>
</html>
```

> 提示：规范明确不鼓励通过`flex-shrink`属性指定弹性缩减因子，而是通过`flex`属性统一指定。

### §9.2.4 弹性基准(`flex-basis`)

`flex-basis`属性用于定义弹性元素的初始尺寸，也就是在`flex-grow`和`flex-shrink`生效前的尺寸。该属性值可以为绝对长度，也可以为参照主轴长度的相对长度，也可以为`auto`，此时优先参考`width`/`height`，若未指定则为`content`。

> 注意：弹性元素的`flex`/`flex-basis`属性永远优先，因此即使`height`和`width`有`!important`也不会生效。除非`flex-basis`的属性值为缺省状态下的`auto`，此时`flex-basis`优先参考`width`/`height`。
>
> ```html
> <html>
> <head>
>     <style>
>         .container {
>             display: flex;
>             width: 30rem;
>             background-color: lightgray;
>             border: 1px solid black;
>             flex-wrap: nowrap;
>         }
>         .container > * {
>             padding: 1rem;
>             margin: 0.1rem;
>             border: 1px solid black;
>         }
>         .container > *:nth-child(1) { flex-basis: 10rem; width: 5rem; } /* width属性没有生效 */
>         .container > *:nth-child(2) { flex-basis: 10rem; width: 10rem; }
>     </style>
> </head>
> <body>
>     <div class="container">
>         <div>Hello</div>
>         <div>World</div>
>     </div>
> </body>
> </html>
> ```

如果没有声明`flex`和`flex-basis`属性，则`flex-basis`默认为`auto`。但是如果声明了`flex`，却没有在其中显示声明`flex-basis`，则`flex-basis`默认为`0`，这代表着其弹性基准默认为整个主轴。

### §9.2.5 弹性属性(`flex`)

`flex`属性是`flex-grow`、`flow-shrink`、`flex-basis`三个属性的集合体。语法为`[flex-grow flex-shrink>? || flex-basis] | none`，也就是说`flex-grow flex-shrink?`和`flex-basis`至少出现一个，顺序任意。初始值为`0 1 auto`。

除此以外，CSS也为`flex`属性定义了一些常用的关键字，用于简写：

| `flex`简写关键字 | 作用                                                         |
| ---------------- | ------------------------------------------------------------ |
| `initial`        | 等价于`flex: 0 1 auto`。根据`width`/`height`属性值，作为`max-width`/`max-height`和`flex-basis` |
| `auto`           | 等价于`flex: 1 1 auto`。根据`width`/`height`属性值，作为`flex-basis` |
| `none`           | 等价于`flex: 0 0 auto`。根据`width`/`height`属性值，作为`max-width`/`max-height`和`min-width`/`min-height`和`flex-basis` |
| `<number>`       | 等价于`flex: <numbder> 0 0`。将该`<numbder>`作为`flex-grow`属性值 |

### §9.2.6 顺序(`order`)

默认情况下，弹性元素的显示顺序与源代码完全相同或相反。如果要对齐进行更细致的排列，可以使用`order`属性控制单个弹性元素的显示顺序。改属性值为任意整数（包括负整数），初始值为0。数值越大，排列位置越靠后。

`order`属性值相同的弹性元素属于同一个排序组。排序组之间的顺序由`order`属性值决定，排序组之内的顺序还是由源代码决定。

```html
<html>
<head>
    <style>
        .container {
            display: flex;
            width: 30rem;
            background-color: lightgray;
            border: 1px solid black;
            flex-wrap: nowrap;
        }
        .container > * {
            padding: 1rem;
            margin: 0.1rem;
            border: 1px solid black;
            text-align: center;
            width: 1rem;
        }
        .container > *:nth-child(2) { order: 1; } /* 2排到了最后 */
        .container > *:nth-child(5) { order: -1; } /* 5排到了最前 */
        .container > *:nth-child(8) { order: -1; } /* 2和5同属一个排序组，在源代码中2在5的前面 */
    </style>
</head>
<body>
    <div class="container">
        <div>1</div>
        <div>2</div>
        <div>3</div>
        <div>4</div>
        <div>5</div>
        <div>6</div>
        <div>7</div>
        <div>8</div>
        <div>9</div>
    </div>
</body>
</html>
```

> 注意：`order`属性改变的是显示顺序，它不能改变Tab键的索引顺序。

## §9.3 响应式设计

弹性盒布局被广泛应用与响应式设计中。考虑以下需求：将页面垂直分成三列，从左往右分别为菜单、内容区、侧边栏。我们给出以下通用示例CSS并进行分析：

```css
nav {flex: 0 1 200px; min-width: 150px}
article {flex: 1 2 600px;}
aside {flex: 0 1 200px; min-width: 150px}
```

1. 当页面空间足够时，由于左右两列的`flex-grow`均为0，所以尺寸不变，均为`200px`。只有`<article>`参与拉伸，填满所有空余空间。
2. 当页面空间略有不足时，由于三列的`flex-shrink`均不为0，所以尺寸都会减小。三者按照$1\times 200 :2\times600:1\times 200=1:6:1$的比例缩小。
3. 当页面空间继续不足时，触发了左右两列的`min-width: 150px`限制，所以左右两列不再参与尺寸变化，只有中间一列继续缩小。

```html
<html>
<head>
    <style>
        * {
            margin: 0;
            padding: 0;
        }
        header {
            width: 100%;
            font-size: xx-large;
            color: white;
            background-color: darkblue;
            text-align: center;
            height: 4rem;
            line-height: 4rem;
        }
        main {
            width: 100%;
            height: calc(100% - 4rem);
            background-color: lightgray;
            display: flex;
        }
        main > nav {
            flex: 0 1 200px;
            min-width: 150px;
            background: linear-gradient(180deg, black, #333);
        }
        main > nav > ul {
            list-style-type: none;
        }
        main > nav > ul > li {
            margin-top: 0.2rem;
            margin-bottom: 0.2rem;
            height: 3rem;
            line-height: 3rem;
            padding-left: 1rem;
            /* padding-top: 0.5rem; */
            background: linear-gradient(90deg, #111, gray);
        }
        main > nav > ul > li:hover, main > nav > ul > li:active {
            background: linear-gradient(90deg, #444, gray);
        }
        main > nav > ul > li > a {
            text-decoration: none;
            color: white;
        }

        main > article {
            padding: 1rem;
            flex: 1 2 600px;
            background-color: lightyellow;
        }
        main > aside {
            flex: 0 1 200px;
            min-width: 150px;
            background-color: lightpink;
            padding: 1rem;
        }
    </style>
</head>
<body>
    <header>这是标题</header>
    <main>
        <nav>
            <ul>
                <li><a href="#">首页</a></li>
                <li><a href="#">新闻</a></li>
                <li><a href="#">支持</a></li>
                <li><a href="#">关于我们</a></li>
            </ul>
        </nav>
        <article>这是内容</article>
        <aside>这是侧边栏</aside>
    </main>
</body>
</html>
```

# §10 栅格布局

栅格布局更加普适，它基于行和列的二维布局。它的子元素被称为栅格元素。

- 栅格轨道：两条位于栅格边缘且相对着的栅格线之间夹住的整个区域。从栅格容器的一边延伸到对边，即栅格列或栅格行。
- 栅格单元：四条相邻的栅格线围成的矩形区域，且内部没有其它栅格线贯穿。
- 栅格区域：任意四条栅格线围成的矩形区域，由一个或多个栅格单元组成。

## §10.1 栅格容器

开发者可以通过`display`属性声明栅格容器。栅格容器分为两种，一种是常规栅格（`display: grid`），另一种是行内栅格（`display: inline-grid`）。

```html
<html>
<head>
    <style>
        .container {
            background-color: lightgray;
            margin-bottom: 1rem;
        }
        .container > .grid {
            border: 1px solid black;
            width: 100px; 
            height: 50px;
	        grid-template-rows: repeat(2,1fr); 
            grid-template-columns: repeat(2,1fr);
        }
        .container:nth-child(1) > .grid { display: grid; }
        .container:nth-child(2) > .grid { display: inline-grid; }
        .container > .grid > * { border: 1px solid black; }
    </style>
</head>
<body>
    <div class="container">
        This is a <div class="grid"></div> in the middle of this sentence.
    </div>
    <div class="container">
        This is a <div class="grid"></div> in the middle of this sentence.
    </div>
    <script>
        const gridDOMs = document.querySelectorAll('.grid');
        gridDOMs.forEach(element => {
            element.innerHTML = "<span></span><span></span><span></span><span></span>"
        });
    </script>
</body>
</html>
```

之所以不将常规栅格称为"块级栅格"，是因为CSS规范明确指出栅格容器不是块级容器。栅格容器与块级元素这两者之间有以下两点区别：

1. 浮动的元素不会打乱栅格容器。块级元素面对浮动元素时，倾向于在浮动元素的下方开始显示。而栅格元素在垂直方向上见缝插针。
2. 栅格容器的外边距(`margin`)不与其后代的外边距折叠。例如对于块级元素及其子元素而言，两者同时设置`margin-top: 1rem`时，在父元素内部来看，相当于父元素设置了`padding-top: 0`，也就是两个外边距发生的折叠，导致从视觉上来看子元素的顶部紧贴父元素的顶部。而在栅格容器中，这样做从视觉上来看两者顶部间隔的距离就是子元素的`margin-top`值。
3. 栅格容器的所有`column`系列属性都会被忽略。`columns`/`column-count`是CSS3新增的属性。
4. 栅格容器没有`::first-line`和`::first-letter`伪元素，如果使用将会被忽略。
5. 栅格元素（不是栅格容器）上的`float`和`clear`属性将会被忽略。
6. `vertical-align`属性对栅格元素不起作用。
7. 如果栅格元素被`display: float`或`display: absolute`修饰，且栅格容器被为行内栅格（被`display: inline-grid`修饰），则栅格容器自动转变为常规栅格（用`grid`取代`inline-grid`）。

## §10.2 栅格线(`grid-template-rows/columns`)

`grid-template-rows`和`grid-template-columns`用于大致定义栅格模版中的栅格线。这两个属性的语法及其复杂：

```mermaid
flowchart TB
	grid-template["grid-template-rows/grid-template-columns:<br>none|&lt;track-list&gt;|&lt;auto-track-list&gt;"]
	track-list["&lt;track-list>:<br>[&lt;line-names&gt;?[&lt;track-size&gt;|&lt;track-repeat&gt;]]+&lt;line-names&gt;?"]
	auto-track-list["&lt;auto-track-list&gt;<br>[&lt;line-names&gt;?[&lt;fixed-size&gt;|&lt;fixed-repeat&gt;]]*&lt;line-names&gt;?&lt;auto-repeat&gt;[&lt;line-names&gt;?[&lt;fixed-size&gt;|&lt;fixed-repeat&gt;]]*&lt;line-names&gt;?"]
	line-name-list["&lt;line-name-list&gt;:<br>[&lt;line-names&gt;|&lt;name-repeat&gt;]+"]
	line-names["&lt;line-names&gt;:<br>'['&lt;custom-ident&gt;*']'"]
	track-size["&lt;track-size&gt;:<br>&lt;track-breadth&gt;|minmax(&lt;inflexible-breadth&gt;,&lt;track-breadth&gt;)|fit-content(&lt;length-percentage&gt;)"]
	track-repeat["&lt;track-repeat&gt;:<br>repeat([&lt;integer[1,∞]&gt;],[&lt;line-names&gt;?&lt;track-size&gt;]+&lt;line-names&gt;?)"]
	fixed-size["&lt;fixed-size&gt;:<br>&lt;fixed-breadth&gt;|minmax(&lt;fixed-breadth&gt;,&lt;track-breadth&gt;)|minmax(&lt;inflexible-breadth&gt;,&lt;fixed-breadth&gt;)"]
	fixed-repeat["&lt;fixed-repeat&gt;:<br>repeat([&lt;integer[1,∞]&gt;],[&lt;line-names&gt;?&lt;fixed-size&gt;]+&lt;line-names&gt;?)"]
	auto-repeat["&lt;auto-repeat&gt;:<br>repeat([auto-fill|auto-fit],[&lt;line-names&gt;?&lt;fixed-size&gt;]+&lt;line-names&gt;?)"]
	name-repeat["&lt;name-repeat&gt;:<br>repeat([&lt;integer[1,∞]&gt;|auto-fill],&lt;line-names&gt;+)"]
	track-breadth["&lt;track-breadth&gt;:<br>&lt;length-percentage&gt;|&lt;flex&gt;|min-content|max-content|auto"]
	inflexible-breadth["&lt;inflexible-breadth&gt;:<br>&lt;length-percentage&gt;|min-content|max-content|auto"]
	length-percentage["&lt;length-percentage&gt;:<br>&lt;length&gt;|&lt;percentage&gt;"]
	fixed-breadth["&lt;fixed-breadth&gt;:<br>&lt;length-percentage&gt;"]
	
	grid-template --> track-list & auto-track-list & line-name-list
	track-list --> line-names & track-size & track-repeat 
	auto-track-list --> line-names & fixed-size & fixed-repeat & auto-repeat
	line-name-list --> line-names & name-repeat
	line-names
	track-size --> track-breadth & inflexiable-breadth & length-percentage
	track-repeat --> line-names & track-size & line-names
	fixed-size --> fixed-breadth & inflexiable-breadth
	fixed-repeat --> line-names & fixed-size & line-names
	auto-repeat --> line-names & fixed-size & line-names
	name-repeat --> line-names
```

### §10.2.1 宽度固定的栅格轨道

这里所说的“固定”，指的是栅格线之间的距离不受内容的影响。

在下面的例子中，我们创建了一个三列的栅格容器，宽度分别为`200px`、栅格容器宽度的`50%`、`100px`。

```html
<html>
<head>
    <style>
        .grid {
            background-color: lightgray;
            width: 40rem;
            height: 12rem;
            display: grid;
            grid-template-columns: 200px 50% 100px;
        }
        .grid > * {
            background-color: lightblue;
            border: 1px solid black;
            margin-right: 0.1rem;
        }
    </style>
</head>
<body>
    <div class="grid">
        <div>Test</div>
        <div>Test</div>
        <div>Test</div>
    </div>
</body>
</html>
```

我们还可以在此基础上为三列的四条栅格线分别指定名称：

```css
grid-template-columns:
	[first-line second-line] 200px [third-line] 50% [fourth-line] 100px;
```

> 注意：`grid-template-rows`和`grid-template-columns`命名的栅格线名称可以重复，**甚至一个属性内也可以出现重名的具名栅格线**。

除了绝对长度单位，和以栅格容器为基准的相对百分比之外，开发者还可以使用`minmax(...,...)`关键字指定 最小值和最大值。如果最大值小于最小值，那么仅以最小值为基准。

```css
grid-template-columns:
	[fisrt-line second-line] 200px [third-line] calc(100% - 200px - 100px) [fourth-line] 100px;

grid-template-rows:
	[first-line second-line] 200px [third-line] minmax(100px, calc(100% - 200px - 100px)) [fourth-line] 100px;
```

### §10.2.2 弹性栅格轨道(`fr`)

`fr`单位（也成为份数单位）不同于`px`、`vw`这些单位，它代表着一个抽象的长度单元，具体长度随着上下文而改变。

例如把竖向栅格轨道分成四等分，可以写成以下两种等价的形式：

```css
grid-template-columns: 1fr 1fr 1fr 1fr;
grid-template-columns: 25% 25% 25% 25%;
```

如果要分成五等分，`fr`允许直接在后面加`fr`，但是绝对单位和相对单位不行。

```css
grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
grid-template-columns: 25% 25% 25% 25% 25%; /* 错误的写法 */
```


$$
\text{length}_i=\begin{cases}
	\text{track\_size}_i,\textcolor{green}{when\ \text{track\_size}\in\{绝对长度单位\}}
	\\
	\displaystyle\frac{\text{track\_size}_i}{100\%}\times栅格容器尺寸,\textcolor{green}{when\ \text{track\_size}\in\{相对长度单位\}}
	\\
	\displaystyle\frac{\text{track\_size}_i}{
        \displaystyle\sum_{
            \begin{align}
                &i\in[1,n]\in\Z^+,\\
                &\text{track\_size}_i\textcolor{red}{\in}\{fr\}
            \end{align}
        }^{n}{(\text{track\_size}_i)}
	}\times(
		100\%-\displaystyle\sum_{
            \begin{align}
                &i\in[1,n]\in\Z^+,\\
                &\text{track\_size}_i\textcolor{red}{\notin}\{fr\}
            \end{align}
        }^{n}{(\text{track\_size}_i)}
    ), \textcolor{green}{when\ \text{track\_size}_i\in\{fr\}}
\end{cases}
$$

> 注意：`minmax()`的最小值不允许使用`fr`，否则将导致声明失效。

如果在无法确定各部分尺寸的情况下，想要对齐某些部分，可以使用`min-content`和`max-content`关键字。

`max-content`关键字的作用是占据内容所需的最大空间，尽可能减少换行。`min-content`关键词的作用是占据尽可能少的空间，只保证最长的行内元素能在一行内完整显示即可。

```html
<html>
<head>
    <style>
        #grid-1 {
            width: 50rem;
            display: grid;
            grid-template-columns: max-content max-content max-content max-content;
            grid-template-rows: max-content max-content max-content;
            padding-bottom: 1rem;
        }
        #grid-2 {
            width: 50rem;
            display: grid;
            grid-template-columns: 1fr 1fr 1fr 1fr;
            grid-template-rows: 1fr 1fr 1fr;
        }
        span {
            border: 1px solid gray; 
            margin: -1px -1px 0 0;
        }
        img { 
            margin: 0.25em;
            background-color: lightgray;
            width: 40px;
            height: 40px;
        }
        img.wide { width: 90px; height: 60px; }
        img.tall { width: 60px; height: 90px; }
        img.big { width: 90px; height: 90px; }
    </style>
</head>
<body>
    <div id="grid-1">
        <span> <img class=""> </span>
        <span> <img class="wide"> </span>
        <span> <img class=""> </span>
        <span> <img class="tall"> </span>
        <span> <img class=""> </span>
        <span> <img class=""> </span>
        <span> <img class="big"> </span>
        <span> <img class="wide"> </span>
        <span> <img class=""> </span>
        <span> <img class=""> </span>
        <span> <img class=""> </span>
        <span> <img class=""> </span>
    </div>    
    <div id="grid-2">
        <span> <img class=""> </span>
        <span> <img class="wide"> </span>
        <span> <img class=""> </span>
        <span> <img class="tall"> </span>
        <span> <img class=""> </span>
        <span> <img class=""> </span>
        <span> <img class="big"> </span>
        <span> <img class="wide"> </span>
        <span> <img class=""> </span>
        <span> <img class=""> </span>
        <span> <img class=""> </span>
        <span> <img class=""> </span>
    </div>    
    <script>
        const imgDOMs = document.querySelectorAll('img');
        imgDOMs.forEach(element => {
            element.setAttribute("src","https://developer.mozilla.org/static/media/github-mark-small.348586b8904b950b8ea8.svg");
        });
    </script>
</body>
</html>
```

在上面的例子中，分数单位修饰的栅格单元是均匀分布的，而`max-content`修饰的栅格单元是由内部元素的尺寸按需分配，每一行的高度均为这一行内容的最大高度，每一列的宽度均为这一列内容的最大宽度。容易发现，份数单位强调的是占据所有空间且按指定比例分配，而`max-content`强调的是按内容分配，合适即止。

事实上，`max-content`和`min-content`也可以用作`minmax(...,...)`的实参。`max-content`和`minmax(0,max-content)`的区别是：`max-content`只是指定了栅格元素尺寸的行为，要求必须容纳最长的行内元素，因此栅格轨道可能会超出栅格容器规定的尺寸范围(`width`/`height`)。而`minmax(0,max-content)`指定了栅格元素的尺寸范围，要求在不超出弹性容器范围的基础上，尽量向最大值靠拢，因此栅格轨道不会溢出。

```html
<html>
<head>
    <style>
        #grid-1 {
            width: 50rem;
            display: grid;
            grid-template-columns: minmax(1rem, max-content) minmax(1rem, max-content) minmax(1rem, max-content) minmax(1rem, max-content);
            grid-template-rows: minmax(1rem, max-content) minmax(1rem, max-content) minmax(1rem, max-content);
            padding-bottom: 1rem;
        }
        span {
            border: 1px solid gray; 
            margin: -1px -1px 0 0;
        }
        img { 
            margin: 0.25em;
            background-color: lightgray;
            width: 40px;
            height: 40px;
        }
        img.wide { width: 90px; height: 60px; }
        img.tall { width: 60px; height: 90px; }
        img.big { width: 90px; height: 90px; }
    </style>
</head>
<body>
    <div id="grid-1">
        <span> <img class=""> </span>
        <span> <img class="wide"> </span>
        <span> <img class=""> </span>
        <span> <img class="tall"> </span>
        <span> <img class=""> </span>
        <span> <img class=""> </span>
        <span> <img class="big"> </span>
        <span> <img class="wide"> </span>
        <span> <img class=""> </span>
        <span> <img class=""> </span>
        <span> <img class=""> </span>
        <span> <img class=""> </span>
    </div>     
    <script>
        const imgDOMs = document.querySelectorAll('img');
        imgDOMs.forEach(element => {
            element.setAttribute("src","https://developer.mozilla.org/static/media/github-mark-small.348586b8904b950b8ea8.svg");
        });
    </script>
</body>
</html>
```

除此以外，`auto`关键字也可以作为`minmax(...,...)`的实参。当`auto`用于第一个形参时，`minmax(auto,...)`表示的是最小值等于`min-width`/`min-height`属性；当`auto`用于第二个形参时，`minmax(...,auto)`完全等价于`max-content`。

### §10.2.3 根据轨道中的内容适配

在上一节中，我们知道`max-content`和`min-content`是按照轨道中的内容适配的两个极端。要实现更精细的控制，我们可以使用`fit-content()`函数。我们可以认为这是一个语法糖，它只接受一个绝对长度参数或百分数参数，`fit-content(x)`完全等价于`min(max-content, max(min-content, x))`，也就是把`x`限制在`min-content`和`max-content`区间内。

```html
<html>
<head>
    <style>
        #grid {
            width: 50rem;
            display: grid;
            grid-template-columns: fit-content(15rem) fit-content(15rem) fit-content(15rem);
            grid-template-rows: max-content;
            padding-bottom: 1rem;
        }
        span {
            border: 1px solid gray; 
            margin: -1px -1px 0 0;
        }
        img { 
            margin: 0.25em;
            background-color: lightgray;
            width: 40px;
            height: 40px;
        }
        img.wide { width: 90px; height: 60px; }
        img.tall { width: 60px; height: 90px; }
        img.big { width: 90px; height: 90px; }
    </style>
</head>
<body>
    <div id="grid">
        <span>这句话很短。</span>
        <span>这句话的长度适中。</span>
        <span>这句话很长很长很长很长很长很长很长很长很长很长很长很长很长。</span>
    </div>     
    <script>
        const imgDOMs = document.querySelectorAll('img');
        imgDOMs.forEach(element => {
            element.setAttribute("src","https://developer.mozilla.org/static/media/github-mark-small.348586b8904b950b8ea8.svg");
        });
    </script>
</body>
</html>
```

可以看到，在上面的例子中，如果内容宽度小于`min-content`和`fit-content(15rem)`，那么列宽以内容宽度为准；如果内容宽度很长，那么此时`max-content`就是内容宽度，大于`15rem`，所以最终宽度就是`15rem`。

### §10.2.4 重复栅格线

如果各栅格轨道的宽度或高度是一致的，我们可以用`repeat()`批量创建。该函数接受两个形参，第一个是重复次数，第二个是一个或多个栅格轨道的尺寸。

```css
grid-template-columns: repeat(3, 5rem); /* 等价于5rem 5rem 5rem */
grid-template-columns: repeat(2, 3rem 4rem 5rem); /* 等价于3rem 4rem 5rem 3rem 4rem 5rem */
```

`repeat()`也可以创建重名的具名栅格线：

```css
grid-template-columns: repeat(2, 4rem [start] 4rem [end] 4rem);
/* 等价于 [匿名栅格线] 4rem [start] 4rem [end] 4rem [匿名栅格线] 4rem [start] 4rem [end] 4rem [匿名栅格线]  */
```

> 注意：相邻的两条具名栅格线将合并为一条同时具有两个名称的栅格线。
>
> ```css
> /* 以下两种声明完全等价 */
> grid-template-columns: repeat(3, [top] 5rem [bottom]);
> grid-template-columns: [top] 5em [bottom top] [top bottom] 5em [bottom];
> ```

前面提到过`repeat()`的第一个形参是重复次数。除了一切正整数以外，重复次数也可以是`auto-fill`关键字，表示填满为止，称为“自动重复”。自动重复的有以下缺陷：

1. `repeat(auto-fill, ...)`的第二个形参必须是`<line-names>? <fixed-size> <line-names>?`。其中最多出现两次`<line-names>`。

2. `repeat(auto-fill, ...)`的第二个形参如果包含份数单位，则只会重复一次。因为只需`1fr`就能填满所有的空间，不必多个`1fr 1fr ...`。

3. 在单个`grid-template-rows`和`grid-template-columns`属性值最多只能使用一次自动重复。

   ```css
   /* 语法无效 */
   grid-template-columns: repeat(auto-fill, 3rem) repeat(auto-fill, 4rem);
   
   /* 语法有效 */
   grid-template-columns: repeat(5, 3rem) repeat(auto-fill, 4rem);
   grid-template-columns: repeat(auto-fill, 3rem) repeat(5, 4rem);
   ```

除了`auto-fill`外，也可以使用`auto-fit`。它们唯一的区别是：当栅格元素中没有任何内容时，`auto-fit`会剔除该栅格元素对应的栅格轨道，而`auto-fill`不会。

### §10.2.5 自动增加栅格线(`grid-auto-rows/columns`)

`grid-auto-rows`和`grid-auto-columns`分别负责控制自动增加的栅格轨道的尺寸，其属性值可以是一个表示长度的形参，也可以是`minmax(..., ...)`函数。

```html
<html>
<head>
    <style>
        .grid {
            display: grid;
            margin-bottom: 1rem;
            padding: 5px;
            grid-auto-rows: 25px;
            grid-auto-columns: 20px;
            margin-bottom: 2rem;
        }
        .grid:nth-child(1) {
            grid-template-rows: repeat(5, 50px);
            grid-template-columns: repeat(3, 50px);
        }
        .grid:nth-child(2) {
            grid-template-rows: repeat(5, 50px);
            grid-template-columns: repeat(3, 50px);
        }
        .grid > * { border: 1px solid black; }
        .grid:nth-child(1){ grid-auto-flow: row; }
        .grid:nth-child(2){ grid-auto-flow: row dense; }
        .grid:nth-child(3){ grid-auto-flow: column; }
        .grid:nth-child(4){ grid-auto-flow: column dense; }
        .grid > img {
            width: 50px;
            height: 50px;
        }
        .grid > img:nth-child(2) {
            width: 100px; 
            grid-column: auto / span 2;
        }
        .grid > img:nth-child(4) {
            height: 100px; 
            grid-row: auto / span 2;
        }
        .grid > img:nth-child(7) {
            width: 100px; height: 100px; 
            grid-row: auto / span 2;
            grid-column: auto / span 2;
        }
        .grid > img:nth-child(8) {
            width: 100px; 
            grid-column: auto / span 2;
        }
    </style>
</head>
<body>
    grid-auto-flow: row;
    <div class="grid"></div>
    grid-auto-flow: column;
    <div class="grid"></div>
    <script>
        const gridDOMs = document.querySelectorAll('.grid');
        gridDOMs.forEach(element => {
            element.innerHTML = "<img src='https://meyerweb.github.io/csstdg4figs/13-grid-layout/i/yinyang.png'>".repeat(12);
        });
    </script>
</body>
</html>
```

## §10.3 栅格区域

### §10.3.1 定义栅格区域(`grid-template-areas`)

如果说`grid-template-rows`和`grid-template-columns`定义了栅格区域的尺寸，那么`grid-template-areas`定义了栅格区域的形状。

`grid-template-areas`属性值是一群字符串，每个字符串包含着数量相等的标识符，代表这一行的栅格布局。例如在下面的例子中，我们将一个$3\times4$的栅格容器划分为了五个矩形区域：

```css
grid-template-areas:
	"header header header header"
	"leftside content content rightside"
	"leftside footer footer footer";
```

> 注意：`grid-template-areas`定义的栅格区域只能为矩形，否则声明无效。

如果存在不需要合并到任何栅格区域的栅格单元，可以在`grid-template-areas`中使用一个或多个小数点`.`命名：

```css
grid-template-areas:
	"header header header header"
	"leftside content content ."
	"leftside . .. ...";
```

### §10.3.2 使用栅格区域(`grid-area`)

在[§10.4.1 栅格线附加(`grid-row/column`)](###§10.4.1 栅格线附加(`grid-row/column`))一节中，我们知道可以为栅格元素手动指定矩形边界的四条栅格线的位置。本节我们可以给栅格元素附加`grid-area`属性，让CSS根据`grid-template-areas`的布局自动指定栅格线。该属性接受1~4个形参。

当该属性接受一个形参时，表示给栅格容器的子元素的`grid-area`赋予对应的标识符：

```html
<html>
<head>
    <style>
        .grid {
            width: 40rem;
            display: grid;
            grid-template-rows: 2rem 10rem 2rem;
            grid-template-areas: 
                "header header header header"
                "leftside content content rightside"
                "leftside footer footer footer";
        }
        .grid > * {
            border: 1px solid black;
            text-align: center;
            line-height: 2rem;
        }
        .grid > *:nth-child(1) { background-color: lightblue; }
        .grid > *:nth-child(2) { background-color: lightcoral; }
        .grid > *:nth-child(3) { background-color: lightcyan; }
        .grid > *:nth-child(4) { background-color: lightgoldenrodyellow; }
        .grid > *:nth-child(5) { background-color: lightgray; }
    </style>
</head>
<body>
    <div class="grid">
        <div style="grid-area: header;">Header</div>
        <div style="grid-area: leftside;">LeftSide</div>
        <div style="grid-area: content;">Content</div>
        <div style="grid-area: footer;">Footer</div>
        <div style="grid-area: rightside;">RightSide</div>
    </div>
</body>
</html>
```

当该属性接受四个用`/`分割形参时，表示栅格元素使用的四条栅格线，顺序为`grid-row-start`、`grid-column-start`、`grid-row-end`、`grid-column-end`，这是逆时针的顺序，与之前我们接触较多的顺时针顺序相反。如果接受到的形参为2~3个，那么缺少的值必定是结束子元素。CSS将寻找其对应的开始子元素的值，如果恰为栅格线名称，则结束子元素的值等于开始子元素的值，添加`-end`后缀尝试寻找栅格线。如果不是栅格线名称，则变为`auto`。

```html
<html>
<head>
    <style>
        .grid {
            width: 40rem;
            display: grid;
            grid-template-rows: [A] 2rem [B] 10rem [C] 2rem [D];
            grid-template-columns: [A] 8rem [B] 15rem [C] 8rem [D];
            grid-template-areas: 
                "header header header header"
                "leftside content content rightside"
                "leftside footer footer footer";
        }
        .grid > * {
            border: 1px solid black;
            text-align: center;
            line-height: 2rem;
        }
        .grid > *:nth-child(1) { background-color: lightblue; }
        .grid > *:nth-child(2) { background-color: lightcoral; }
        .grid > *:nth-child(3) { background-color: lightcyan; }
        .grid > *:nth-child(4) { background-color: lightgoldenrodyellow; }
        .grid > *:nth-child(5) { background-color: lightgray; }
    </style>
</head>
<body>
    <div class="grid">
        <div style="grid-area: A / A / B / D;">Header</div>
        <div style="grid-area: B / A / D / B;">LeftSide</div>
        <div style="grid-area: B / B / C / C;">Content</div>
        <div style="grid-area: C / B / D / D;">Footer</div>
        <div style="grid-area: B / C / C / D;">RightSide</div>
    </div>
</body>
</html>
```

上面的例子中，我们先定义了`grid-template-areas`，后在`grid-area`中使用了标识符。其实当我们通过`grid-area`属性为栅格区域命名`xxx`时，CSS会自动为栅格区域的首尾两条栅格线命名`xxx-start`和`xxx-end`。这种命名机制称为“隐式命名机制”。这也正是给子元素赋值`grid-area`属性就能确定栅格区域的原理。利用这一原理，我们可以反向在`grid-template-rows`和`grid-template-columns`中只给栅格线赋名，而不使用`grid-template-areas`，就能定义栅格区域：

```html
<html>
<head>
    <style>
        .grid {
            width: 40rem;
            display: grid;
            grid-template-rows: [header-start] 2rem [header-end leftside-start content-start rightside-start] 10rem [content-end rightside-end footer-start] 2rem [leftside-end footer-end];
            grid-template-columns: [header-start leftside-start] 10rem [leftside-end content-start footer-start] 20rem [content-end rightside-start] 10rem [header-end rightside-end footer-end] ;
        }
        .grid > * {
            border: 1px solid black;
            text-align: center;
            line-height: 2rem;
        }
        .grid > *:nth-child(1) { background-color: lightblue; }
        .grid > *:nth-child(2) { background-color: lightcoral; }
        .grid > *:nth-child(3) { background-color: lightcyan; }
        .grid > *:nth-child(4) { background-color: lightgoldenrodyellow; }
        .grid > *:nth-child(5) { background-color: lightgray; }
    </style>
</head>
<body>
    <div class="grid">
        <div style="grid-area: header;">Header</div>
        <div style="grid-area: leftside;">LeftSide</div>
        <div style="grid-area: content;">Content</div>
        <div style="grid-area: footer;">Footer</div>
        <div style="grid-area: rightside;">RightSide</div>
    </div>
</body>
</html>
```

在上例中，我们指定了每一个栅格区域的行与列的全部四条具名栅格线的位置。如果行与列中有一个定义了两行，另一个没定义，那么该栅格区域将会被放置在后者的最后一行/列。

## §10.4 栅格元素

当我们定义好栅格线和栅格区域后，我们就可以给栅格容器填充栅格元素了。接下来我们把栅格元素附加到栅格的网架中。

### §10.4.1 附加栅格线(`grid-row/column`)

`grid-row-start`、`grid-row-end`、`grid-column-start`、`grid-column-end`这四个属性作用于栅格元素，表示该元素的四条边界分别附加到哪个栅格线上。

1. 当这四个属性值为**正整数**时，表示四条边界附着到的栅格线的序号（从`1`开始计数）：

   ```html
   <html>
   <head>
       <style>
           .grid {
               width: 40rem;
               display: grid;
               grid-template-rows: 2rem 10rem 2rem;
               grid-template-columns: 10rem 20rem 10rem;
           }
           .grid > * {
               border: 1px solid black;
               text-align: center;
               line-height: 2rem;
           }
           .grid > *:nth-child(1) { 
               grid-row-start: 1;
               grid-row-end: 2;
               grid-column-start: 1;
               grid-column-end: 4;
               background-color: lightblue; 
           }
           .grid > *:nth-child(2) { 
               grid-row-start: 2;
               grid-row-end: 4;
               grid-column-start: 1;
               grid-column-end: 2;
               background-color: lightcoral; 
           }
           .grid > *:nth-child(3) { 
               grid-row-start: 2;
               grid-row-end: 3;
               grid-column-start: 2;
               grid-column-end: 3;
               background-color: lightcyan; 
           }
           .grid > *:nth-child(4) { 
               grid-row-start: 3;
               grid-row-end: 4;
               grid-column-start: 2;
               grid-column-end: 4;
               background-color: lightgoldenrodyellow; 
           }
           .grid > *:nth-child(5) { 
               grid-row-start: 2;
               grid-row-end: 3;
               grid-column-start: 3;
               grid-column-end: 4;
               background-color: lightgray; 
           }
       </style>
   </head>
   <body>
       <div class="grid">
           <div>Header</div>
           <div>LeftSide</div>
           <div>Content</div>
           <div>Footer</div>
           <div>RightSide</div>
       </div>
   </body>
   </html>
   ```

2. 当这四个属性值为`span <integer>`时，表示跨越指定数量的栅格轨道确定的栅格线。如果`span`关键字用于`...start`属性，则表示从`...end`确定的栅格线向起边往回数`<integer>`个栅格轨道；如果`span`关键字用于`...end`属性，则表示从`...start`确定的栅格线向终边往前数`<integer>`个栅格轨道。

   ```html
   <html>
   <head>
       <style>
           .grid {
               width: 40rem;
               display: grid;
               grid-template-rows: 2rem 10rem 2rem;
               grid-template-columns: 10rem 20rem 10rem;
           }
           .grid > * {
               border: 1px solid black;
               text-align: center;
               line-height: 2rem;
           }
           .grid > *:nth-child(1) { 
               grid-row-start: 1;
               grid-row-end: span 1;
               grid-column-start: 1;
               grid-column-end: span 3;
               background-color: lightblue; 
           }
           .grid > *:nth-child(2) { 
               grid-row-start: 2;
               grid-row-end: span 2;
               grid-column-start: span 1;
               grid-column-end: 2;
               background-color: lightcoral; 
           }
           .grid > *:nth-child(3) { 
               grid-row-start: span 1;
               grid-row-end: 3;
               grid-column-start: 2;
               grid-column-end: span 1;
               background-color: lightcyan; 
           }
           .grid > *:nth-child(4) { 
               grid-row-start: span 1;
               grid-row-end: 4;
               grid-column-start: 2;
               grid-column-end: span 2;
               background-color: lightgoldenrodyellow; 
           }
           .grid > *:nth-child(5) { 
               grid-row-start: 2;
               grid-row-end: 3;
               grid-column-start: 3;
               grid-column-end: 4;
               background-color: lightgray; 
           }
       </style>
   </head>
   <body>
       <div class="grid">
           <div>Header</div>
           <div>LeftSide</div>
           <div>Content</div>
           <div>Footer</div>
           <div>RightSide</div>
       </div>
   </body>
   </html>
   ```

3. 当这四个属性值为**负整数**时，表示四条边界附着到的栅格线的序号（从`-1`开始从终边向起边反向计数）：

   ```html
   <html>
   <head>
       <style>
           .grid {
               width: 40rem;
               display: grid;
               grid-template-rows: 2rem 10rem 2rem;
               grid-template-columns: 10rem 20rem 10rem;
           }
           .grid > * {
               border: 1px solid black;
               text-align: center;
               line-height: 2rem;
           }
           .grid > *:nth-child(1) { 
               grid-row-start: -4;
               grid-row-end: -3;
               grid-column-start: -4;
               grid-column-end: -1;
               background-color: lightblue; 
           }
           .grid > *:nth-child(2) { 
               grid-row-start: -3;
               grid-row-end: -1;
               grid-column-start: -4;
               grid-column-end: -3;
               background-color: lightcoral; 
           }
           .grid > *:nth-child(3) { 
               grid-row-start: -3;
               grid-row-end: -2;
               grid-column-start: -3;
               grid-column-end: -2;
               background-color: lightcyan; 
           }
           .grid > *:nth-child(4) { 
               grid-row-start: -2;
               grid-row-end: -1;
               grid-column-start: -3;
               grid-column-end: -1;
               background-color: lightgoldenrodyellow; 
           }
           .grid > *:nth-child(5) { 
               grid-row-start: -3;
               grid-row-end: -2;
               grid-column-start: -2;
               grid-column-end: -1;
               background-color: lightgray; 
           }
       </style>
   </head>
   <body>
       <div class="grid">
           <div>Header</div>
           <div>LeftSide</div>
           <div>Content</div>
           <div>Footer</div>
           <div>RightSide</div>
       </div>
   </body>
   </html>
   ```

4. 当这四个属性值为具名栅格线的名称时，表示四条边界附着到的栅格线为这条栅格线。

   ```html
   <html>
   <head>
       <style>
           .grid {
               width: 40rem;
               display: grid;
               grid-template-rows: [A] 2rem [B] 10rem [C] 2rem [D];
               grid-template-columns: [A] 10rem [B] 20rem [C] 10rem [D];
           }
           .grid > * {
               border: 1px solid black;
               text-align: center;
               line-height: 2rem;
           }
           .grid > *:nth-child(1) { 
               grid-row-start: A;
               grid-row-end: B;
               grid-column-start: A;
               grid-column-end: D;
               background-color: lightblue; 
           }
           .grid > *:nth-child(2) { 
               grid-row-start: B;
               grid-row-end: D;
               grid-column-start: A;
               grid-column-end: B;
               background-color: lightcoral; 
           }
           .grid > *:nth-child(3) { 
               grid-row-start: B;
               grid-row-end: C;
               grid-column-start: B;
               grid-column-end: C;
               background-color: lightcyan; 
           }
           .grid > *:nth-child(4) { 
               grid-row-start: C;
               grid-row-end: D;
               grid-column-start: B;
               grid-column-end: D;
               background-color: lightgoldenrodyellow; 
           }
           .grid > *:nth-child(5) { 
               grid-row-start: B;
               grid-row-end: C;
               grid-column-start: C;
               grid-column-end: D;
               background-color: lightgray; 
           }
       </style>
   </head>
   <body>
       <div class="grid">
           <div>Header</div>
           <div>LeftSide</div>
           <div>Content</div>
           <div>Footer</div>
           <div>RightSide</div>
       </div>
   </body>
   </html>
   ```

5. 当存在多条重名的具名栅格线时，可以使用`<custom-ident> <integer>`表示选择这一行/列中众多具有`<custom-ident>`的第`<integer>`条栅格线。这个`<integer>`也可以是负数。

   同理，`span <integer> <custom-ident>`表示选择再往前/后数的第`<integer>`条名为`<custom-ident>`的具名栅格线。

   ```html
   <html>
   <head>
       <style>
           .grid {
               width: 40rem;
               display: grid;
               grid-template-rows: [A] 2rem [A] 10rem [A] 2rem [A];
               grid-template-columns: [A] 10rem [A] 20rem [A] 10rem [A];
           }
           .grid > * {
               border: 1px solid black;
               text-align: center;
               line-height: 2rem;
           }
           .grid > *:nth-child(1) { 
               grid-row-start: A 1;
               grid-row-end: span 1 A;
               grid-column-start: A 1;
               grid-column-end: A -1;
               background-color: lightblue; 
           }
           .grid > *:nth-child(2) { 
               grid-row-start: A 2;
               grid-row-end: A -1;
               grid-column-start: A 1;
               grid-column-end: A 2;
               background-color: lightcoral; 
           }
           .grid > *:nth-child(3) { 
               grid-row-start: A 2;
               grid-row-end: A 3;
               grid-column-start: A 2;
               grid-column-end: A 3;
               background-color: lightcyan; 
           }
           .grid > *:nth-child(4) { 
               grid-row-start: A 3;
               grid-row-end: A 4;
               grid-column-start: A 2;
               grid-column-end: A 4;
               background-color: lightgoldenrodyellow; 
           }
           .grid > *:nth-child(5) { 
               grid-row-start: A 2;
               grid-row-end: A 3;
               grid-column-start: A 3;
               grid-column-end: A 4;
               background-color: lightgray; 
           }
       </style>
   </head>
   <body>
       <div class="grid">
           <div>Header</div>
           <div>LeftSide</div>
           <div>Content</div>
           <div>Footer</div>
           <div>RightSide</div>
       </div>
   </body>
   </html>
   ```

6. 在使用标识符声明`grid-template-areas`时，CSS就已经根据创建的矩形栅格区域隐式创建了栅格线名称。我们可以直接使用这个栅格线名称作为这四个属性的属性值：

   ```html
   <html>
   <head>
       <style>
           .grid {
               width: 40rem;
               display: grid;
               grid-template-rows: 2rem 10rem 2rem;
               grid-template-areas: 
                   "header header header header"
                   "leftside content content rightside"
                   "leftside footer footer footer";
           }
           .grid > * {
               border: 1px solid black;
               text-align: center;
               line-height: 2rem;
           }
           .grid > *:nth-child(1) { 
               grid-row-start: header;
               grid-row-end: header;
               grid-column-start: header;
               grid-column-end: header;
               background-color: lightblue; }
           .grid > *:nth-child(2) { 
               grid-row-start: leftside;
               grid-row-end: leftside;
               grid-column-start: leftside;
               grid-column-end: leftside;
               background-color: lightcoral; }
           .grid > *:nth-child(3) { 
               grid-row-start: content;
               grid-row-end: content;
               grid-column-start: content;
               grid-column-end: content;
               background-color: lightcyan; }
           .grid > *:nth-child(4) { 
               grid-row-start: footer;
               grid-row-end: footer;
               grid-column-start: footer;
               grid-column-end: footer;
               background-color: lightgoldenrodyellow; }
           .grid > *:nth-child(5) { 
               grid-row-start: rightside;
               grid-row-end: rightside;
               grid-column-start: rightside;
               grid-column-end: rightside;
               background-color: lightgray; }
       </style>
   </head>
   <body>
       <div class="grid">
           <div>Header</div>
           <div>LeftSide</div>
           <div>Content</div>
           <div>Footer</div>
           <div>RightSide</div>
       </div>
   </body>
   </html>
   ```

   > 注意：当这四个属性遇到标识符时，会寻找该标识符对应和栅格线，也会尝试在后面添加`-start`/`-end`（取决于属性是开始属性还是结束属性）再开始寻找。因此在上面的例子中，以下两种写法等效：
   >
   > ```css
   > grid-row-start: rightside;
   > grid-row-start: rightside-start;
   > ```

我们已经分别介绍了行与列各自的两个子属性。它们可以分别简写为一个子属性：`grid-row`和`grid-column`，接受一到两个参数。

当`grid-row`和`grid-column`接受到两个由`/`分隔的子属性值时，它们分别表示开始子属性与结束子属性。如果只接受到一个参数，那么该参数表示开始子属性，而结束子属性的值为`auto`——如果第一个参数是整数，那么`auto`表示该整数加一；如果第一个参数是标识符，那么CSS会尝试在标识符后面添加`-end`寻找对应的栅格线。

```html
<html>
<head>
    <style>
        .grid {
            width: 40rem;
            display: grid;
            grid-template-rows: 2rem 10rem 2rem;
            grid-template-areas: 
                "header header header header"
                "leftside content content rightside"
                "leftside footer footer footer";
        }
        .grid > * {
            border: 1px solid black;
            text-align: center;
            line-height: 2rem;
        }
        .grid > *:nth-child(1) { 
            grid-row: header; /* 等价于header / header */
            grid-column: header / header;
            background-color: lightblue; }
        .grid > *:nth-child(2) { 
            grid-row: 2 / 4;
            grid-column: 1 / 2;
            background-color: lightcoral; }
        .grid > *:nth-child(3) { 
            grid-row: 2 / span 1;
            grid-column: 2 / span 2;
            background-color: lightcyan; }
        .grid > *:nth-child(4) { 
            grid-row: span 1 / 4;
            grid-column: span 3 / 5;
            background-color: lightgoldenrodyellow; }
        .grid > *:nth-child(5) { 
            grid-row: 2 / 3;
            grid-column: -2 / -1;
            background-color: lightgray; }
    </style>
</head>
<body>
    <div class="grid">
        <div>Header</div>
        <div>LeftSide</div>
        <div>Content</div>
        <div>Footer</div>
        <div>RightSide</div>
    </div>
</body>
</html>
```

我们已经知道了如何使用`grid-template-rows`/`grid-template-columns`或`grid-template-areas`定义栅格轨道。但是如果定义栅格区域时，使用的栅格线超出了栅格轨道，那么CSS就会为其创建***隐式栅格**。
$$
\text{起始栅格线编号}=\begin{cases}
	\text{grid-...-start}&,\text{grid-...-start}\ge 1\notin\{\text{span}\}
	\\
	N+\text{grid-...-start}+1&,\text{grid-...-start}\le-1
	\\
	\text{grid-...-start}-\text{span}&,\text{grid-...-start}\in\left[1,N\right]\in\{\text{span}\}
	\\
	\textcolor{red}{N-\text{span}} &,\textcolor{red}{\text{grid-...-end}>N\in\{\text{span}\}}
\end{cases}
\\
\text{终止栅格线编号}=\begin{cases}
	\text{grid-...-end}&,\text{grid-...-end}\ge 1\notin\{\text{span}\}
	\\
	N+\text{grid-...-end}+1&,\text{grid-...-end}\le-1
	\\
	\text{grid-...-start}+\text{span}&,\text{grid-...-start}\in\left[1,N\right]\in\{\text{span}\}
\end{cases}
$$
上面的公式有两点需要注意：

1. 如果引用的栅格线序号大于范围，则可以直接引用，也可以使用`span`；如果小于范围，则不能直接使用负数引用，因为负数计数的起点是栅格轨道内该方向的最后一条不超出范围的栅格线，所以只能用`span`。
2. 当$\textcolor{red}{\text{grid-...-end}>N\in\{\text{span}\}}$时，计算起始栅格线编号时，`span`采用的起点是`min(grid-...-end, N)`，而不是单纯的`grid-...-end`。

```html
<html>
<head>
    <style>
        .grid > span {
            border: 1px gray dotted;
        }
        .grid > *[class^="box"] {
            border: 5px solid;
            font-weight: bold;
            font-size: larger;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .grid > *:nth-child(1) { grid-column: 1; grid-row: 1 / 4; color: rgb(255, 0, 0); }
        .grid > *:nth-child(2) { grid-column: 2; grid-row: 3 / span 2; color: rgb(255, 128, 0); }
        .grid > *:nth-child(3) { grid-column: 3; grid-row: span 2 / 3; color: rgb(216, 168, 0); }
        .grid > *:nth-child(4) { grid-column: 4; grid-row: span 4 / 5; color: rgb(0, 128, 0); }
        .grid > *:nth-child(5) { grid-column: 5; grid-row: span 6 / 5; color: rgb(0, 0, 255); }
        .grid > *:nth-child(6) { grid-column: 6; grid-row: -1 / span 3; color: rgb(128, 0, 128); }
        .grid > *:nth-child(7) { grid-column: 7; grid-row: span 3 / -1; color: rgb(0, 0, 0); }
        .grid {
            display: grid;
            grid-auto-rows: 2em;
            grid-auto-columns: 5em;
            grid-template-rows: repeat(2, 2rem);
            grid-template-columns: repeat(6, 5rem);
        }
        .grid > .explicit {
            background: rgba(0, 0, 0, 0.1);
            grid-area: 1 / 1 / 3 / 7;
        }
    </style>
</head>
<body>
    <div class="grid">
    </div>
    <script>
        
        function getSpanHTML(className, content){
            if(!className instanceof String){
                return "<span></span>"
            }
            if(!content instanceof String || content === undefined){
                return "<span></span>"
            }
            return `<span class="${className}">${content}</span>`;
        }

        const gridDOMs = document.querySelectorAll('.grid');
        gridDOMs.forEach(element => {
            let gridHTML = "";
            gridHTML += getSpanHTML("box1", 1);
            gridHTML += getSpanHTML("box2", 2);
            gridHTML += getSpanHTML("box3", 3);
            gridHTML += getSpanHTML("box4", 4);
            gridHTML += getSpanHTML("box5", 5);
            gridHTML += getSpanHTML("box6", 6);
            gridHTML += getSpanHTML("box7", 7);
            gridHTML += getSpanHTML("explicit");
            gridHTML += getSpanHTML("").repeat(25);
            element.innerHTML = gridHTML;
        });

    </script>
</body>
</html>
```

前面我们提到了`grid-row/column-start/end`的语法。如果给予不符合语法的属性值，CSS不会一概认为声明无效，而是尝试修复。

1. 如果`grid-...-start > grid-...-start`（结束在前，开始在后），则两个属性值对调。
2. 如果`grid-...-start`和`grid-...-start`都包含`span`，则开始子属性的属性值得到保留，例如`span`视为`span 1`。结束子属性的属性值被抛弃，替换为`auto`，取决于元素边界所在的栅格流。
3. 如果`grid-...-start`或`grid-...-start`出现了`span <custom-ident>`，缺少了后继的`<integer>`，CSS会将其自动替换为`span 1`。

默认情况下，一个栅格元素只占用一个栅格单元。即使强行改变栅格元素的尺寸，也不会超出一个栅格单元的范围：

```html
<html>
<head>
    <style>
        .grid {
            display: grid;
            width: 20rem;
            height: 8rem;
            grid-template-rows: repeat(3, 50px);
            grid-template-columns: repeat(4, 50px);
        }
        .grid > * { border: 1px solid black; }
        .grid > img {
            width: 50px;
            height: 50px;
        }
        .grid > img:nth-child(2) { width: 100px; }
        .grid > img:nth-child(4) { height: 100px; }
        .grid > img:nth-child(7) { width: 100px; height: 100px; }
        .grid > img:nth-child(8) { width: 100px; }
    </style>
</head>
<body>
    <div class="grid"></div>
    <script>
        const gridDOMs = document.querySelectorAll('.grid');
        gridDOMs.forEach(element => {
            element.innerHTML = "<img src='https://meyerweb.github.io/csstdg4figs/13-grid-layout/i/yinyang.png'>".repeat(12);
        });
    </script>
</body>
</html>
```

如果要让一个栅格元素横跨多个栅格单元，可以考虑使用`grid-row/column`声明栅格元素的起始位置。至于横跨多个栅格单元之后的排版，可参考[§10.5 栅格流(`grid-auto-flow`)](##§10.5 栅格流(`grid-auto-flow`))一节：

```html
<html>
<head>
    <style>
        .grid {
            display: grid;
            grid-template-rows: repeat(3, 50px);
            grid-template-columns: repeat(4, 50px);
        }
        .grid > * { border: 1px solid black; }
        /* .grid:nth-child(1){ grid-auto-flow: row; } */
        /* .grid:nth-child(2){ grid-auto-flow: row dense; } */
        /* .grid:nth-child(3){ grid-auto-flow: column; } */
        /* .grid:nth-child(4){ grid-auto-flow: column dense; } */
        .grid > img {
            width: 50px;
            height: 50px;
        }
        .grid > img:nth-child(2) {
            width: 100px; 
            grid-column: auto / span 2;
        }
        .grid > img:nth-child(4) {
            height: 100px; 
            grid-row: auto / span 2;
        }
        .grid > img:nth-child(7) {
            width: 100px; height: 100px; 
            grid-row: auto / span 2;
            grid-column: auto / span 2;
        }
        .grid > img:nth-child(8) {
            width: 100px; 
            grid-column: auto / span 2;
        }
    </style>
</head>
<body>
    <div class="grid"></div>
    <script>
        const gridDOMs = document.querySelectorAll('.grid');
        gridDOMs.forEach(element => {
            element.innerHTML = "<img src='https://meyerweb.github.io/csstdg4figs/13-grid-layout/i/yinyang.png'>".repeat(12);
        });
    </script>
</body>
</html>
```

### §10.4.2 栅格分层(`order`)

当多个栅格元素占用了同一个栅格单元时，会导致栅格元素重叠。`z-index`较大栅格元素（包括背景、边框）在最上层。如果`z-index`相同，则排在源代码后面的在最上层。

栅格布局提供了一种新的方式：`order`属性。将该属性附在栅格元素上，就能改变**渲染顺序**。`order`属性值越大，渲染顺序就越靠后，等价于图层就越靠上。

`z-index`与`order`的区别是：`z-index`仅影响栅格元素的图层，不影响其位置。而`order`较大的元素，可能本应被自动安排到某位置，但是由于它渲染顺序靠后，那么该位置就会被`order`较小的栅格元素所抢占，因此`order`较大的栅格元素的位置有可能会靠后。

## §10.5 栅格流(`grid-auto-flow`)

如果不明确指定栅格元素在栅格容器中的位置，那么栅格元素将会自动放入栅格容器中，具体的位置受栅格流的影响，放入第一个适合的区域中。

栅格流分为两种模式——行优先和列优先，通过`grid-auto-flow`属性设置。

| `grid-auto-flow`属性值 | 作用         |
| ---------------------- | ------------ |
| `row`(缺省)            | 行优先       |
| `row dense`            | 行优先密集流 |
| `column`               | 列优先       |
| `column dense`         | 列优先密集流 |

在之前的实践中，容易发现默认的行优先会将栅格元素按照从左到右、从上到下的顺序填充。而列优先会按照从上到下、从左到右的顺序填充：

```html
<html>
<head>
    <style>
        .grid {
            display: grid;
            width: 20rem;
            height: 8rem;
            grid-template-rows: repeat(3, 2rem);
            grid-template-columns: repeat(2, 4rem);
        }
        .grid > * {
            border: 1px solid black;
            text-align: center;
            line-height: 2rem;
        }
        .grid:nth-child(1){ grid-auto-flow: row; }
        .grid:nth-child(2){ grid-auto-flow: row dense; }
        .grid:nth-child(3){ grid-auto-flow: column; }
        .grid:nth-child(4){ grid-auto-flow: column dense; }
    </style>
</head>
<body>
    <div class="grid"></div>
    <div class="grid"></div>
    <div class="grid"></div>
    <div class="grid"></div>
    <script>
        const gridDOMs = document.querySelectorAll('.grid');
        gridDOMs.forEach(element => {
            element.innerHTML = "<div>1</div><div>2</div><div>3</div><div>4</div><div>5</div>";
        });
    </script>
</body>
</html>
```

密集流的区别在于，它会想尽一切办法填充栅格流中的空白区域。对于非密集流而言，CSS安排好上一个栅格元素后，会从**这个位置**起向后搜索空余位置；对于密集流而言，CSS安排好上一个元素后，会从**初始位置**起向后搜索，确保利用每一个违背占用的栅格单元。
$$
非密集流:=\begin{cases}
	\textcolor{red}{栅格元素序号k:=1;}\\
	\text{foreach}(i\in[1,M])\{ \\
		\ \ \ \ \text{forearch}(j\in[1,N])\{\\
			\ \ \ \ \ \ \ \ \text{if}(元素k需要的栅格单元均未被占用)\{\\
			\ \ \ \ \ \ \ \ \ \ \ \ 占用所需的单元格;\\
			\ \ \ \ \ \ \ \ \ \ \ \ \textcolor{red}{k\text{++};}\\
			\ \ \ \ \ \ \ \ \}\text{else}\\
			\ \ \ \ \ \ \ \ \ \ \ \ \text{continue};\\
			\ \ \ \ \ \ \ \ \}\\
		\ \ \ \ \}\\
	\}
\end{cases}
,
密集流:=\begin{cases}
	\textcolor{red}{\text{foreach}(栅格元素序号k\in[1,K])\{}\\
		\ \ \ \ \text{foreach}(i\in[1,M])\{ \\
		\ \ \ \ \ \ \ \ \text{forearch}(j\in[1,N])\{\\
			\ \ \ \ \ \ \ \ \ \ \ \  \text{if}(元素k需要的栅格单元均未被占用)\{\\
			\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ 占用所需的单元格;\\
			\ \ \ \ \ \ \ \ \ \ \ \ \}\text{else}\\
			\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \text{continue};\\
			\ \ \ \ \ \ \ \ \ \ \ \ \}\\
		\ \ \ \ \ \ \ \ \}\\
		\ \ \ \ \}\\
	\}
\end{cases}
$$

```html
<html>
<head>
    <style>
        .grid {
            display: grid;
            margin-bottom: 1rem;
            padding: 5px;
        }
        .grid:nth-child(1), .grid:nth-child(2) {
            grid-template-rows: repeat(6, 50px);
            grid-template-columns: repeat(4, 50px);
        }
        .grid:nth-child(3), .grid:nth-child(4) {
            grid-template-rows: repeat(4, 50px);
            grid-template-columns: repeat(6, 50px);
        }
        .grid > * { border: 1px solid black; }
        .grid:nth-child(1){ grid-auto-flow: row; }
        .grid:nth-child(2){ grid-auto-flow: row dense; }
        .grid:nth-child(3){ grid-auto-flow: column; }
        .grid:nth-child(4){ grid-auto-flow: column dense; }
        .grid > img {
            width: 50px;
            height: 50px;
        }
        .grid > img:nth-child(2) {
            width: 100px; 
            grid-column: auto / span 2;
        }
        .grid > img:nth-child(4) {
            height: 100px; 
            grid-row: auto / span 2;
        }
        .grid > img:nth-child(7) {
            width: 100px; height: 100px; 
            grid-row: auto / span 2;
            grid-column: auto / span 2;
        }
        .grid > img:nth-child(8) {
            width: 100px; 
            grid-column: auto / span 2;
        }
    </style>
</head>
<body>
    grid-auto-flow: row;
    <div class="grid"></div>
    grid-auto-flow: row dense;
    <div class="grid"></div>
    grid-auto-flow: column;
    <div class="grid"></div>
    grid-auto-flow: column dense;
    <div class="grid"></div>
    <script>
        const gridDOMs = document.querySelectorAll('.grid');
        gridDOMs.forEach(element => {
            element.innerHTML = "<img src='https://meyerweb.github.io/csstdg4figs/13-grid-layout/i/yinyang.png'>".repeat(12);
        });
    </script>
</body>
</html>
```

## §10.6 栏距(`grid-gap`)

栏距是两个栅格轨道之间的间隔。一个轴上只能设定一个间隔值，使用`grid-gap`属性指定，其属性值由两个长度决定——分别为行栏距和列栏距，只需空格分隔。

> 注意：`grid-row-gap`和`grid-column-gap`已被弃用，而且已从MDN文档中移除。虽然Chromium依然支持，但还是应该避免使用。

```html
<html>
<head>
    <style>
        .grid {
            display: grid;
            grid-template-rows: repeat(4, 3rem);
            grid-template-columns: repeat(4, 3rem);
            grid-gap: 5px 20px;
        }
        .grid > * {
            border: 1px solid black;
            text-align: center;
            line-height: 3rem;
            font-size: x-large;
        }
    </style>
</head>
<body>
    <div class="grid"></div>
    <script>
        const gridDOMs = document.querySelectorAll('.grid');
        gridDOMs.forEach((element, key) => {
            let elementHTML = "";
            for (let i = 0 ; i < 16 ; i++) {
                let redCode = Math.floor((1 + Math.sin(2 * Math.PI * i / 16)) / 2 * 255).toString();
                let greenCode = Math.floor((1 + Math.sin(2 * Math.PI * i / 16 + Math.PI * 2 / 3)) / 2 * 255).toString();
                let blueCode = Math.floor((1 + Math.sin(2 * Math.PI * i / 16 + Math.PI * 4 / 3)) / 2 * 255).toString();
                console.log(redCode);
                elementHTML += `<span style="background: rgba(${redCode}, ${greenCode}, ${blueCode}, 0.5);">${i}</span>`;
            }
            element.innerHTML = elementHTML;
        });
    </script>
</body>
</html>
```

## §10.7 栅格的对齐方式

栅格布局也提供了完善的栅格对齐方案。使用的属性与[§9 弹性盒布局](#§9 弹性盒布局)基本一致。

| 属性名称          | 控制哪些元素的对齐行为         | 应用于   |
| ----------------- | ------------------------------ | -------- |
| `justify-self`    | 行内方向(横向)上的一个栅格元素 | 栅格元素 |
| `justify-items`   | 行内方向(横向)上的全部栅格元素 | 栅格容器 |
| `justify-content` | 行内方向(横向)上的整个栅格     | 栅格容器 |
| `align-self`      | 块级方向(纵向)上的一个栅格元素 | 栅格元素 |
| `align-items`     | 块级方向(纵向)上的全部栅格元素 | 栅格容器 |
| `align-content`   | 块级方向(纵向)上的整个栅格     | 栅格容器 |

### §10.7.1 对齐单个元素(`justify-self`/`align-self`)

让栅格元素内部使用`display: flex`布局，就可以使用`justify-self`和`align-self`分别控制水平和垂直方向的对齐行为。

| `justify-self`/`align-self`的属性值 | 在`justify-self`的作用                              | 在`align-self`的作用                                         |
| ----------------------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
| `start`                             | 向栅格区域的起边(左边)对齐                          | 向栅格区域的起边(上边)对齐                                   |
| `end`                               | 向栅格区域的终边(右边)对齐                          | 向栅格区域的终边(下边)对齐                                   |
| `center`                            | 向栅格区域的水平中心对齐                            | 向栅格区域的水平中心对齐                                     |
| `left`                              | 等价于`start`                                       | 等价于`start`                                                |
| `right`                             | 等价于`end`                                         | 注意！**等价于`start`**！                                    |
| `self-start`                        | 受`<div dir="...">`的影响，向书写方向起点的一侧对齐 | 不受`<div dir="...">`的影响，等价于`start`                   |
| `self-end`                          | 受`<div dir="...">`的影响，向书写方向终点的一侧对齐 | 不受`<div dir="...">`的影响，等价于`end`                     |
| `stretch`                           | 拉伸元素，同时向`start`和`end`对齐                  | 拉伸元素，同时向`start`和`end`对齐                           |
| `baseline`                          |                                                     | 将该栅格元素的第一条基线，与同水平栅格轨道中最高的栅格元素的第一条基线对齐 |
| `last-baseline`                     |                                                     | 将该栅格元素的最后一条基线，与同水平栅格轨道中最高的栅格元素的最后一条基线对齐 |
| `flex-start`                        | 仅在弹性盒布局中有意义，在栅格布局中等价于`start`   | 仅在弹性盒布局中有意义，在栅格布局中等价于`start`            |
| `flex-end`                          | 仅在弹性盒布局中有意义，在栅格布局中等价于`end`     | 仅在弹性盒布局中有意义，在栅格布局中等价于`end`              |

```html
<html>
<head>
    <style type="text/css">
        body { padding: 1rem; }

        .grid {
            display: grid;
            padding: 0.5em;
            grid-gap: 0.75em 0.5em;
            grid-template-rows: 6em;
            grid-template-columns: repeat(10, 9em);
        }

        .grid > *[class^="box"] {
            font-size: 1em;
            padding: 0.25em;
            border: 0.2rem solid;

            display: flex; /* 让文字居中 */
            align-items: center;
            justify-content: center;
        }

        .grid:nth-child(1) [class*="box"] { align-self: center; }
        .grid:nth-child(1) .box1 { justify-self: start; }
        .grid:nth-child(1) .box2 { justify-self: end; }
        .grid:nth-child(1) .box3 { justify-self: center; }
        .grid:nth-child(1) .box4 { justify-self: left; }
        .grid:nth-child(1) .box5 { justify-self: right; }
        .grid:nth-child(1) .box6 { justify-self: self-start; direction: rtl; }
        .grid:nth-child(1) .box7 { justify-self: self-end; direction: rtl; }
        .grid:nth-child(1) .box8 { justify-self: stretch; }
        
        .grid:nth-child(2) [class*="box"] { justify-self: center; }
        .grid:nth-child(2) .box1 { align-self: start; }
        .grid:nth-child(2) .box2 { align-self: end; }
        .grid:nth-child(2) .box3 { align-self: center; }
        .grid:nth-child(2) .box4 { align-self: left; }
        .grid:nth-child(2) .box5 { align-self: right; }
        .grid:nth-child(2) .box6 { align-self: self-start; direction: rtl; }
        .grid:nth-child(2) .box7 { align-self: self-end; direction: rtl; }
        .grid:nth-child(2) .box8 { align-self: stretch; }

        /* 绘制栅格元素的边框 */
        .grid > *[class*="gridline"] {
            border: 1px dashed blue;
        }

        .grid > *[class*="1"] { grid-row: 1; grid-column: 1; }
        .grid > *[class*="2"] { grid-row: 1; grid-column: 2; }
        .grid > *[class*="3"] { grid-row: 1; grid-column: 3; }
        .grid > *[class*="4"] { grid-row: 1; grid-column: 4; }
        .grid > *[class*="5"] { grid-row: 1; grid-column: 5; }
        .grid > *[class*="6"] { grid-row: 1; grid-column: 6; }
        .grid > *[class*="7"] { grid-row: 1; grid-column: 7; }
        .grid > *[class*="8"] { grid-row: 1; grid-column: 8; }
    </style>
</head>
<body>
    justify-self: (默认align-self: center;)
    <div class="grid">
        <span class="box1">start</span>
        <span class="box2">end</span>
        <span class="box3">center</span>
        <span class="box4">left</span>
        <span class="box5">right</span>
        <span class="box6">self-start (RTL)</span>
        <span class="box7">self-end (RTL)</span>
        <span class="box8">stretch</span>
        <span class="gridline1"></span>
        <span class="gridline2"></span>
        <span class="gridline3"></span>
        <span class="gridline4"></span>
        <span class="gridline5"></span>
        <span class="gridline6"></span>
        <span class="gridline7"></span>
        <span class="gridline8"></span>
    </div>
    align-self: (默认justify-self: center;)
    <div class="grid">
        <span class="box1">start</span>
        <span class="box2">end</span>
        <span class="box3">center</span>
        <span class="box4">left</span>
        <span class="box5">right</span>
        <span class="box6">self-start (RTL)</span>
        <span class="box7">self-end (RTL)</span>
        <span class="box8">stretch</span>
        <span class="gridline1"></span>
        <span class="gridline2"></span>
        <span class="gridline3"></span>
        <span class="gridline4"></span>
        <span class="gridline5"></span>
        <span class="gridline6"></span>
        <span class="gridline7"></span>
        <span class="gridline8"></span>
    </div>
</body>
</html>
```

### §10.7.2 对齐所有元素(`align-items`/`justify-items`)

`align-items`与`justify-items`作用于栅格容器的CSS属性，负责调整栅格容器内的所有栅格元素的水平与垂直的对齐行为。其属性值与`justify-self`和`align-self`完全相同。

### §10.7.3 分布所有元素(`align-content`/`justify-content`)

`align-content`和`justify-content`负责决定栅格元素的分布方式。

```html
<html>
<head>
    <style>
        .grid {
            display: grid;
            background-color: lightgray;
            margin-bottom: 1rem;
        }
        .grid:nth-child(1), .grid:nth-child(2), .grid:nth-child(3) {
            width: 30rem;
            grid-template-rows: repeat(1, 3rem);
            grid-template-columns: repeat(4, 3rem);
        }
        .grid:nth-child(4), .grid:nth-child(5), .grid:nth-child(6) {
            height: 15rem;
            grid-template-rows: repeat(4, 3rem);
            grid-template-columns: repeat(1, 3rem);
        }
        .grid:nth-child(1){ justify-content: space-between; }
        .grid:nth-child(2){ justify-content: space-around; }
        .grid:nth-child(3){ justify-content: space-evenly; }
        .grid:nth-child(4){ align-content: space-between; }
        .grid:nth-child(5){ align-content: space-around; }
        .grid:nth-child(6){ align-content: space-evenly; }
        .grid > * {
            border: 1px solid black;
            text-align: center;
            line-height: 3rem;
            font-size: x-large;
        }
    </style>
</head>
<body>
    justify-content: space-between;
    <div class="grid"></div>
    justify-content: space-around;
    <div class="grid"></div>
    justify-content: space-evenly;
    <div class="grid"></div>
    align-content: space-between;
    <div class="grid"></div>
    align-content: space-around;
    <div class="grid"></div>
    align-content: space-evenly;
    <div class="grid"></div>
    <script>
        const gridDOMs = document.querySelectorAll('.grid');
        gridDOMs.forEach((element, key) => {
            let elementHTML = "";
            for (let i = 0 ; i < 4 ; i++) {
                let redCode = Math.floor((1 + Math.sin(2 * Math.PI * i / 16)) / 2 * 255).toString();
                let greenCode = Math.floor((1 + Math.sin(2 * Math.PI * i / 16 + Math.PI * 2 / 3)) / 2 * 255).toString();
                let blueCode = Math.floor((1 + Math.sin(2 * Math.PI * i / 16 + Math.PI * 4 / 3)) / 2 * 255).toString();
                console.log(redCode);
                elementHTML += `<span style="background: rgba(${redCode}, ${greenCode}, ${blueCode}, 0.5);">${i}</span>`;
            }
            element.innerHTML = elementHTML;
        });
    </script>
</body>
</html>
```

## §10.8 栅格总属性(`grid`)

`grid`属性以简洁的语法定义栅格模版和栅格流，但是二者不能同时设置。

<iframe src="https://embed.ihateregex.io/make/KG5vbmUpJTdDKHN1YmdyaWQpJTdDKCUzQ2dyaWQtdGVtcGxhdGUtcm93cyUzRSU1QyU1QyUyRiUzQ2dyaWQtdGVtcGxhdGUtY29sdW1ucyUzRSklN0MoKCglM0NsaW5lLW5hbWVzJTNFKSUzRiUzQ3N0cmluZyUzRSglM0N0cmFjay1zaXplJTNFKSUzRiglM0NsaW5lLW5hbWVzJTNFKSUzRiklMkIoJTVDJTVDJTJGJTNDdHJhY2stbGlzdCUzRSklM0YpJTdDKCUzQ2dyaWQtYXV0by1mbG93JTNFKCUzQ2dyaWQtYXV0by1yb3dzJTNFKCU1QyU1QyUyRiUzQ2dyaWQtYXV0by1jb2x1bW5zJTNFKSUzRiklM0Yp"
        height="400"
        style="width:100%"
        frameborder="0"></iframe>

`grid`其中的一种取值是`<grid-template-rows>/<grid-template-columns>`，例如下面两种写法完全等效：

```css
.grid {
	grid:
        "header header header header" 3rem
        "leftside content content rightside" 1fr
        "footer footer footer footer" 5rem / 
        2rem 3fr minmax(10rem, 1fr) 2rem;
}
.grid {
	grid-template-areas:
        "header header header header"
        "leftside content content rightside"
        "footer footer footer footer";
    grid-template-rows: 3rem 1fr 5rem;
    grid-template-columns: 2rem 3ft minmax(10rem, 1ft) 2rem;
}
```

`grid`的另一种取值是`(<line-names>? <string> <track-size>? <line-names>?)+ (/<track-list>)?`。这本质是上一种方式的升级版：

```css
.grid {
    grid:
	    "header header header header" 3rem,
    	[main-start] "leftside content content rightside" 1fr [main=stop],
        "footer footer footer footer" 5rem [page-end] /
        2rem 3fr minmax(10rem, 1fr) 2rem;
}
```

` [<grid-auto-flow>[<grid-auto-rows>[/<grid-auto-columns>]?]?]`，例如下面两种写法完全等效：

```css
.grid {
	grid: dense rows 2rem / minmax(1rem, 3rem);
}
.grid {
	grid-auto-flow: dense rows;
    grid-auto-rows: 2rem;
    grid-auto-columns: minmax(1rem, 3rem);
}
```

> 注意：`grid: subgrid;`属性值还不成熟，因此不过多介绍。 

# §11 表格布局

表格布局是最原始的布局方式。在弹性盒布局和栅格布局出现之前，只有表格布局能对齐不同尺寸的元素。它有诸多限制，例如每个单元格的长宽必须相等，除表题之外的元素都没有外边距等等。

以下是表格布局常用的术语：

- 行框：由栅格单元构成的行
- 行组：由相邻的行框组成的集合
- 列框：由一个或多个栅格单元构成的列
- 列组：由相邻的列框组成的集合

## §11.0 前置知识

HTML可以使用标签直接定义表格，下面是一个示例：

```html
<table>
	<thead> <!-- 定义表头 -->
    	<tr> <!-- 定义一行 -->
            <th>列标题1</th> <!-- 定义表头单元格 -->
      		<th>列标题2</th>
      		<th>列标题3</th>
    	</tr>
  	</thead>
  	<tbody>
    	<tr>
      		<td>行1，列1</td> <!-- 定义表体单元格 -->
      		<td>行1，列2</td>
      		<td>行1，列3</td>
    	</tr>
    	<tr>
      		<td>行2，列1</td>
      		<td>行2，列2</td>
      		<td>行2，列3</td>
    	</tr>
  	</tbody>
</table>
```

### §11.0.1 匿名表格对象

如果源代码中定义的表格缺少了一些元素，HTML会尝试以匿名对象的形式插入缺少的表格组件。这种行为遵守的规则称为“对象插入规则”。

1. 如果`<td>`/`<th>`的父元素不是`<tr>`，则在`<td>`/`<th>`及其父元素之间插入一个匿名`<tr>`对象。
2. 如果`<tr>`的父元素不是`<table>`/`<inline-table>`/`<tbody>`，则在`<tr>`及其父元素之间插入一个匿名`<table>`对象。
3. 如果`<col>`的父元素不是`<table>`/`<inline-table>`/`<colgroup>`，则在`<col>`及其父元素之间插入一个匿名`<table>`对象。
4. 如果`<tbody>`/`<thead>`/`<tfoot>`/`<colgroup>`/`<caption>`的父元素不是`<table>`，则在该元素及其父元素之间插入一个匿名`<table>`对象。
5. 如果`<table>`/`<inline-table>`的子元素不是`<tbody>`/`<tfoot>`/`<tr>`/`<caption>`，则在该元素与其子元素之间插入一个匿名`<tr>`对象。
6. 如果`<tbody>`/`<thead>`/`<tfoot>`的子元素不是`<tr>`，则在该元素与其子元素之间插入一个匿名`<tr>`对象。
7. 如果`<tr>`的子元素不是`<td>`/`<th>`，则在该元素与其子元素之间插入一个匿名`<td>`/`<th>`对象。

## §11.1 表格布局的定义

与弹性盒布局和栅格布局不同，HTML其实可以通过`<tr>`和`<td>`就能创建表格布局。但是XML就没有这种规范了，只能使用CSS的`display`属性定义。由于`display`的语法极其繁琐，这里我们只介绍和表格布局有关的属性值。

| `display`属性值      | 对应的HTML元素 | 作用                                          |
| -------------------- | -------------- | --------------------------------------------- |
| `table`              | `<table>`      | 把元素定义为块级表格                          |
| `inline-table`       |                | 把元素定义为行内表格                          |
| `table-row`          | `<tr>`         | 把元素定义为由单元格构成的行                  |
| `table-row-group`    | `<tbody>`      | 把元素定义为由一行或多行构成的组              |
| `table-header-group` | `<thead>`      | 与`table-row-group`相似，但是永远显示在最前面 |
| `table-footer-group` | `<tfoot>`      | 与`table-row-group`相似，但是永远显示在最后面 |
| `table-column`       | `<col>`        | 把元素定义为由单元格构成的列，不会被渲染      |
| `table-column-group` | `<colgroup>`   | 把元素定义为由一列或多列构成的组，不会被渲染  |
| `table-cell`         | `<td>`/`<th>`  | 把元素定义为一个单元格                        |
| `table-caption`      | `<caption>`    | 把元素定义为一个表题                          |

开发者应熟记上表中`display`属性值的作用。例如给定下列XML文档，我们可以根据该表赋予对应的`display`属性值，使其转化为表格数据：

```xml
<scores>
    <headers>
        <label>Team</label>
        <label>Score</label>
    </headers>
    <game sport="football">
        <team>
            <name>Red</name>
            <score>8</score>
        </team>
        <team>
            <name>Red</name>
            <score>5</score>
        </team>
    </game>
</scores>
```

```css
scores { display: table; }
headers { display: table-header-group; }
game { display: table-row-group; }
team { display: table-row; }
label, name, score { display: table-cell; }
```

CSS的表格布局是"行主导"的。具体来说，CSS默认源代码中会显式地声明行，而列从行中的单元格布局衍生而来。于是，第一列从每行的第一个单元格抽取而来，第二列、第三列......同理。

CSS规定：一个完整的表格包含了6个独立的图层，从上到下分别是：单元格、行、行组、列、列组、表格。

```html
<html>
<head>
    <style>
        table { background-color: lightblue; }
        table > thead > tr:nth-child(1) > td:nth-child(2) { background-color: lightgreen; }
        table > tbody > tr:nth-child(2) > td:nth-child(2) { background-color: lightcoral; }
    </style>
</head>
<body>
    <table>
        <thead>
            <tr>
                <td>Team</td>
                <td>Points</td>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>A</td>
                <td>15</td>
            </tr>
            <tr>
                <td>B</td>
                <td>21</td>
            </tr>
        </tbody>
    </table>
</body>
</html>
```

## §11.2 表格表题(`caption-side`)

| `caption-side`属性值 | 作用       |
| -------------------- | ---------- |
| `top`(缺省)          | 表题在上方 |
| `bottom`             | 表题在下方 |

```html
<html>
<head>
    <style>
        table { width: 200px; background-color: lightblue; margin-bottom: 2rem; }
        table:nth-of-type(1) > caption { caption-side: top; color: red;}
        table:nth-of-type(2) > caption { caption-side: bottom; color: red;}
    </style>
</head>
<body>
    caption-side: top;
    <table>
        <caption>Figure1: Scores List</caption>
        <thead>
            <tr><td>Team</td><td>Points</td></tr>
        </thead>
        <tbody>
            <tr><td>A</td><td>15</td></tr>
            <tr><td>B</td><td>21</td></tr>
        </tbody>
    </table>
    caption-side: bottom;
    <table>
        <caption>Figure1: Scores List</caption>
        <thead>
            <tr><td>Team</td><td>Points</td></tr>
        </thead>
        <tbody>
            <tr><td>A</td><td>15</td></tr>
            <tr><td>B</td><td>21</td></tr>
        </tbody>
    </table>
</body>
</html>
```

## §11.3 表格边框(`border-collapse`)

CSS有两种不同的边框模型——早期默认使用折叠边框模型，后期默认使用分离边框模型。我们可以使用`border-collapse`属性单独指定使用哪种模型。

| `border-collapse`属性值 | 作用             |
| ----------------------- | ---------------- |
| `collapse`(CSS2缺省)    | 使用折叠边框模型 |
| `separate`(CSS3缺省)    | 使用分离边框模型 |
| `inherit`               |                  |

### §11.3.1 分离边框模型

在分离边框模型中，每个单元格都与其它单元格相隔一定的距离，且边框不会重叠。

```html
<html>
<head>
    <style>
        table { 
            width: 200px; 
            background-color: lightblue;
            margin-bottom: 2rem; 
            border-collapse: separate;
        }
        table td {
            border: 2px solid black;
            padding: 5px;
        }
    </style>
</head>
<body>
    caption-side: top;
    <table>
        <caption>Figure1: Scores List</caption>
        <thead><tr><td>Team</td><td>Points</td></tr></thead>
        <tbody><tr><td>A</td><td>15</td></tr><tr><td>B</td><td>21</td></tr></tbody>
    </table>
</body>
</html>
```

在分离边框模型的基础上，可以使用`border-spacing`属性指定边框的横向与纵向间距。该属性附在`<table>`元素上，由1~2个形参组成，形参之间用空格分割，前者表示横向间距，后者表示纵向间距。

```html
<html>
<head>
    <style>
        table { 
            width: 200px; 
            background-color: lightblue;
            margin-bottom: 2rem; 
            border-collapse: separate;
            border-spacing: 1px 10px; /* 设置的边框间距 */
        }
        table td {
            border: 2px solid black;
            padding: 5px;
        }
    </style>
</head>
<body>
    caption-side: top;
    <table>
        <caption>Figure1: Scores List</caption>
        <thead><tr><td>Team</td><td>Points</td></tr></thead>
        <tbody><tr><td>A</td><td>15</td></tr><tr><td>B</td><td>21</td></tr></tbody>
    </table>
</body>
</html>
```

如果单元格中没有内容，则被称为空单元格。我们可以使用`empty-cells`属性决定是否显示空单元格（包括该单元格的背景和边框）。该属性值有`show`(缺省)/`hide`两种取值。

```html
<html>
<head>
    <style>
        table { 
            width: 200px; 
            background-color: lightblue;
            margin-bottom: 2rem; 
            border-collapse: separate;
            empty-cells: hide; /* 隐藏空白单元格 */
        }
        table td {
            border: 2px solid black;
            padding: 5px;
        }
    </style>
</head>
<body>
    <table>
        <caption>Figure1: Scores List</caption>
        <thead><tr><td>Team</td><td>Points</td></tr></thead>
        <tbody><tr><td>A</td><td>15</td></tr><tr><td>B</td><td></td></tr></tbody>
    </table>
</body>
</html>
```

### §11.3.2 折叠边框模型

折叠边框模型具有以下特点：

1. 单元格的边框之间一定没有间隔。
2. `display: table`/`display: inline-table`的元素不能有内边距，但是可以有内边距。这意味着边框一定紧贴着最外层的单元格。

其实在折叠边框模型中，边框不是真的折叠了，而是重叠了。两条相邻的边框都会被绘制，至于哪一条边框在图层上方，需要按照以下规则确定：

1. 如果某一条边框为`border-style: hidden`，则该条边框优先级最低。

2. 如果所有边框均可见，则较窄边框的优先级高于较宽边框。

3. 如果所有边框的宽度相同，那么按照边框式样(`border-style`)对优先级从高到低排序：

   ```
   double > solid > dashed > dotted > ridge > outset > groove > inset > none
   ```

4. 如果所有边框的式样相同，那么边框会按照下列顺序尝试继承颜色：

   ```
   单元格 > 行 > 行组 > 列 > 列组 > 表格
   ```

## §11.4 表格尺寸

### §11.4.1 宽度(`table-layout`)

`table-layout`

| `table-layout`属性值 | 作用         |
| -------------------- | ------------ |
| `auto`(缺省)         | 使用自动布局 |
| `fixed`              | 使用固定布局 |

固定布局无需考虑单元格中的内容，因此加载速度更快。它具有以下特点：

1. 如果列元素**不是**`width: auto`，那么设定的就是整列的宽度。
2. 如果列元素是`width: auto`，但是该列中的存在一个单元格，其所在的行恰为第一行，而且规定了这个单元格不是`width: auto`，那么这个单元格的宽度就是整列的宽度。如果这个单元格横跨多列，那么宽度会平分到到各列中。
3. 如果列元素与其第一行的单元格都是`width: auto`，那么CSS将自行确定宽度，尽量保证各列宽度相等。

分析上面的规则，我们不难发现固定布局加载速度快的原因：每一列的宽度只由第一行确定，后面所有行的这一列单元格都与第一行确定的列宽度保持一致。

固定布局最终的宽度为$\max\left({\text{width}_{\text{table}},\displaystyle\sum_{j}\text{width}_{1,j}}\right)$。如果$\displaystyle\sum_{j}\text{width}_{1,j}>\text{width}_{\text{table}}$，那么多出来的这部分宽度会均匀的增加给各列的宽度。

```html
<html>
<head>
    <style>
        table {
            table-layout: fixed;
            width: 400px;
            border-collapse: collapse;
        }
        td { border: 1px solid; }
        col#c1 { width: 200px; }
        #r1c2 { width: 75px; }
        #r2c3 { width: 500px; }
    </style>
</head>
<body>
    <table>
        <colgroup>
            <col id="c1">
            <col id="c2">
            <col id="c3">
            <col id="c4">
        </colgroup>
        <tr>
            <td id="r1c1">1-1</td>
            <td id="r1c2">1-2</td>
            <td id="r1c3">1-3</td>
            <td id="r1c4">1-4</td>
        </tr>
        <tr>
            <td id="r2c1">2-1</td>
            <td id="r2c2">2-2</td>
            <td id="r2c3">2-3</td>
            <td id="r2c4">2-4</td>
        </tr>
        <tr>
            <td id="r3c1">3-1</td>
            <td id="r3c2">3-2</td>
            <td id="r3c3">3-3</td>
            <td id="r3c4">3-4</td>
        </tr>
        <tr>
            <td id="r4c1">4-1</td>
            <td id="r4c2">4-2</td>
            <td id="r4c3">4-3</td>
            <td id="r4c4">4-4</td>
        </tr>
    </table>
</body>
</html>
```

自动布局是CSS默认使用的布局。它加载较慢的原因是：CSS必须读去玩表格中的全部内容后，才能开始排布。具体步骤如下所示：

1. 计算每一列中的每个单元格的最小宽度和最大宽度：
   - 单元格的最小宽度：在内容可以换行，但是不能超出单元格边框的前提下，单元格所占的宽度。
   - 单元格的最大宽度：在内容不能换行（除非遇到强制换行，例如`<br>`/`&#10;`）的前提下
2. 计算各列的的最小宽度和最大宽度。
3. 如果单元格横跨多列，那么涉及到的这几列的最小宽度之和，必须等于单元格的最小宽度。同理，涉及到的这几列的最大宽度之和也要等于该单元格的最大宽度。
4. 到此为止，CSS已经知道每一列有多宽了。如果`<table>`是`width: auto`，则表格的最终宽度等于列宽度、边框、间距这三者之和。如果`<table>`不是`width: auto`，则表格的最终宽度为$\max{\left(\text{width}_{\text{table}},\displaystyle\sum_{}(\text{width}_{列}+\text{width}_\text{边框}+\text{width}_{间距})\right)}$。

例如，我们假设有一个四行四列的表格，每个单元格包含的内容都较少，仅有`22px`，都有`1px`的边框，宽度为`auto`。第三列宽度为`25%`，一行二列单元格的宽度为`40%`，二行二列单元格的宽度为`50px`，二行三列单元格的宽度为`35px`，四行一列单元格的宽度为`100px`，四行撕裂单元格的宽度为`1px`。请求解这四列的实际宽度。接下来我们按照上面的算法进行计算：

1. 计算每一列中的每个单元格的最小宽度和最大宽度。
2. 计算各列的最小宽度和最大宽度。
   - 第一列：四行一列单元格的宽度为`100px`，远高于单元格内容宽度，所以最小宽度和最大宽度都是`100px`。
   - 第二列：一行二列单元格的宽度为`40%`，二行二列单元格的宽度为`50px`，因此最小宽度和最大宽度分别为`50px`和`40%`。
   - 第三列：三行三列单元格的宽度是`35px`，这一列宽度为`25%`，以你最小宽度和最大宽度分别为`35px`和`25%`。
   - 第四列：四行四列单元格的宽度为`1px`，远小于内容宽度`22px`，因此最小宽度和最大宽度都是内容宽度`22px`。
3. 计算表格的最小宽度和最大宽度。
   - 表格的最小宽度为$215\text{px}$，最大宽度为$123\text{px}+65\%$，其中$123\text{px}$是第一列与第四列的最大宽度与公用折叠边框宽度之和。由于$\text{123px}$占用了整个宽度的$1-40\%-25\%=35\%$，所以表格的最大宽度为$\displaystyle\frac{123\text{px}}{35\%}\approx351.43\text{px}$。

### §11.4.2 高度

开发者可以对表格显式使用`height`属性指定高度。对于表格而言，`height`的行为更类似于`min-height`——如果各行高度之和大于表格高度，那么表格的高度实际上会超出`height`的限制。如果反过来说小于表格高度，那么表格的高度就是`height`所规定的。

> 注意：当各行高度之和小于表格高度时，CSS并没有规定这部分多出来的高度应该怎么办。不同的用户代理可能有不同的行为——有的会在表格中留白，有的会给所有行平分高度。

### §11.4.3 对齐

| `text-align`/`vertical-align`属性值 | `vertica-align`作用                            |
| ----------------------------------- | ---------------------------------------------- |
| `top`                               | 单元格中内容的顶部与（所跨第一的）行的顶部对齐 |
| `bottom`                            | 单元格中内容的底部与（所跨最后的）行的底部对齐 |
| `middle`                            | 单元格中内容的中线与（所跨各）行的中线对齐     |
| `baseline`                          | 单元格中内容的基线与（所跨第一）行的基线对齐   |

```html
<html>
<head>
    <style>
        table {
            table-layout: fixed;
            width: 50rem;
            height: 10rem;
            border-collapse: collapse;
        }
        table td { 
            border: 1px solid; 
            padding-top: 1rem;
        }
        table > tbody > tr > td:nth-of-type(1) { vertical-align: top; }
        table > tbody > tr > td:nth-of-type(2) { vertical-align: bottom; }
        table > tbody > tr > td:nth-of-type(3) { vertical-align: middle; }
        table > tbody > tr > td:nth-of-type(4) { vertical-align: baseline; font-size: larger; }
    </style>
</head>
<body>
    <table>
        <tbody>
            <tr>
                <td>vertical-align: top;</td>
                <td>vertical-align: bottom;</td>
                <td>vertical-align: middle;</td>
                <td>vertical-align: baseline;</td>
            </tr>
        </tbody>
    </table>
</body>
</html>
```

# §12 列表与生成

列表是CSS中最简单的块级框。从CSS2开始允许开发者控制列表项目的编号。

## §12.1 列表样式

### §12.1.1 列表记号(`list-style-type`)

| `list-style-type`属性值 | 作用                      | `list-style-type`属性值 | 作用                    | `list-style-type`属性值 | 作用                                    |
| ----------------------- | ------------------------- | ----------------------- | ----------------------- | ----------------------- | --------------------------------------- |
| `disc`                  | 实心圆点                  | `kannada`               |                         | `hiragana`              |                                         |
| `circle`                | 空心圆点                  | `lao`                   |                         | `hiragana-iroha`        |                                         |
| `square`                | 实心正方形                | `malayalam`             |                         | `katakana`              |                                         |
| `disclosure-open`       | 实心倒三角型              | `mongolian`             |                         | `katakana-ironha`       |                                         |
| `disclosure-closed`     | 实心右三角形              | `myanmar`               |                         | `japanese-informal`     |                                         |
| `decimal`               | 十进制数字                | `oriya`                 |                         | `japanese-formal`       |                                         |
| `decimal-leading-zero`  | 十进制数字（带有前导`0`） | `persian`               |                         | `korean-hangul-formal`  |                                         |
| `arabic-indic`          |                           | `lower-roman`           | 小写罗马字母            | `korean-hanja-informal` |                                         |
| `armenian`              |                           | `upper-roman`           | 大写罗马字母            | `korean-hanja-formal`   |                                         |
| `upper-armenian`        |                           | `tamil`                 |                         | `simp-chinese-infromal` |                                         |
| `lower-armenian`        |                           | `telugu`                |                         | `simp-chinese-formal`   |                                         |
| `bengail`               |                           | `thai`                  |                         | `trad-chinese-informal` |                                         |
| `cambodian`             |                           | `tibetan`               |                         | `trad-chinese-formal`   |                                         |
| `khmer`                 |                           | `lower-alpha`           | 实质上就是`lower-latin` | `ethiopic-numeric`      |                                         |
| `cjk-decimal`           | 中日韩汉字计数            | `lower-latin`           | 小写拉丁字母            | **none**                | **不显示**                              |
| `devanagari`            |                           | `upper-alpha`           | 实质上就是`upper-latin` | **`string`**            | 自行指定记号字符（包括特殊字符与Emoji） |
| `gujarati`              |                           | `upper-latin`           | 大写拉丁字母            |                         |                                         |
| `gurmukhi`              |                           | `cjk-earthly-branch`    |                         |                         |                                         |
| `georgian`              |                           | `cjk-heavenly-stem`     |                         |                         |                                         |
| `hebrew`                |                           | `lower-greek`           |                         |                         |                                         |

```html
<html>
<head>
    <style>
        .container {
            display: flex;
            width: 60rem;
            flex-wrap: wrap;
        }
        ol {
            margin: 0.5rem;
            padding-right: 2rem;
            border: 1px solid black;
        }
    </style>
</head>
<body>
    list-style-type:
    <div class="container"></div>
    <script>
        const listStyleTypes = [
            "disc", "circle", "square", "disclosure-open", "disclosure-closed", "decimal", "decimal-leading-zero", "arabic-indic", "armenian",
            "upper-armenian", "lower-armenian", "bengail", "cambodian", "khmer", "cjk-decimal", "devanagari", "gujarati", "gurmukhi", "georgian", "hebrew",
            "kannada","lao", "malayalam", "mongolian", "myanmar", "oriya", "persian", "lower-roman", "upper-roman", 
            "tamil", "telugu", "thai", "tibetan", "lower-alpha", "lower-latin", "upper-alpha", "upper-latin", "cjk-earthly-branch", "cjk-heavenly-stem", "lower-greek",
            "hiragana", "hiragana-iroha", "katakana", "katakana-iroha", "japanese-informal", "japanese-formal", "korean-hangul-formal", "korean-hanja-informal", "korean-hanja-formal", 
            "simp-chinese-informal", "simp-chinese-formal", "trad-chinese-informal", "trad-chinese-formal", "ethiopic-numeric"
        ];
        const conatinerDOM = document.querySelector(".container");
        listStyleTypes.forEach((type, key) => {
            conatinerDOM.innerHTML += `<ol style="list-style-type: ${type};">${listStyleTypes[key]}<li>Alice</li><li>Bob</li><li>Carol</li></ol>`;
        });
    </script>
</body>
</html>
```

### §12.1.2 列表记号图像(`list-style-image`)

常规的列表记号只支持纯文本，CSS也支持使用`list-style-image`属性图像设置记号。其属性值可以是任何图像，包括渐变图像。

```html
<html>
<head>
    <style>
        .container {
            display: flex;
            width: 60rem;
            flex-wrap: wrap;
        }
        ol {
            margin: 0.5rem;
            padding-right: 2rem;
            border: 1px solid black;
            list-style-type: none;
            list-style-image: radial-gradient(red, black, red);
            font-size: xx-large;
        }
    </style>
</head>
<body>
    <div class="container">
        <ol>
            <li>Alice</li>
            <li>Bob</li>
            <li>Carol</li>
        </ol>
    </div>
</body>
</html>
```

> 注意：图表项目图像的有一个缺点：默认尺寸特别小。在2020年之前，没有任何方案能在不更改`font-size`的情况下单独改变这一图像的尺寸。在2020年后，各大浏览器开始支持CSS规范中的`::marker`伪元素，可以直接更改它的`font-size`/`color`/`margin`等属性。

### §12.1.3 列表记号的位置(`list-style-position`)

`list-style-position`属性决定列表记号的显示位置。

| `list-style-position`属性值 | 作用                                                       |
| --------------------------- | ---------------------------------------------------------- |
| `inside`                    | 把记号放在列表内容之中，即记号左边与下面几行的首字左边对齐 |
| `outside`(缺省)             | 把记号放在列表内容以外，即记号右边与下面几行的首字左边对齐 |

```html
<html>
<head>
    <style>
        .container {
            display: flex;
            width: 60rem;
            flex-wrap: wrap;
        }
        ol {
            margin: 0.5rem;
            padding-right: 2rem;
            border: 1px solid black;
            list-style-type: square;
            width: 10rem;
        }
        ol > li:nth-of-type(1) { list-style-position: inside; }
        ol > li:nth-of-type(2) { list-style-position: outside; }
    </style>
</head>
<body>
    <div class="container">
        <ol>
            <li>This is a long sentence with <code>list-style-position: inside;</code></li>
            <li>This is a long sentence with <code>list-style-position: outside;</code></li>
        </ol>
    </div>
</body>
</html>
```

## §12.2 生成内容

CSS定义了生成内容的方式，不必通过源文档展示。例如前面提到的列表记号起始就是生成内容的一种。

### §12.2.1 `::before`和`::after`

我们可以使用`::before`和`::after`伪元素将生成的内容插入到文档中。

```css
a[href] { border: 1px solid black; }

a[href]::before { content: "(Link)"; }
a[href].link-pdf::after { content: url(./static/icon/pdf.png); }
```

容易发现，`a[href]`的内容区被撑大了。CSS规定，除了列表记号，所有`::before`和`::after`伪元素都被规定位于元素框的内部。这意味着`text-decoration`属性会影响`content`属性的文字、`border`作用的边框区也会被撑大。

`::before`和`::after`伪元素有以下特点：

- CSS2规定：`::before`和`::after`伪元素禁止使用浮动属性(`float`)和定位属性(`position`)。
- 如果`::before`和`::after`的选择目标是块级元素，那么`display`属性只能为`none`、`inline`、`block`、`marker`，其他属性值一律重置为`block`。
- 如果`::before`和`::after`的选择目标是行内元素，那么`display`属性只能为`none`、`inline`，其他属性值一律重置为`inline`。

```html
<html>
<head>
    <style>
        h1 { color: darkred; }
        h1.life-category::before { 
            content: "[LIFE]";
            color: black;
            padding: 1rem;
            font-size: large;
            text-align: center;
        }
        h1.social-category::before { 
            content: "[SOCIAL]";
            color: black;
            padding: 1rem;
            font-size: large;
            text-align: center;
        }
    </style>
</head>
<body>
    <h1 class="life-category">Healthy plan for jogging</h1>
    <h1 class="social-category">America president election 2024 will start </h1>
</body>
</html>
```

### §12.2.2 指定内容(`content`)

`contnet`属性用于指示生成的内容，只能用于`::before`和`::after`伪元素中。

| `content`属性值                                          | 作用                               |
| -------------------------------------------------------- | ---------------------------------- |
| `normal`(缺省)                                           |                                    |
| `<string>`                                               | 字符串                             |
| `url(<uri>)`                                             | 加载图片资源                       |
| `<counter>`                                              | 计数器                             |
| `attr(<identifier>)`                                     | 获取当前标签的`<identifier>`属性值 |
| `[open-quote|close-quote|no-open-quote|no-close-quote]+` | 引号                               |

当`content`属性为`<string>`时，CSS会将字符串中的内容原封不动的展示出来。

> 注意：我们知道CSS会显示`content: <string>`，这意味着`<string>`无法进程HTML包含注入，其中的HTML实体、便签也不会生效。正确的做法是使用转义符`\`（兼容性差）或Unicode字符`\????`。
>
> ```html
> <html>
> <head>
>     <style>
>         a:nth-of-type(1)::after {
>             margin: 1rem;
>             content: "<em>&nbsp;</em>"
>         }
>         a:nth-of-type(2)::after {
>             margin: 1rem;
>             content: "Hello \000A dfsdfsdf"
>         }
>     </style>
> </head>
> <body>
>     <a>Hello</a>
>     <br>
>     <a>Hello</a>
> </body>
> </html>
> ```
>

当`content`属性为`attr(<identifier>)`时，CSS会显示该元素的`<identifier>`属性值作为未经解析的字符串，原封不动地显示出来。

```html
<html>
    <head>
        <style>
            a:nth-of-type(1)::after {
                margin: 1rem;
                content: attr(type)
            }
        </style>
    </head>
    <body>
        <a type="news">Hello</a>
    </body>
</html>
```

当`content`属性为`[open-quote|close-quote|no-open-quote|no-close-quote]+`时，CSS绘制引号。`quotes`属性值必须是偶数个只由空格分隔的字符串，表示多级引号。例如英语中双引号`"`内的引号记为单引号`'`：

| `content`引号相关属性值 | 作用                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `open-quote`            | 从`quotes`属性值中选取奇数位置的字符，作为起始的引号字符，增加一层嵌套等级，通常用于`::before`伪元素 |
| `close-quote`           | 从`quotes`属性值中选取偶数位置的字符，作为结尾的引号字符，减少一层嵌套等级，通常用于`::after`伪元素 |
| `no-open-quote`         | 不显示起始的引号字符，增加一层嵌套等级，通常用于`::before`伪元素 |
| `no-close-quote`        | 不显示结束的引号字符，增加一层嵌套等级，通常用于`::after`伪元素 |

```html
<html>
    <head>
        <style>
            quotation { display: block; }
            quote { quotes: '"' '"' "'" "'"; }
            quotation > quote::before { content: open-quote; }
            quotation > quote::after { content: close-quote; }
            quotation > quotee { quotes: "(" ")"; }
            quotation > quotee::before { content: open-quote; }
            quotation > quotee::after { content: close-quote; }
            quotation > quote > quote::before { content: open-quote; }
            quotation > quote > quote::after { content: close-quote; }
        </style>
    </head>
    <body>
        <quotation>
            <quote>Where's a will, there's a way</quote>
            <quotee>George Herber</quotee>
        </quotation>
        <quotation>
            <quote>George Herber said that 
                <quote>Where's a will, there's a way</quote>
            </quote>
        </quotation>
    </body>
</html>
```

> 注意：`quotes`属性、`content: open-quote`和`content: close-quote`必须同时配合使用，否则不生效。

### §12.2.3 计数器

计数器较为常见，有序列表的记号就是计数器中的其中一种。CSS1中不能修改计数器，由HTML为有序列表定义计数行为；从CSS2开始，HTML得以使用两个关于计数器的新属性，配套`content`属性值，实现更多样的计数效果。

`counter-reset`属性用于创建计数器。其语法可表示为`[<identifier> <integer>?]*`，其中`<integer>`缺省为0：

```css
ol {
	counter-reset: timer1 timer2 1;
    /* 定义计数器timer1为0,计数器timer2为1*/
}
```

`counter-reset`属性用于指定计数器的递增间隔。其余法可表示为`[<identifiler> <integer>?]*`。其中`<integer>`缺省为1。

```css
ol > li {
    counter-increment: timer1 timer2 -1;
    /* 定义计数器timer1递增值为1,计数器timer2递增值为-1*/
}
```

`content(计数器名称)`可以输出计数器的值。

```html
<html>
    <head>
        <style>
            OrderedList { counter-reset: itemCounter 0; }
            OrderedList > item { 
                display: block;
                counter-increment: itemCounter -1; 
            }
            OrderedList > item::before {
                border: 1px solid black;
                content: "<🥰" counter(itemCounter) "> " ;
            }
        </style>
    </head>
    <body>
        <div>特殊的列表:</div>
        <OrderedList>
            <item>abc</item>
            <item>123</item>
        </OrderedList>
    </body>
</html>
```

利用多个计数器配合工作，我们就能实现章节的自动标注：

```html
<html>
    <head>
        <style>
            h1 {
                counter-reset: section 0 subsection 0;
                counter-increment: chapter 1;
                padding-left: 0rem;
            }
            h2 {
                counter-reset: subsection;
                counter-increment: section 1;
                padding-left: 1rem;
            }
            h3 {
                counter-increment: subsection 1;
                padding-left: 2rem;
            }
            h1::before { content: counter(chapter) " "; }
            h2::before { content: counter(chapter) "." counter(section) " "; }
            h3::before { content: counter(chapter) "." counter(section) "." counter(subsection) " "; }
        </style>
    </head>
    <body>
        <h1>极限</h1>
        <h2>极限的定义</h2>
        <h3>数列的极限</h3>
        <h3>函数的极限</h3>
        <h3>极限的物理意义</h3>
        <h2>极限的应用</h2>
        <h1>导数</h1>
    </body>
</html>
```

其实，除了`counter(计数器名称)`以外，`counter()`还能接受第二个形参，表示列表类型的关键字，详见[§12.1.1 列表记号(`list-style-type`)](###§12.1.1 列表记号(`list-style-type`))一节。

```html
<html>
    <head>
        <style>
            h1 {
                counter-reset: section 0 subsection 0;
                counter-increment: chapter 1;
                padding-left: 0rem;
            }
            h2 {
                counter-reset: subsection;
                counter-increment: section 1;
                padding-left: 1rem;
            }
            h3 {
                counter-increment: subsection 1;
                padding-left: 2rem;
            }
            h1::before { content: counter(chapter, upper-alpha) " "; }
            h2::before { content: counter(chapter, upper-alpha) "." counter(section) " "; }
            h3::before { content: counter(chapter, upper-alpha) "." counter(section) "." counter(subsection, lower-roman) " "; }
        </style>
    </head>
    <body>
        <h1>极限</h1>
        <h2>极限的定义</h2>
        <h3>数列的极限</h3>
        <h3>函数的极限</h3>
        <h3>极限的物理意义</h3>
        <h2>极限的应用</h2>
        <h1>导数</h1>
    </body>
</html>
```

> 注意：若元素有`display: none`，则不参与计数器的递增。但是`visibility: hidden`依然会参与递增。

#### §12.2.3.1 计数器的作用域

我们已经知道如何给有限嵌套深度的列表创建计数器了。如果嵌套层级变深，那么上述的列表就很难维护。基于此，CSS2.1提出了计数器的作用域概念，使得不同作用域的计数器可以重名。

```html
<html>
    <head>
        <style>
            ol {
                padding-left: 0;
                list-style: none;
                counter-reset: ordered;
            }
            ol li::before {
                counter-increment: ordered 1;
                content: counter(ordered) ".";
            }
        </style>
    </head>
    <body>
        <ol>
            <li></li>
            <li></li>
            <ol>
                <li></li>
                <li></li>
                <ol>
                    <li></li>
                    <li></li>
                </ol>
            </ol>
        </ol>
    </body>
</html>
```

计数器的作用域取决于HTML文档标签嵌套的区域。在上面的例子中，每当CSS遇到一个`<ol>`，就在当前作用域中创建一个计数器，就算当前作用域已经有一个上层定义的同名计数器，由于同名计数器是在上层作用域定义的，因此也不会影响这一层的定义。每当遇到一个`li::before`时，就给当前作用域的计数器加一。

#### §12.2.3.2 `counters()`

在[§12.2.3.1 计数器的作用域](####§12.2.3.1 计数器的作用域)一节中，我们发现这样作出的章节序号只能显示当前层级的序号（例如子节），无法显示上层层级的序号（例如章）。这时我们可以使用`counters()`，将`counter()`本应输出的内容贴到上级`content()`的后面。`counters()`必须接受两个参数，第一个参数是同名计数器的名称，负责选中**所有作用域中的同名计数器**，并按照嵌套顺序排在一起。第二个参数时各同名计数器的值之间使用的分隔符。

```html
<html>
    <head>
        <style>
            ol {
                list-style: none;
                counter-reset: ordered;
            }
            ol li::before {
                counter-increment: ordered 1;
                content: counters(ordered, ".") ".";
            }
        </style>
    </head>
    <body>
        <ol>
            <li>极限
                <ol>
                    <li>极限的定义
                        <ol>
                            <li>数列的极限</li>
                            <li>函数的极限</li>
                            <li>极限的物理意义</li>
                        </ol>
                    </li>
                    <li>极限的应用</li>
                </ol>
            </li>
            <li>导数</li>
        </ol>
    </body>
</html>
```

#### §12.2.3.3 `@counter-style`

`@counter-style`块可以用于定义一些专门的描述符，从而控制计数模式。其格式大致如下所示：

```css
@counter-style <NAME> {
    system: ...;
    symbols: ...;
    ... /* 更多的描述符 */
}
```

| `@counter-style`描述符 | 作用                                                         | 缺省值     |
| ---------------------- | ------------------------------------------------------------ | ---------- |
| `system`               | 定义计数器使用的模式。可用的值有`fixed`、`cyclic`、`alphabetic`、`numeric`、`symbolic`、`additive`、`extends`。必须项 | `symbolic` |
| `symbols`              | 定义计数器模式中使用的符号或`url()`图像。必须项（除了`additive`和`extends`） |            |
| `additive-symbols`     | 定义`system: additive`时计数器模式使用的符号                 |            |
| `prefix`               | 定义放在计数器之前的字符串                                   | `""`       |
| `suffix`               | 定义放在计数器之后的字符串                                   | `"."`      |
| `negative`             | 定义放在计数器负值左侧或两侧的字符串                         | `"-"`      |
| `range`                | 定义计数器模式的范围值，超出范围后使用后备计数器             | `auto`     |
| `fallback`             | 定义后备计数器                                               | `decimal`  |
| `pad`                  | 定义计数器至少有几个字符，用指定字符串补齐                   | `0 ""`     |
| `speak-as`             | 定义计数器在文字转语音时的发音策略                           | `auto`     |

- 固定计数模式(`system: cyclic`)

  这是最简单的计数器模式。由`symbols`给定有限数量的字符，用完后不再重复。

  ```html
  <html>
      <head>
          <style>
              @counter-style LimitedEmoji { system: fixed; symbols: 😀 😁 😂; }
              ol.cyclic { list-style-type: LimitedEmoji; }
          </style>
      </head>
      <body><ol class="cyclic"><li>abc</li><li>def</li><li>ghi</li><li>jkl</li></ol></body>
  </html>
  ```

- 循环计数模式(`system: cyclic`)

  由`symbols`给定有限数量的字符，用完后回到开始再重复。

  ```html
  <html>
      <head>
          <style>
              @counter-style BlinkTriangle { system: cyclic; symbols: ▶ ▷; }
              ol.cyclic { list-style-type: BlinkTriangle; }
          </style>
      </head>
      <body><ol class="cyclic"><li>abc</li><li>def</li><li>ghi</li><li>jkl</li></ol></body>
  </html>
  ```

  > 注意：当`symbols`只有一个值时，起效果等价于不使用计数器，只用`list-style-type: "..."`。

- 符号计数模式(`system: symbolic`)

  在循环计数模式的基础上，每重复一次`symbols`列表，计数器输出的符号就多重复一个。

  ```html
  <html>
      <head>
          <style>
              @counter-style BreededEmoji { system: symbolic; symbols: 😀 😁; }
              ol.symbolic { list-style-type: BreededEmoji; }
          </style>
      </head>
      <body>
          <ol class="symbolic"><li>abc</li><li>def</li><li>ghi</li><li>jkl</li></ol>
      </body>
  </html>
  ```

- 字母计数模式(`system: alphabetic`)

  回想Excel软件中的列名，从小到大分别为`A`,`B`,`C`,...,`Y`,`Z`,`AA`,`AB`,...,`AZ`,...。这种计数模式就是字母计数模式的一种。要求`symbols`必须包含两个及以上的字符，表示字典序依次降低，否则整个`@counter-style`块无效。

  ```html
  <html>
      <head>
          <style>
              @counter-style AlphabetOrder { system: alphabetic; symbols: A B ;}
              ol.alphabetic { list-style-type: AlphabetOrder; }
          </style>
      </head>
      <body>
          <ol class="alphabetic"><li>abc</li><li>def</li><li>ghi</li><li>jkl</li></ol>
      </body>
  </html>
  ```

- 数字计数模式(`system: numeric`)

  数字计数模式类似于$n$进制的计数方式：`symbols`有几个属性值就记为多少进制，从左向右分别记为$0,1,2,...,n-1$对应的字符，最后从$n$进制的$0$开始向上计数。

  ```html
  <html>
      <head>
          <style>
              @counter-style Binary { system: numeric; symbols: '0' '1';}
              ol.numeric { list-style-type: Binary; }
          </style>
      </head>
      <body>
          <ol class="numeric"><li>abc</li><li>def</li><li>ghi</li><li>jkl</li></ol>
      </body>
  </html>
  ```

- 累加计数模式(`system: additive`)

  我们知道，人民币有`100`、`50`、`20`、`5`、`2`、`1`几种面值，对于一个给定的金额，我们想使用最少的纸币张数，拼凑出符合条件的金额。我们也知道，罗马数字使用`M`、`CM`、`D`、`CD`、`C`、`XC`、`L`、`XL`、`X`、`IX`、`V`、`IV`、`I`也能用最少的字符表示任意数字。累加计数模式就是这个作用。

  ```html
  <html>
      <head>
          <style>
              @counter-style Roman { 
                  system: additive; 
                  additive-symbols: 1000 M, 900 CM, 500 D, 400 CD,
                           100 C, 90 XC, 50 L, 40 XL,
                           10 X, 9 IX, 5 V, 4 IV, 1 I;
              }
              ol.additive { 
                  list-style-type: Roman; 
              }
          </style>
      </head>
      <body>
          <ol class="additive">
              <li>abc</li>
              <li>def</li>
              <li>ghi</li>
          </ol>
          <script>
              const olDOM = document.querySelector('.additive');
              olDOM.innerHTML = Array.from({length: 100}, (_, index) => {return `<li>${index + 1}</li>`}).join('')
          </script>
      </body>
  </html>
  ```

  > 注意：累加计数模式使用描述符的是`additive-symbols`，而不是`symbols`，就算使用`symbols`也会被忽略。在非累加计数模式中，严禁使用`additive-symbols`，这会导致整个`@counter-style`块失效。

  > 注意：如果遇到一个给定的数，使得无法做到让`additive-symbols`中的每个字符最多只出现一次，就能表示该数字，那么就使用`fallback`。

- 扩展计数模式(`symbol: extends <计数模式名称>`)

  扩展计数模式的意义在于它能使用现有的计数模式，并在此基础上拓展，从而避免造轮子。例如下面两种`@counter-style`完全一样。

  ```css
  @counter-style A {
  	system: numeric;
      symbols: '0' '1' '2' '3' '4' '5' '6' '7' '8' '9';
      suffix: ") "; pad: 2 "0";
  }
  
  @counter-style B {
  	system: extends decimal;
      suffix: ") "; pad: 2 "0";
  }
  ```

`prefix`和`suffix`描述符用于指定计数器的前后缀，缺省值分别为`""`和`"."`。

```html
<html>
    <head>
        <style>
            @counter-style LimitedEmoji { system: fixed; symbols: 😀 😁 😂; prefix: "~"; suffix: "~" }
            ol.cyclic { list-style-type: LimitedEmoji; }
        </style>
    </head>
    <body>
        <ol class="cyclic"><li>abc</li><li>def</li><li>ghi</li><li>jkl</li></ol>
    </body>
</html>
```

`range`描述符属性值语法为`[<integer> <integer>,]* (最后一个逗号不算) | auto(缺省)`，表示计数器在被调用第几次的次数区间内生效。生效的范围总共为所有区间的并集。例如为了防止符号计数模式生成过长的序号，我们可以为其加上`range`的限制：

```css
<html>
    <head>
        <style>
            @counter-style BreededEmoji { system: symbolic; symbols: 😀 😁; range: 1 2, 4 4; }
            ol.symbolic { list-style-type: BreededEmoji; }
        </style>
    </head>
    <body>
        <ol class="symbolic"><li>abc</li><li>def</li><li>ghi</li><li>jkl</li></ol>
    </body>
</html>
```

`fallback`描述符表示超出`range`范围时使用的计数器，其取值范围与`list-style-type`完全一致，缺省为`decimal`。

```html
<html>
    <head>
        <style>
            @counter-style BreededEmoji { system: symbolic; symbols: 😀 😁; range: 1 2, 4 4; fallback: hebrew; }
            ol.symbolic { list-style-type: BreededEmoji; }
        </style>
    </head>
    <body>
        <ol class="symbolic"><li>abc</li><li>def</li><li>ghi</li><li>jkl</li></ol>
    </body>
</html>
```

`negative`描述符用于表示计数器中负值的负号，语法为`<symbol> <symbol>?`，只能用于支持负数的计数系统中使用（例如`alphabetic`、`numeric`、`symbolic`、`additive`）。

```css
<html>
    <head>
        <style>
            @counter-style DecimalYear { 
                system: numeric; 
                symbols: '0' '1' '2' '3' '4' '5' '6' '7' '8' '9';
                negative: "BC.";
            }
            ol.numeric { 
                counter-reset: DecimalYear -1;
                list-style-type: DecimalYear; 
            }
        </style>
    </head>
    <body>
        公历纪年的历史：
        <ol class="numeric">
            <li value="-1">abc</li>
            <li>def</li>
            <li>ghi</li>
        </ol>
    </body>
</html>
```

`pad`描述符用于指定数字计数系统的填补长度与字符，语法为`<integer> <symbol>`：

```html
<html>
    <head>
        <style>
            @counter-style FormattedDecimal { 
                system: numeric; 
                symbols: '0' '1' '2' '3' '4' '5' '6' '7' '8' '9';
                pad: 4 "0"
            }
            ol.numeric { 
                list-style-type: FormattedDecimal; 
            }
        </style>
    </head>
    <body>
        <ol class="numeric">
            <li>abc</li>
            <li>def</li>
            <li>ghi</li>
        </ol>
    </body>
</html>
```

`speak-as`描述符用于定义语音转文字的行为。

| `speak-as`描述符属性值 | 作用                                                         |
| ---------------------- | ------------------------------------------------------------ |
| `auto`                 | 效果取决于使用的计数系统。例如`system: alphabetic`使用`speak-as: speel-out`；`system: cyclic`使用`speak-as: bullets`；`system: extend`取决于；其余均使用`speak-as: numbers`。 |
| `bullets`              | 蜂鸣器嘀一下                                                 |
| `numbers`              | 以当地发音规则，读出该计数器对应的数字                       |
| `words`                | 以单词为单位，逐个读出计数器单词                             |
| `spell-out`            | 以字符为单位，逐个读出计数器字符                             |
| `<counter-style-name>` | 让CSS根据指定的计数模式自行推断                              |

# §13 变形

CSS乃至计算机领域，常用的坐标系有：

- 笛卡尔坐标系是三维坐标系，以屏幕左上角为原点，向右为X轴正方向，向下为Y轴正方向，向屏幕外为Z轴正方向。
- 球坐标系是三维坐标系，沿坐标轴的正方向看过去，逆时针为正方向。或者沿坐标轴的负方向看过去，顺时针为正方向

## §13.1 变形函数(`transform`)

`transform`属性值的语法为`<transform-function>*`，表示众多变形函数按顺序执行。

| `<transform-function>`属性值                   | 作用                                                         | `<transform-function>`属性值                      | 作用                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------- | ------------------------------------------------------------ |
| `matrix()`                                     | 详见[MDN文档](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-function/matrix) | `scaleY()`                                        | 沿Y轴缩放`<NUMBER>`倍                                        |
| `matrix3d()`                                   | 详见[MDN文档](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-function/matrix3d) | `scaleZ()`                                        | 沿Z轴缩放`<NUMBER>`倍                                        |
| `perspective()`                                | 更改透视的视域，即观察者到该元素Z轴平面的距离。较小的视域类似于鱼眼镜头，较大的视域会产生等距效应，推荐在`500px`到`1000px`之间 | `skew(<NUMBER_X>, <NUMBER_Y>? = 0)`               | 将$(1,1)$掰到$(x,y)$处，进行`[ax,ay]`的矩阵运算。不能简单的拆分成`shewX()`和`shewY()` |
| `rotate(<ANGLE>)`                              | 绕Z轴旋转`<ANGLE>`                                           | `skewX(<ANGLE>)`                                  | 对于X、Y轴，在其沿X轴构成的平面上，使得有向线线角$<\vec{x},\vec{y}或\vec{z}>$增大`<ANGLE>`角度 |
| `rotate3d(<NUMBER>,<NUMBER>,<NUMBER>,<ANGLE>)` | 前三个值指定旋转轴法向量的X、Y、Z分量，第四个值指定角度      | `skewY(<ANGLE>)`                                  | 对于X、Z轴，在其沿X轴构成的平面上，使得有向线线角$<\vec{y},\vec{x}或\vec{z}>$增大`<ANGLE>`角度 |
| `rotateX(<ANGLE>)`                             | 绕X轴旋转`<ANGLE>`                                           | `translate(<LENGTH_X>, <LENGTH_Y>? = 0)`          | 向X、Y轴正方向平移`<LENGTH>`长度，第二个形参缺省为0          |
| `rotateY(<ANGLE>)`                             | 绕Y轴旋转`<ANGLE>`                                           | `translate3d(<LENGTH_X>, <LENGTH_Y>, <LENGTH_Z>)` | 向X、Y、Z轴正方向平移`<LENGTH>`长度                          |
| `rotateZ(<ANGLE>)`                             | 绕Z轴旋转`<ANGLE>`                                           | `translateX(<LENGTH>)`                            | 向X轴正方向平移`<LENGTH>`长度                                |
| `scale(<NUMBER>, <NUMBER>?)`                   | 沿X、Y轴缩放`<NUMBER>`倍，第二个值缺省时与第一个值相同       | `translateY(<LENGTH>)`                            | 向Y轴正方向平移`<LENGTH>`长度                                |
| `scale3d(<NUMBER>,<NUMBER>,<NUMBER>)`          | 沿X、Y、Z轴缩放`<NUMBER>`倍                                  | `translateZ(<LENGTH>)`                            | 向Z轴正方向平移`<LENGTH>`长度                                |
| `scaleX(<NUMBER>)`                             | 沿X轴缩放`<NUMBER>`倍                                        |                                                   |                                                              |

```html
<html>
    <head>
        <style>
            body { display: flex; padding: 1rem; flex-wrap: wrap; }
            div.container { width: 12rem; height: 7rem; border: 1px solid black; margin-right: 3rem; margin-bottom: 2rem;}
            div.box { width: 12rem; height: 7rem; border: 1px solid black; background-color: lightblue; padding: 2px; }
            
            div.container:nth-of-type(1) > div.box { transform: translate(10px,10px); }
            div.container:nth-of-type(2) > div.box { transform: scale(1.1, 0.8); }
            div.container:nth-of-type(3) > div.box { transform: rotate(10deg); }
            div.container:nth-of-type(4) > div.box { transform: skewX(-15deg); }
            div.container:nth-of-type(5) > div.box { transform: perspective(150px) rotateY(30deg); }
            div.container:nth-of-type(6) > div.box { transform: perspective(500px) rotateY(30deg); }
            div.container:nth-of-type(7) > div.box { transform: matrix(1, 0.4, -0.5, 0.5, 40, 40); }
        </style>
    </head>
    <body>
        <div class="container"><div class="box">平移函数:<br/>translate(10px,10px)</div></div>
        <div class="container"><div class="box">缩放函数:<br/>scale(1.1, 0.8)</div></div>
        <div class="container"><div class="box">旋转函数:<br/>rotate(10deg)</div></div>
        <div class="container"><div class="box">倾斜函数:<br/>skewX(-15deg)</div></div>
        <div class="container"><div class="box">视距函数:<br/>perspective(150px) rotateY(30deg)</div></div>
        <div class="container"><div class="box">视距函数:<br/>perspective(500px) rotateY(30deg)</div></div>
        <div class="container"><div class="box">矩阵函数:<br/>matrix(1, 0.4, -0.5, 0.5, 40, 40)</div></div>
    </body>
</html>
```

## §13.2 移动原点(`transform-origin`)

`transform-origin`用于指定变形的原点，接受二到三个参数，分别表示X、Y、Z（如果有的话）的偏移量。其中前两个参数可以接受`[left|center|right|top|bottom]`英文关键字。缺省为`50% 50%`。

```html
<html>
<head>
    <style>
        .container {
            float: left;
            margin: 45px;
            border: 1px solid red;
        }
        .container > * {
            height: 150px;
            width: 150px;
            border: 1px solid gray;
            text-align: center;
            background: linear-gradient(0deg, rgba(255, 255, 255, 0.6), rgba(165, 175, 255, 0.6));
            transform: rotate(30deg);
        }
        .container:nth-of-type(1) > div { transform-origin: 0 0; } 
        .container:nth-of-type(2) > div { transform-origin: 0 100%; } 
        .container:nth-of-type(3) > div { transform-origin: 100% 0; } 
        .container:nth-of-type(4) > div { transform-origin: 100% 100%; } 
        .container:nth-of-type(5) > div { transform-origin: 75% 25%; } 
        .container:nth-of-type(6) > div { transform-origin: 25% 75%; } 
        .container:nth-of-type(7) > div { transform-origin: 50% 100%; } 
        .container:nth-of-type(8) > div { transform-origin: 50% 50%; } 
    </style>
</head>
<body>
    <div class="container"><div>Origin 0 0</div></div>
    <div class="container"><div>Origin 0 100%</div></div>
    <div class="container"><div>Origin 100% 0</div></div>
    <div class="container"><div>Origin 100% 100%</div></div>
    <div class="container"><div>Origin 75% 25%</div></div>
    <div class="container"><div>Origin 25% 75%</div></div>
    <div class="container"><div>Origin 50% 100%</div></div>
    <div class="container"><div>Origin 50% 50%</div></div>
</body>
</html>
```

## §13.3 3D变形方式(`transform-sytle`)

| `transform-style`属性值 | 作用                                                   |
| ----------------------- | ------------------------------------------------------ |
| `flat`(缺省)            | 只使用X、Y轴，视觉效果永远扁平，就像是印在父元素上一样 |
| `preserve-3d`           | 加入Z轴，将父元素的3D变形也考虑进去                    |

```html
<html>
<head>
    <style>
        body { padding: 3rem; display: flex; }
        .container {
            width: 20rem;
            height: 10rem;
            border: 1px solid black;
            background-color: rgba(250, 235, 215, 0.8);
            margin-right: 5rem;
        }
        .container > .item {
            width: 10rem;
            height: 5rem;
            border: 1px solid black;
            background-color: rgba(173, 216, 230, 0.5);
            margin: 1rem auto 1rem auto;
        }
        .container { transform: perspective(500px) rotateY(60DEG) rotateX(-20deg); }
        .container > .item {
            transform-style: preserve-3d;
            transform: perspective(500px) translateZ(60px) rotateX(45deg);
        }
        span {
            color: red;
            font-weight: bolder;
            text-decoration: underline;
        }
        .container:nth-of-type(1) {transform-style: flat; }
        .container:nth-of-type(2) {transform-style: preserve-3d; }
    </style>
</head>
<body>
    <div class="container">
        Outer (transform-style: <span>flat</span>;)
        <div class="item">
            Inner
        </div>
    </div>
    <div class="container">
        Outer (transform-style: <span>preserve-3d</span>);
        <div class="item">
            Inner
        </div>
    </div>
    <script>
        const innerDOMs = document.querySelectorAll('.item');
        let innerDOMRotateX = 0;
        innerDOMs.forEach(element => {
            setInterval(() => {
                element.style['transform'] = `perspective(750px) translateZ(60px) rotateX(${innerDOMRotateX}deg)`;
                innerDOMRotateX += 2;
                innerDOMRotateX %= 360;
            }, 33);
        });

        const outerDOMs = document.querySelectorAll('.container');
        let outerDOMRotateY = 0;
        outerDOMs.forEach(element => {
            setInterval(() => {
                element.style['transform'] = `perspective(500px) rotateY(${outerDOMRotateY}deg) rotateX(-20deg)`;
                outerDOMRotateY += 0.5;
                outerDOMRotateY %= 360;
            }, 30);
        });
    </script>
</body>
</html>
```

> 注意：为了避免3D变形效果被覆盖，需要保证以下属性为其默认值：
>
> - `overflow: visible`
> - `filter: none`
> - `clip: auto`
> - `clip: path`
> - `mask-image: none`
> - `mask-border-source: none`
> - `mix-blend-mode: normal`

## §13.4 视域(`perspective`/`perspective-origin`)

在[§13.1 变形函数(`transform`)](##§13.1 变形函数(`transform`))一节中，我们提过`perspective()`可以用于定义视域的距离。初次以外，CSS还提供了`perspective`属性，用于修改视域的距离；也提供了`perspective-origin`属性，用于定义视域的原点。

变形函数`perspective()`和`perspective`属性的区别在于：变形函数的作用域仅限其修饰的元素，而属性的作用范围包括其修饰的子元素，使其作为一个整体旋转，每个子元素的最终视觉效果互不相同，组合起来更能体现出整体的一面。

```html
<html>
<head>
    <style>
        body { padding: 2rem; display: flex; }
        .container {
            display: flex;
            width: 25rem;
            height: 8rem;
            border: 1px solid black;
            background-color: rgba(250, 235, 215, 0.8);
            margin-right: 5rem;
            margin-bottom: 3rem;
            transform-style: preserve-3d;
        }
        .container > .item {
            width: 7rem;
            height: 5rem;
            border: 1px solid black;
            background-color: rgba(173, 216, 230, 0.5);
            margin: 1rem auto 1rem auto;
        }
        
        body > div:nth-of-type(1) > .container > .item {
            transform: perspective(400px) rotateX(60deg);
        }
        body > div:nth-of-type(2) > .container { perspective: 400px; }
        body > div:nth-of-type(2) > .container > .item {
            transform: rotateX(60deg);
        }
    </style>
</head>
<body>
    <div>
        Outer - transform: perspective(400px); - 各转各的
        <div class="container">
            <div class="item"></div>
            <div class="item"></div>
            <div class="item"></div>
        </div>
    </div>
    <div>
        Outer - perspective: 400px; - 作为一个整体旋转
        <div class="container">
            <div class="item"></div>
            <div class="item"></div>
            <div class="item"></div>
        </div>
    </div>
    <script>
        const outerDOMs = document.querySelectorAll('.container');
        let outerDOMRotateY = 0;
        outerDOMs.forEach(element => {
            setInterval(() => {
                element.style['transform'] = `perspective(400px) rotateY(${outerDOMRotateY}deg) rotateX(-20deg)`;
                outerDOMRotateY += 0.5;
                outerDOMRotateY %= 360;
            }, 30);
        });
    </script>
</body>
</html>
```

视域的原点表示一点透视的消隐点（Vanishing Point），同时也表示着观察者的位置。在观察者能观察到的二维投影上，设想消隐点发出的无数条光线构成一个圆锥体。以`perspective`属性的长度，沿物体平面向外平移，得到一个新的平行平面，记为观察者眼睛的移动范围。现在随意选定物体平面上的一点，尝试在平行平面中移动视角，使得纸面上选定的点与消隐点重合，即为最终的视图。其属性值语法与`transform-origin`完全一致，以元素的左上角为原点，通过指定消隐点在X、Y、Z(如果有的话)的偏移量。详见[MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/perspective-origin)。

## §13.5 背面可见性(`backface-visibility`)

`backface-visibility`属性只有两个取值——`visible`和`hidden`，决定元素背面面朝观察者时是否渲染。

```html
<html>
<head>
    <style>
        body { padding: 3rem; display: flex; }
        .container {
            width: 20rem;
            height: 10rem;
            border: 1px solid black;
            background-color: rgba(250, 235, 215, 0.8);
            margin-right: 5rem;
        }
        .container > .item {
            width: 10rem;
            height: 5rem;
            border: 1px solid black;
            background-color: rgba(173, 216, 230, 0.5);
            margin: 1rem auto 1rem auto;
        }
        .container { 
            transform: perspective(500px) rotateY(60DEG) rotateX(-20deg);
            backface-visibility: hidden;
        }
        .container > .item {
            transform-style: preserve-3d;
            transform: perspective(500px) translateZ(60px) rotateX(45deg);
        }
        span {
            color: red;
            font-weight: bolder;
            text-decoration: underline;
        }
        .container:nth-of-type(1) {transform-style: flat; }
        .container:nth-of-type(2) {transform-style: preserve-3d; }
    </style>
</head>
<body>
    <div class="container">
        Outer (transform-style: <span>flat</span>;)
        <div class="item">
            Inner
        </div>
    </div>
    <div class="container">
        Outer (transform-style: <span>preserve-3d</span>);
        <div class="item">
            Inner
        </div>
    </div>
    <script>
        const innerDOMs = document.querySelectorAll('.item');
        let innerDOMRotateX = 0;
        innerDOMs.forEach(element => {
            setInterval(() => {
                element.style['transform'] = `perspective(750px) translateZ(60px) rotateX(${innerDOMRotateX}deg)`;
                innerDOMRotateX += 2;
                innerDOMRotateX %= 360;
            }, 33);
        });

        const outerDOMs = document.querySelectorAll('.container');
        let outerDOMRotateY = 0;
        outerDOMs.forEach(element => {
            setInterval(() => {
                element.style['transform'] = `perspective(500px) rotateY(${outerDOMRotateY}deg) rotateX(-20deg)`;
                outerDOMRotateY += 0.5;
                outerDOMRotateY %= 360;
            }, 30);
        });
    </script>
</body>
</html>
```

利用这一特性，我们可以实现旋转翻页的效果。在下面的代码中，`div.info`翻转了180度，因此被隐藏了起来，从而让`img`显示。当鼠标移动到`.container`时，整体翻转了180度，导致`div.info`翻转了0度，因此尝试显示，同时图片发生了镜像翻转到后面。又因为开启了3D旋转，因此`div.info`在图层最前面，配上透明度背景，从而实现这一效果。

```html
<html>
<head>
    <style type="text/css">
        .container {
            position: relative; /* 使得 。container > * {position: absolute;} 找到参照物 */
            border: 1px solid black;
            width: 250px;
            height: 250px;
        }
        .container > * { position: absolute; top: 0; left: 0; }
        .container > div.info {
            width: 100%;
            height: 100%;
            padding: 1rem;
            box-sizing: border-box;

            transform: rotateY(180deg);
            backface-visibility: hidden;
            background: rgba(255, 255, 255, 0.9);
        }
        .container:hover {
            transform: rotateY(180deg);
            transform-style: preserve-3d;
        }
        </style>
</head>
<body>
    <div class="container">
        <img src="https://meyerweb.github.io/csstdg4figs/16-transforms/img/box-salmon.png">
        <div class="info">
            <h3>这是介绍文字</h3>
        </div>
    </div>
</body>
</html>
```

# §14 过渡

CSS的过渡能控制属性在一段时间内，从一个值缓慢变为另一个值，使得样式变化得更自然一些。

## §14.1 定义过渡的属性

### §14.1.1 过渡属性(`transition-property`)

`transition-property`属性用于指定应用过渡效果的CSS属性名称。数据值可以接受数个用逗号分隔的CSS属性，也可以作用于所有CSS属性`all`(缺省)，也可以禁用所有CSS属性(`none`)。

```html
<html>
    <head>
        <style type="text/css">
            body {
                padding: 10rem;
            }
            div {
                color: #ff0000;
                border: 2px solid #00ff00;
                border-radius: 0;
                transform: scale(1) rotate(0deg);
                transform-origin: center center;
                opacity: 1;
                box-shadow: 3px 3px rgba(0, 0, 0, 0.1);
                width: 50px;
                padding: 100px;
            }
            div:hover {
                color: #000000;
                border: 5px dashed #000000;
                border-radius: 50%;
                transform: scale(2) rotate(-10deg);
                opacity: 0.5;
                box-shadow: -3px -3px rgba(255, 0, 0, 0.5);
                width: 100px;
                padding: 20px;
                background-color: lightblue;

                transition-property: all;
                transition-duration: 500ms;
            }
        </style>
    </head>
    <body>
        <div>Hello</div>
    </body>
</html>
```

### §14.1.2 过渡持续时间(`transition-duration`)

`transition-duration`属性用于指定**从其它状态到当前状态**（而不是反过来）的用时，单位为`s`或`ms`，属性值是若干个用逗号分隔的数字。缺省值为`0s`。

```html
<html>
    <head>
        <style type="text/css">
            div { background-color: lightblue; width: 10rem; height: 5rem; border-radius: 2rem; text-align: center; line-height: 5rem; }
            div {
                width: 10rem;
                transition-duration: 0.05s;
            }
            div:hover {
                width: 30rem;
                transition-duration: 0.4s;
            }
        </style>
    </head>
    <body>
        <div>Hello</div>
    </body>
</html>
```

`transition-property`和`transition-duration`配套使用，可以为不同的CSS属性指定不同的过渡持续时间：

```css
div:hover {
    transition-property: all, color, border, border-radius;
	transition-duration: 5ms, 10ms, 20ms, 40ms;
}
```

> 注意：实践中经常把`all`放在`transition-property`的最前面，防止覆盖后面的CSS属性。

### §14.1.3 过渡内部时序(`transition-timing-function`)

`transition-timing-function`接受一个表示数值变化速率的函数`<easing-function>`。

| `<easing-function>`(部分值) | 等价的三次贝塞尔曲线               | 作用                                                         |
| --------------------------- | ---------------------------------- | ------------------------------------------------------------ |
| `cubix-bezier(x,y,z)`       |                                    | 指定一个三次贝塞尔曲线                                       |
| `ease`                      | `cubic-bezier(0.25, 0.1, 0.25, 1)` | 慢快慢                                                       |
| `linear`                    | `cubic-bezier(0, 0, 1, 1)`         | 匀速                                                         |
| `ease-in`                   | `cubic-bezier(0.42, 0, 1, 1)`      | 慢快                                                         |
| `ease-out`                  | `cubic-bezier(0, 0, 0.58, 1)`      | 快慢                                                         |
| `ease-in-out`               | `cubic-bezier(0.42, 0, 0.58, 1)`   | 快慢快                                                       |
| `step-start`                |                                    | 在开始时完成突变                                             |
| `step-end`                  |                                    | 在接受时完成突变                                             |
| `step(n, start)`            |                                    | 将时间均分成$n$等份，每份时间内保持不变，时间段两端突变。`start`表示开始时的正导数为+∞，结束时的负导数为0。 |
| `step(n, end)`              |                                    | 将时间均分成$n$等份，每份时间内保持不变，时间段两端突变。`end`表示开始时的正导数为0，结束时的负导数为+∞。 |

CSS标准预置的`<easing-function>`远远没有发挥出贝塞尔曲线的全部能力。[easings.net](https://easings.net/zh-cn#)提供了更多的预设。

当过渡完成后，想退回起点时，设正向位置与时间的函数为$f(t)$，则退回的函数为$1-f(1-t)$。

### §14.1.4 延迟过渡(`transition-delay`)

`transition-delay`属性用于指定触发过渡与过渡开始之间的时间间隔，缺省值为`0s`。

当`trainsition-delay`列出的值多于`transition-property`，则多出的值将会被忽略。反之如果少于，则会重复使用最后一个延时值。

如果延时值为负，那么当触发过渡时，过渡不会从头(`0s`)开始，而是从延时值的绝对值的那一时刻开始。

### §14.1.5 延时总属性(`transition`)

`transition`是以上四个子属性的复合体，语法为`[<transition-property>? <transition-duration> <transition-timing-function> <transition-delay>]* (用逗号分隔) | none`。

## §14.2 过渡事件

当某个CSS子属性过渡结束后（包括过渡结束与过渡完全恢复初始值），都会出发`transtitionend`事件。这里的子属性指的是最小单位的属性（例如`background-color`），而不是由众多子属性缝合成的大属性（例如`background`）。

`transitionend`事件通过如下方式监听：

```javascript
document.querySelector('...').addEventListener('transitionend',
	(e) => {console.log(e);} /* TransitionEvent实例 */
)
```

| `TransitionEvent`实例属性(部分) | 属性值取值范围                | 作用                     |
| ------------------------------- | ----------------------------- | ------------------------ |
| `propertyName`                  | `string`                      | 过渡结束的CSS属性名称    |
| `pesudoElement`                 | `"::after"`/`"::before"`/`""` | 应用过渡效果的伪元素     |
| `elapsedTime`                   | `number`                      | 过渡持续的时间，单位为秒 |

## §14.3 支持动画的属性

过渡属性(`transition-property`)中可以使用的属性有很多，他们有一个共同点——都能插值。如果这个属性能找到两个值的中间点，那么该属性就大概率支持动画。对于由众多子属性构成的大属性而言，只要其中有一个子属性能插值，那么这个大属性就能插值。

详见[MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_animated_properties#%E6%97%A0%E5%8A%A8%E7%94%BB%E6%80%A7)。

# §15 动画

前文提到的过渡是一种简单的动画，我们只能控制起始和终止状态，没有办法单独控制过渡进行时的属性。而CSS动画允许开发者有更多控制权，但需要开发者显式声明关键帧。

## §15.1 关键帧块(`@keyframes`)

`@keyframes`块用于定义可重复使用的关键帧块，用动画标识符进行标识。每个关键帧块包含若干个关键帧选择符，表示动画持续时间内的时间点，可以是`from`/`to`或百分数。其语法大致如下所示：

```css
@keyframes 动画标识符 {
	关键帧选择符[, 关键帧选择符, ......] {
    	CSS属性: ...;
        CSS属性: ...;
    }
	关键帧选择符[, 关键帧选择符, ......] {
    	CSS属性: ...;
        CSS属性: ...;
    }
    ......
}
```

动画标识符的命名规则有点复杂：

1. 动画标识符使用的字符只能从正则表达式`[A-Za-z0-9\-_\u00A0-\u10FFFF]`中选取。
2. 特殊字符（`!@#$%&*`等等）必须使用反斜杠`\`转义。
3. 动画标识符不能以数字开头。
4. 动画标识符不能以两个连续的连字符`-`开头。
5. 动画标识符如果以一个连字符`-`开头，则后面不能紧跟数字字符，除非对数字字符用反斜杠`\`转义。
6. 动画标识符推荐不要与关键字重名。虽然CSS没有明令禁止，但浏览器可能无法识别。

关键帧选择符表示在这一个或多个时刻，给元素附加上如下状态。

```html
<html>
    <head>
        <style type="text/css">
            div { background-color: lightblue; width: 10rem; height: 3rem; border-radius: 2rem; text-align: center; line-height: 3rem; }
            @keyframes BreathePurpleLight {
                from, 10% {
                    background-color: #ff00ff;
                }
                90%, to {
                    background-color: purple
                }
            }
            
            div {
                animation: BreathePurpleLight infinite 2s alternate;
            }
        </style>
    </head>
    <body>
        <div>呼吸灯效果</div>
    </body>
</html>
```

如果没有显式指定`from`/`0%`和`end`/`100%`，那么首尾的效果都会以元素本身的属性为准。例如下面两个动画应用到`<div>`时完全等价：

```css
div { color: blue; }
@keyframes A {
    33% { color: red; }
    66% { color: green; }
}
@keyframes B {
    0% { color: blue; }
    33% { color: red; }
    66% { color: green; }
    100% { color: blue; }
}

/* 下面两种方案完全等价 */
div { animation: A infinite 2s alternate; }
div { animation: B infinite 2s alternate; }
```

如果关键帧选择符被多次声明，则采取覆盖原则：先声明、无冲突的属性得以保留，后声明、有冲突的属性会覆盖先声明的属性。

### §15.1.1 使用动画(`animation-name`)

`animation-name`属性用于指定元素使用的动画名称。由若干个用逗号分隔的名称组成，缺省为`none`。

> 注意：如果其中的一个动画名称不存在，则仅忽略动画，其它动画照常工作。
>
> 如果通过脚本中途添加了动画，使其又存在了，那么自存在的这一刻起立即生效。

### §15.1.2 动画时长(`animation-duration`)

`animation-duration`属性规定动画运行一次所需的时间。由若干个用逗号分隔的时间组成，单位为`s`或`ms`。

> 注意：如果其中的一个时间非法（语法错误、`0s`/负数秒等），会导致这条CSS属性声明全部失效。

> 注意：如果同一时刻有多个动画规则控制同一个CSS属性，导致引起冲突，那么就按照`animation-name`中目前正在生效的、排名顺序最后的规则为准。
>
> 例如在下面的例子中，前`2s`都是`blue`在生效，`2s~5s`内是`green`生效，`5s~10s`由`red`生效。
>
> ```css
> @keyframes red { from, to { color: red; } }
> @keyframes green { from, to { color: green; } }
> @keyframes blue { from, to { color: blue; } }
> div {
> 	animation-name: red, green, blue;
>     animation-duration: 10s, 5s, 2s;
> }
> ```

### §15.1.3 动画迭代次数(`animation-iteration-count`)

`animation-iteration-count`属性用于规定动画的重复次数，属性值为`[<number>|infinite]* (用逗号分隔)`，缺省值为1。

> 注意：如果其中的一个数字非法（例如负数），该数字会被重置为1。

当动画迭代次数为正的浮点数时，动画在最后一次迭代时不会进行到关键帧的`100%`，而是会在中途无限停下来，同时触发`animationend`事件，就像是`animation-play-state: pause`一样。

### §15.1.4 动画方向(`animation-direction`)

`animation-direction`决定了各个关键帧播放的次序，属性值为下列表格关键字中的一个或多个，用逗号分隔。缺省值为1。

| `animation-direction`属性值 | 作用                                                         |
| --------------------------- | ------------------------------------------------------------ |
| `normal`(缺省)              | 动画每次迭代都从`0%`关键帧到`100%`关键帧                     |
| `reverse`                   | 动画每次迭代都从`100%`关键帧到`0%`关键帧                     |
| `alternate`                 | 动画奇数次迭代从`0%`关键帧到`100%`关键帧，偶数次迭代从`100%`关键帧到`0%`关键帧 |
| `alternate-reverse`         | 动画奇数次迭代从`100%`关键帧到`0%`关键帧，偶数次迭代从`0%`关键帧到`100%`关键帧 |

### §15.1.5 动画延迟(`animation-delay`)

`animation-delay`决定了动画附加到元素上之后，经过多长时间才开始播放第一次迭代。由若干个用逗号分隔的时间组成，单位为`s`或`ms`。

### §15.1.6 动画内部时序(`animation-timing-function`)

`animation-timing-function`属性负责控制动画的内部时许，其属性值取值与[§14.1.3 过渡内部时序(`transition-timing-function`)](###§14.1.3 过渡内部时序(`transition-timing-function`))类似。

```html
<html>
    <head>
        <style>
            @keyframes progressBar {
                from {width: 20rem;}
                to {width: 3rem;}
            }
            div {
                width: 20rem;
                height: 0.5rem;
                margin-bottom: 0.2rem;
                background-color: black;
                animation: progressBar 2s cubic-bezier(0,4,1,-4) both;
            }
        </style>
    </head>
    <body>
        <script>
            const bodyDOM = document.getElementsByTagName('body')[0];
            const bodyDOMContent = Array.from({length: 40}, (_,index)=>{return index;}).map((index, _)=>{
                let animationIterationCount = (index / 40).toFixed(2);
                return `<div style="animation-iteration-count: ${animationIterationCount}"></div>`;
            }).join("");
            bodyDOM.innerHTML = bodyDOMContent;
        </script>
    </body>
</html>
```

`animation-timing-function`也可以在关键帧中说明，只作用于该关键帧声明的其它属性。当动画运行到此关键帧时，里面的内部时序函数将会覆盖原有的，并一致保持下去，直到遇到下一个内部时序函数。

```css
@keyframes demo {
    from { left:0; top:0; }
    33% { left: 100px; animation-timing-function: ease-in; }
    66% { top: 200px;}
    66% { animation-timing-function: ease-in; } /* 无效，因为关键帧中没有其它属性*/
}
```

### §15.1.7 动画播放状态(`animation-play-state`)

`animation-play-state`属性用于控制动画的播放与暂停（包括`animation-delay`延时的计时），对应的属性值分别是`running`(缺省)和`paused`，可以接受多个用逗号分隔的属性值。

```html
<html>
    <head>
        <style>
            @keyframes progressBar {
                from {width: 20rem;}
                to {width: 3rem;}
            }
            div {
                width: 20rem;
                height: 0.5rem;
                background-color: black;
                animation: progressBar 5s cubic-bezier(0,4,1,-4) both;
            }
            div:hover {
                animation-play-state: paused; /* 鼠标悬停时暂停动画 */
            }
        </style>
    </head>
    <body>
        <script>
            const bodyDOM = document.getElementsByTagName('body')[0];
            const bodyDOMContent = Array.from({length: 40}, (_,index)=>{return index;}).map((index, _)=>{
                let animationIterationCount = 'infinite';
                return `<div style="animation-iteration-count: ${animationIterationCount}"></div>`;
            }).join("");
            bodyDOM.innerHTML = bodyDOMContent;
        </script>
    </body>
</html>
```

> 注意：如果在播放动画时赋予`display: none`属性，则这一瞬间的动画状态全部遗忘，就算让`display`恢复正常，动画也不会继承上次的状态继续播放，而是从头开始。

### §15.1.8 动画填充模式(`animation-fill-mode`)

`animation-fill-mode`属性用于控制动画播放结束后是否使用原来的属性值，接受若干个由逗号分隔的属性值。

| `animation-fill-mode`属性值 | 作用                                                     |
| --------------------------- | -------------------------------------------------------- |
| `none`(缺省)                | 只有在动画播放时，才应用关键帧的属性                     |
| `forwards`                  | 在`animationend`事件触发之后，也应用`to`关键帧的属性     |
| `backwards`                 | 在`animationstart`事件触发之前，就应用`from`关键帧的属性 |
| `both`                      | `forwards`和`backwards`的结合体                          |

```html
<html>
    <head>
        <style>
            @keyframes progressBar {
                from {width: 20rem;}
                to {width: 3rem;}
            }
            div {
                width: 20rem;
                height: 0.5rem;
                background-color: black;
                animation: progressBar 5s cubic-bezier(0,4,1,-4) both;
            }
            div:hover {
                animation-play-state: paused;
            }
        </style>
    </head>
    <body>
        <script>
            const bodyDOM = document.getElementsByTagName('body')[0];
            const bodyDOMContent = Array.from({length: 40}, (_,index)=>{return index;}).map((index, _)=>{
                let animationIterationCount = 'infinite';
                return `<div style="animation-iteration-count: ${animationIterationCount}"></div>`;
            }).join("");
            bodyDOM.innerHTML = bodyDOMContent;
        </script>
    </body>
</html>
```

### §15.1.9 动画总属性(`animation`)

`animation`属性是前八个子属性的合集，语法为：`[<animation-name> <animation-duration> <animation-timing-function> <animation-delay> <animation-iteration-count> <animation-direction> <animation-fill-mode> <animation-play-state> ,](用逗号分隔)`，初始值为`0s ease 0s 1 normal none running none`。

该属性的简写规则如下：

- 如果有两个时间相关的属性值，那么前一个被判定为`<animation-duration>`，第二个被判定为`<animaiton-delay>`。

- 如果`<animation-name>`与某个关键字重名，那么应该放在最后，否则会被当成关键字解析。（`none`除外，`<animation-name>`不能与`none`这个关键字重名）

- 如果`<animation-name>`与某个子属性的属性值格式一致，除了放在最后以外，还需要使用转义符。

  例如：`@keyframes 4s`，就不能`animaiton: 1s 2s 4s`。因为`4s`的数字在前，不是有效的标识符，需要改为`animation: 1s 2s \4s`。

## §15.2 关键帧对象与事件

Chrome提供的JavaScript运行时包含了完善的关键帧API。

一个`@keyframes`关键帧块对应着一个`CSSKeyframesRule`实例，包含若干个表示关键帧的`CSSKeyframeRule`实例。关键帧`CSSKeyframeRule`实例提供了`appendRule(str)`、`deleteRule(str)`、`findRule(str)`这三个API，用于实现增删查改。

```javascript
<html>
    <head>
        <style type="text/css">
            @keyframes BreathePurpleLight {
                from, 10% {
                    background-color: #ff00ff;
                }
                90%, to {
                    background-color: purple
                }
            }
            div { background-color: lightblue; width: 10rem; height: 3rem; border-radius: 2rem; text-align: center; line-height: 3rem; }
            div {
                animation: BreathePurpleLight infinite 2s alternate;
            }
        </style>
    </head>
    <body>
        <div>Hello</div>
        <script>
            // 获取关键帧块
            let keyframesObject = document.styleSheets[0].cssRules[0]; /* CSSKeyframesRule实例 */
            console.log(keyframesObject);
            
            // 获取关键帧
            let keyframesRule = keyframesObject.findRule('from,10%'); /* CSSKeyframeRule实例 */
            console.log(keyframesRule.cssText)
            
            // 删除关键帧
            keyframesObject.deleteRule('from, 10%');

            // 添加关键帧
            keyframesObject.appendRule('50% {background-color: darkgreen;}')
        </script>
    </body>
</html>
```

CSS的动画有以下三个事件，均对应着同一类`AnimationEvent`的实例，不同事件之间由其`type`属性区分：

| CSS动画事件名称<br/>`AnimationEvent.type`值 | 事件含义                                                     |
| ------------------------------------------- | ------------------------------------------------------------ |
| `animationstart`                            | 动画的开始。若`animation-delay`为负值，则立即触发。          |
| `animationend`                              | 动画的结束。若`animation-iteration-count: infinite`且`animation-duration`为正，则永远不触发。 |
| `animationiteration`                        | 一次迭代结束与下一次迭代开始。要求实际上的一次迭代时间必须大于0。与`animationend`不会同时触发。 |

```javascript
<html>
    <head>
        <style type="text/css">
            @keyframes BreatheLight {
                from, 10% {
                    background-color: #ff00ff;
                }
                90%, to {
                    background-color: purple
                }
            }
            div { background-color: lightblue; width: 10rem; height: 3rem; border-radius: 2rem; text-align: center; line-height: 3rem; }
            div {
                animation: BreatheLight infinite 2s alternate;
            }
        </style>
    </head>
    <body>
        <div class="animated">Hello</div>
        <script>
            let animatedDOM = document.getElementsByClassName('animated')[0];
            animatedDOM.addEventListener('animationstart', (e) => {console.log(e);});
            animatedDOM.addEventListener('animationend', (e) => {console.log(e);});
            animatedDOM.addEventListener('animationiteration', (e) => {console.log(e);});
        </script>
    </body>
</html>
```

下面是一道测试题：请问三种动画时间分别触发了多少次？

```css
div {
    animation-name: xxx;
    animation-duration: 1s;
    animation-iteration-count: 4;
    animation-delay: -2s;
}
```

动画刚开始运行时，肯定有一次`animationstart`。每次迭代时，由于`animation-delay`为负值，且其绝对值大于`animation-duration`，所以每次迭代开始时就已经结束了，因此永远不会触发`animationiteration`时间。重复上述过程四次后，触发一次`animationend`事件。

## §15.3 动画链

在[过渡](###§14.1.1 过渡属性(`transition-property`))一节，我们直到可以让许多属性同时进行过渡，但是没有办法分批过渡。CSS动画提供的所有子属性，都支持多个值用逗号连在一起，本来也没有办法按顺序执行。但是通过精确设计`animation-duration`和`animation-delay`的关系，我们就能让下一个动画在前一个动画结束后立即开始。这种效果称为动画链。

### §15.3.1 基于`animation-delay`

下面是一个动画链的例子，其时间线如下所述：`red`动画会先延时`3s`，在第`3s`时执行`red`动画共计`1s`，在第`4s`时结束。恰巧`orange`动画已经延时了`4s`，正好开始运行了。依次类推，`animation-duration`和`animation-delay`两者之间必须满足恒等式：$\text{animation-delay}_{i+1}=\text{animation-delay}_i+\text{animation-duration}_i$。

```html
<html>
    <head>
        <style>
            @keyframes red {100%{background-color: red;}}
            @keyframes orange {100%{background-color: orange;}}
            @keyframes yellow {100%{background-color: yellow;}}
            @keyframes green {100%{background-color: green;}}
            @keyframes blue {100%{background-color: blue;}}
            div {
                width: 10rem;
                height: 5rem;
                border: 2px black solid;
            }
            div:nth-of-type(1) {
                animation-name: red, orange, yellow, blue, green;
                animation-duration: 1s, 3s, 5s, 7s, 11s;
                animation-delay: 3s, 4s, 7s, 12s, 19s;
            }
        </style>
    </head>
    <body>
        <div></div>
    </body>
</html>
```

### §15.3.2 基于JavaScript

使用JavaScript脚本实现动画链的思路大致是：给每个元素增加`animationend`的`EventListener`，触发时为下一个元素添加动画。

```html
<!DOCTYPE HTML>
<html>
    <head>
        <style>
            @keyframes red {100%{background-color: red;}}
            @keyframes orange {100%{background-color: orange;}}
            @keyframes yellow {100%{background-color: yellow;}}
            @keyframes green {100%{background-color: green;}}
            @keyframes blue {100%{background-color: blue;}}
            div {
                width: 10rem;
                height: 2rem;
                border: 2px black solid;
                margin-bottom: 0.5rem;
            }
            div {
                animation-duration: 1s;
            }
            div:nth-of-type(1) {
                animation-name: red;
            }
        </style>
    </head>
    <body>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
    </body>
    <script>
        const divDOMs = document.querySelectorAll('div');
        const keyframeStyles = document.styleSheets;
        divDOMs.forEach((element, index) => {
            element.addEventListener('animationend', (event)=>{
                let animationName = [0,1,2,3,4].map((index) => {return document.styleSheets[0].rules[index].name;})[index + 1];
                document.querySelectorAll('div')[index + 1].style.animationName = animationName;
            })
        });
    </script>
</html>
```

> 注意：当动画正在执行时，除了`animation-name`以外，更改动画的所有属性都不会终止动画的执行。

## §15.4 动画延时迭代

我们知道，`animation-iteration-count`仅仅是对单次动画的多次重复，这导致各个关键帧状态的出现具有周期性。如果我们需要打破这种周期性，例如“红一秒、白两秒、红三秒、白四秒”，应该怎么办呢？本节我们试着探讨这一问题，并提出几种解决方案。

1. 把所有动作压缩在一个动画中

   ```css
   @keyframes demo {
       from, 10% { color: red; } /* 10秒的0%~10%为一秒 */
       10.1%,30% { color: white; } /* 以此类推... */
       30.1%,60% { color: red; }
       60.1%,to  { color: white; }
   }
   div {
   	animation-name: demo;
       animation-duration: 10s; /* 总长度为10秒 */
   }
   ```

2. 取时间的最大公因数，创建若干个动画构建动画链

   ```css
   @keyframes red { from,to {color: red;} }
   @keyframes white { from,to {color: white;} }
   div {
       animation-name: red, white, white, red, red, red, white, white, white, white;
       animation-delay: 0s, 1s, 2s, 3s, 4s, 5s, 6s, 7s, 8s, 9s;
   }
   ```

3. 使用JavaScript监听`animationend`事件，动态地增加和删除动画

   ```javascript
   let divDOM = document.querySelectorAll('div')[0];
   
   /* 只控制动画之间的间隔，动画执行时长由其自己控制 */
   div.addEventListener('animationend', (event)=>{
       let delay = parseInt(Math.random() * 2000);
   	div.classList.remove('CSS名称'); /* 进入动画的延时 */
       setTimeout(()=>{
       	div.classList,add('CSS名称'); /* 开始动画 */
       }, delay);
   });
   ```

## §15.6 动画性能调优

市面上大部分浏览器将动画的计算放在GPU中执行，其它任务放在CPU的UI线程中。如果存在视觉属性，它不属于动画，而是其它非动画的地方，那么它就会占用CPU，导致视觉效果卡顿。

对于透明度、3D变换来说，推荐把这些任务交给GPU。对于`top`/`right`/`bottom`/`left`/`visibility`而言，它们的改变会导致盒模型的重排和重绘，推荐交给CPU。

```css
/* CPU */
div { transform: translateZ(0); }

/* GPU */
@keyframes demo {
    from { transform: translateZ(0); }
}
```

## §15.7 减少动画(`prefers-reduced-motion`)

2017年年底，CSS标准增加了一个名为`prefers-reduced-motion`的媒体查询，旨在减少动画的出现，避免癫痫和晕动病。浏览器会根据用户代理，选择是否启用该媒体查询中的CSS属性。

```css
@media (prefers-reduced-motion){
    * {
    	animation: none !important;
        transition: none !important;
    }
}
```

# §16 图像处理

## §16.1 滤镜

十多年以前，微软的Internet Explorer首次为CSS引入`filter`属性，用于实现DirectX效果。后来CSS也创建并通过了这一提案。

`filter`属性用于增加滤镜，接受若干个用逗号分隔的函数：

| `filter()`属性值                  | 作用                                                   |
| --------------------------------- | ------------------------------------------------------ |
| `none`(缺省)                      |                                                        |
| `blur(<length>)`                  | 高斯模糊                                               |
| `brightness()`                    | 控制元素的亮度                                         |
| `contrast()`                      | 控制元素的对比度                                       |
| `drop-shadow()`                   | 沿元素的`alpha`通道创建形状一致的投影                  |
| `grayscale()`                     | 把元素的元素变为灰阶                                   |
| `hue-rotate()`                    | 把元素的颜色在色轮上旋转色相，同时保持饱和度和明度不变 |
| `invert()`                        | 把元素的颜色反相处理，取`255`的补数                    |
| `opacity(<number>, <percentage>)` | 不透明度                                               |
| `sepia()`                         | 把元素的所有颜色向红褐色转换(`rgb(112,66,20)`)         |
| `saturate()`                      | 控制元素的饱和度                                       |
| `url()`                           | SVG滤镜                                                |

```html
<html>
    <head>
        <style>
            body { padding: 1rem; }
            .item {
                width: 12rem;
                border: 1px solid black;
                border-radius: 1rem;
                padding: 1rem;
                margin-right: 1rem;
            }
            .item:hover { 
                transition: background-color 0.08s ease-in 0s; 
                background-color: #eee; 
            }
            .item > * {
                margin: 0.5rem auto 0.5rem auto;
                text-align: center;
            }
            .container {
                display: flex;
            }
            .img {
                width: 5rem;
                height: 5rem;
                background: linear-gradient(45deg, 
                    #5566ff 0%,#5566ff 33%, lightblue 33%, lightblue 66%, purple 66%, purple 100%
                );
            }
        </style>
    </head>
    <body>
        <section>
            <h1>基本滤镜</h1>
            <div class="container">
                <div class="item"><div class="img" style="filter: blur(3px);"></div><div>filter: blur(3px);</div></div>
                <div class="item"><div class="img" style="filter: opacity(0.2);"></div><div>filter: opacity(0.2);</div></div>
                <div class="item"><div class="img" style="filter: drop-shadow(5px 5px 4px #555555);"></div><div>filter: drop-shadow(5px 5px 4px gray);</div></div>
            </div>
        </section>
        <section>
            <h1>颜色滤镜</h1>
            <div class="container">
                <div class="item"><div class="img" style="filter: grayscale(0.9);"></div><div>filter: grayscale(0.9);</div></div>
                <div class="item"><div class="img" style="filter: sepia(0.6);"></div><div>filter: sepia(0.6);</div></div>
                <div class="item"><div class="img" style="filter: invert(0.8);"></div><div>filter: invert(0.8);</div></div>
                <div class="item"><div class="img" style="filter: hue-rotate(30deg);"></div><div>filter: hue-rotate(30deg);</div></div>
            </div>
        </section>
        <section>
            <h1>亮度、对比度、饱和度滤镜</h1>
            <div class="container">
                <div class="item"><div class="img" style="filter: brightness(1.4);"></div><div>filter: brightness(1.4);</div></div>
                <div class="item"><div class="img" style="filter: contrast(2.2);"></div><div>filter: contrast(2.2);</div></div>
                <div class="item"><div class="img" style="filter: saturate(3);"></div><div>filter: saturate(3);</div></div>
            </div>
        </section>
    </body>
</html>
```

### §16.1.1 基本滤镜

- `blur(<length>)`负责给元素使用高斯模糊。`<length>`表示高斯模糊使用的标准差，设为`0`时不生效，设为负数时无效。
- `opacity([<number>|<percentage>])`负责给元素使用不透明度。`0`为完全透明，`1`为完全不透明。当值大于`1`时，会自动取到上限`1`；当值小于`0`时无效。
- `drop-shadow(<length>{2,3} <color>?)`负责沿元素的`alpha`通道创建形状一致的投影。前两（三）个参数分别表示横向偏移、纵向偏移、（模糊半径）。`<color>`用于指定颜色，缺省为元素的CSS`color`属性。

### §16.1.2 颜色滤镜

- `grayscale([<number>]|<percentage>)`：把元素的元素变为灰阶。
- `sepia([<number>|<percentage>])`：把元素的所有颜色向红褐色转换(`rgb(112,66,20)`)。
- `invert([<number>|<percentage>])`：把元素的颜色反相处理，取`255`的补数。
- `hue-rotate(<angle>)`：把元素的颜色在色轮上旋转色相，同时保持饱和度和明度不变。

### §16.1.3 亮度、对比度、饱和度滤镜

## §16.2 合成与混合

将若干个元素合在一起，称为元素的合成。

### §16.3.1 混合元素(`mix-blend-mode`)

`mix-blend-mode`属性用于定义元素的混合方式。使用该属性的元素是前景，它的背景是位于该元素背后的元素，或者背后的元素背景。该属性的取值范围如下所示：

- `normal`(缺省)

  我们知道，无论是声明的先后顺序，还是`z-index`，其实元素之间在Z轴上都有着严格的先后顺序。我们遍历两个元素的同一位置的每一个像素，如果在该处坐标，两个元素的`alpha`值都为`1`，则意味着都不透明，直接选取Z轴层级最高的；如果前景元素`alpha`小于`1`，则进行线性混合。

- `darken`：各取RGB分量中最小的
  $$
  \overrightarrow{\text{image}'_{i,j}}=\left[\begin{matrix}
  	(\text{image}'_{i,j})_R \\ (\text{image}'_{i,j})_G \\ (\text{image}'_{i,j})_B
  \end{matrix}\right]=\left[\begin{matrix}
  	\min{((\text{background}_{i,j})_R, (\text{foreground}_{i,j})_R)} \\
  	\min{((\text{background}_{i,j})_G, (\text{foreground}_{i,j})_G)} \\
  	\min{((\text{background}_{i,j})_B, (\text{foreground}_{i,j})_B)} \\
  \end{matrix}\right]
  $$

- `brighten`：各取RGB分量中最大的
  $$
  \overrightarrow{\text{image}'_{i,j}}=\left[\begin{matrix}
  	(\text{image}'_{i,j})_R \\ (\text{image}'_{i,j})_G \\ (\text{image}'_{i,j})_B
  \end{matrix}\right]=\left[\begin{matrix}
  	\max{((\text{background}_{i,j})_R, (\text{foreground}_{i,j})_R)} \\
  	\max{((\text{background}_{i,j})_G, (\text{foreground}_{i,j})_G)} \\
  	\max{((\text{background}_{i,j})_B, (\text{foreground}_{i,j})_B)} \\
  \end{matrix}\right]
  $$

- `difference`：各取各取RGB分量中两者之差的绝对值
  $$
  \overrightarrow{\text{image}'_{i,j}}=\left[\begin{matrix}
  	(\text{image}'_{i,j})_R \\ (\text{image}'_{i,j})_G \\ (\text{image}'_{i,j})_B
  \end{matrix}\right]=\left[\begin{matrix}
  	\left|(\text{background}_{i,j})_R- (\text{foreground}_{i,j})_R)\right| \\
  	\left|(\text{background}_{i,j})_G- (\text{foreground}_{i,j})_G)\right| \\
  	\left|(\text{background}_{i,j})_B- (\text{foreground}_{i,j})_B)\right| \\
  \end{matrix}\right]
  $$

- `exclusion`：各取各取RGB分量，换算成位于`o~1`区间内的百分数，代入表达式$x+y-2xy$，而不是`difference`的$|x-y|$，将计算的结果转回`uint8`。这是`difference`的温和版本

- `multiply`：将`exclusion`的表达式换为$x\times y$

- `screen`：将`exclusion`的表达式换为$1-(1-x)\times(1-y)$

- `overlay`：如果**前景**在该点的像素RGB分量中的一个小于`128`（即百分比小于`50%`），则使用`multiply`；反之则使用`screen`

- `hard-light`：如果**后景**在该点的像素RGB分量中的一个小于`128`（即百分比小于`50%`），则使用`multiply`；反之则使用`screen`

- `soft-light`：`hard-light`的柔和版本，计算公式详见[维基百科](https://en.wikipedia.org/wiki/Blend_modes#Soft_Light)

- `color-dodge`：将`exclusion`的表达式换为$\displaystyle\frac{y}{1-x}$，颜色减淡

- `color-burn`：将`exclusion`的表达式换为$\displaystyle\frac{1-y}{x}$，颜色加深

- `hue`：取前景的色相角度、背景的明度和饱和度

- `saturation`：取前景的饱和度、背景的明度和色相角度

- `color`：取前景的色相角度和饱和度、背景的明度

- `luminosity`：取前景的明度、背景的色相角度和饱和度

### §16.3.2 混合背景(`background-blend-mode`)

前面我们提到的背景颜色取自于后面元素的颜色，本节将取自于后面元素的`background`。`background-blend-mode`的取值与上一节完全相同，这里不再赘述。

### §16.3.3 独立混合(`isolation`)

我们知道，`filter`属性是串联执行的，也就是说CSS会从最后一个值向前执行，最后构成一个$f=f_{n}(f_{n-1}(...(f_2(f_1))))$的总变换。然而有些属性不是串联执行的，而是各自执行完毕再混合起来，例如`background`，最后构成一个$f=f(f_1,f_2,...,f_{n-1},f_n)$的总变换，具有独立性。

`isolation`属性用于控制`filter`在图像处理时是否需要独立混合，是否需要排除父元素、后背元素等等的影响。"是"代表`isolate`，"否"代表`auto`(缺省)。

```html
<html>
    <head>
        <style>
            .container {
                padding: 1rem;
                background: linear-gradient(to top, goldenrod, skyblue);
                display: flex;
            }
            .container > * {
                margin: 1rem;
            }
            img { 
                height: 200px; 
                mix-blend-mode: difference; 
            }
            .container > div:nth-of-type(2) { 
                isolation: isolate; 
            }
        </style>
    </head>
    <body>
        <div class="container">
            <div><img src="https://meyerweb.github.io/csstdg4figs/19-filters-blending-clipping-masking/i/diamond.png"></div>
            <div><img src="https://meyerweb.github.io/csstdg4figs/19-filters-blending-clipping-masking/i/diamond.png"></div>
        </div>
    </body>
</html>
```

在上面的例子中，第一个图片经过了与后面的上级元素的差值处理，符合我们的预期。然而第二个图片的上级元素有`isolation: isolate`，因此将图片与`.container`隔离开来，从而显示原图。

## §16.3 裁剪和遮罩

虽然在`filter`中使用SVG滤镜也能实现裁剪，但是为方便起见，如果只需踩掉一部分，我们也可以使用`clip-path`属性。该属性用于定义裁切形状。

### §16.3.1 裁剪形状

`clip-path`可以用以下四个简单的形状函数定义——`inset()`、`circle()`、`ellipse()`、`polygon()`。它们已经在[§7.3.1 定义形状(`shape-outside`)](###§7.3.1 定义形状(`shape-outside`))详细解释过了，这里不再赘述。

```html
<html>
    <head>
        <style>
            .container {
                display: flex;
                flex-wrap: wrap;
            }
            .item {
                width: 15rem;
                margin: 0.5rem;
                border: 1px solid black;
                padding: 0.5rem;
            }
            .image {
                width: 100%;
                height: 7rem;
                border: 3px solid red;
                background: linear-gradient(45deg, purple, blue);
            }
            .text { text-align: center; }

            .item:nth-of-type(1) > .image { clip-path: none; }
            .item:nth-of-type(2) > .image { clip-path: inset(10px 0 25% 2rem); }
            .item:nth-of-type(3) > .image { clip-path: circle(5rem at 50% 50%); }
            .item:nth-of-type(4) > .image { clip-path: ellipse(10rem 3rem at 50% 50%); }
            .item:nth-of-type(5) > .image { clip-path: polygon(50% 0, 100% 50%, 50% 100%, 0 50%); }
            .item:nth-of-type(6) > .image { clip-path: circle(5rem at 50% 50%); }
        </style>
    </head>
    <body>
        <div class="container">
            <div class="item"><div class="image"></div><div class="text">clip-path:<br/>none;</div></div>
            <div class="item"><div class="image"></div><div class="text">clip-path:<br/>inset(10px 0 25% 2rem);</div></div>
            <div class="item"><div class="image"></div><div class="text">clip-path:<br/>circle(5rem at 50% 50%);</div></div>
            <div class="item"><div class="image"></div><div class="text">clip-path:<br/>ellipse(10rem 3rem at 50% 50%);</div></div>
            <div class="item"><div class="image"></div><div class="text">clip-path:<br/>polygon(50% 0, 100% 50%, 50% 100%, 0 50%);</div></div>
        </div>
    </body>
</html>
```

### §16.3.2 裁剪框

裁剪形状需要使用长度或百分比来指定顶点位置，而裁剪框直接对应盒模型的区域边界。

| `clip-path`属性值(裁剪框部分) | 作用                        |
| ----------------------------- | --------------------------- |
| `margin-box`                  | 恰好包含外边距的区域        |
| `border-box`                  | 恰好包含边框的区域          |
| `padding-box`                 | 恰好包含内边距的区域        |
| `content-box`                 | 恰好包含内容区的区域        |
| `view-box`                    | 使用最近的父辈元素的SVG视区 |
| `fill-box`                    | 使用SVG对象范围作为裁剪框   |
| `stroke-box`                  | 使用SVG描边范围作为裁剪框   |

> 注意：截止到Chrome 118，Chrome依然不支持上述属性。详见[MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/clip-path#%E6%B5%8F%E8%A7%88%E5%99%A8%E5%85%BC%E5%AE%B9%E6%80%A7)。

### §16.3.3 裁剪填充规则(`clip-rule`)

`clip-rule`用于控制裁剪路径交汇时创建裁剪形状的行为。

| `clip-rule`属性值 | 作用                           |
| ----------------- | ------------------------------ |
| `nonzero`         | 同样填充路径路径交汇的区域     |
| `evenodd`         | 当且仅当奇数次路径交汇时才填充 |

```html
<html>
	<head>
		<style>
			body {display: flex; justify-content: center;}
			ol {position: relative; list-style: none; margin: 0; padding: 0;}
			ol li {
                background: 
                    radial-gradient(rgba(255,255,255,0.5) 50%, white 75%),
                    linear-gradient(42deg, white 45%, teal, white 65%);
            }
			ol li img {width: 250px;}
			ol li b + * {display: block; width: 100%; text-align: center; font-size: 1.5em;}
		</style>
	</head>
	<body>
		<ol>
			<li><b><img src="https://meyerweb.github.io/csstdg4figs/19-filters-blending-clipping-masking/i/star-nonzero.svg"></b><code>nonzero</code></li>
			<li><b><img src="https://meyerweb.github.io/csstdg4figs/19-filters-blending-clipping-masking/i/star-evenodd.svg"></b><code>evenodd</code></li>
		</ol>
	</body>
</html>
```

## §16.4 蒙版

蒙版限定了一块区域，位于区域内部的内容可见，位于外部的区域隐藏起来。蒙版与裁剪区域的区别是：蒙版是通过图像定义的，而裁切区域是通过卢进共定义的。除此以外，蒙版提供的属性更多。

> 注意：截止到Chrome 118，Chrome对蒙版的支持已经仍不完善。大部分属性必须使用`-webkit-`前缀，有些属性甚至仍然不支持（例如`mask-mode`）。

### §16.4.1 定义蒙版(`mask-image`)

`mask-image`属性用于指定用作蒙版的图像。

```css
img.A { 
	mask-image: url(...) /* 图像B */
}
```

在上面的例子中，B图像的形状会限制A图像的显示范围。

### §16.4.2 蒙版模式(`mask-mode`)

`mask-mode`属性决定了蒙版图像到蒙版的生成方式，接受若干个用逗号分隔的属性值。

| `mask-mode`属性值    | 作用                                                         |
| -------------------- | ------------------------------------------------------------ |
| `alpha`              | 把蒙版的alpha通道当作图像的透明度                            |
| `luminance`          | 把蒙版的亮度当作图像的透明度                                 |
| `match-source`(缺省) | `alpha`和`luminance`的结合体。如果蒙版是`<img>`(包括`SVG`/`linear-gradient()`/`element()`...)，则使用`alpha`；如果蒙版是SVG的`<mask>`，则使用`liminance` |

### §16.4.3 蒙版尺寸(`mask-size`)

在之前的例子中，蒙版图像和蒙版这两者的尺寸是一致的。我们可以使用`mark-size`属性，来单独改变蒙版的尺寸。该属性的区域与`background-size`完全相同。

| `mask-size`属性值                   | 作用                                                         |
| ----------------------------------- | ------------------------------------------------------------ |
| `[<length>|<percentage>|auto]{1,2}` | 蒙版的长与宽                                                 |
| `cover`                             | 尽可能的在保持长宽比的前提下，将蒙版缩放至能覆盖被修饰元素的尺寸 |
| `contain`                           | 尽可能的在保持长宽比的前提下，将蒙版缩放至能由被修饰元素的尺寸覆盖的尺寸 |

### §16.4.4 蒙版重复方式(`mask-repeat`)

与`background-repeat`的取值范围完全相同，这里不再赘述。

### §16.4.5 蒙版定位(`mask-position`/`mask-origin`)

与`background-position`/`background-origin`的取值范围完全相同，这里不再赘述。

### §16.4.6 蒙版裁剪(`mask-clip`)

蒙版本身也可以进行裁剪。`mask-clip`属性决定了蒙版的裁剪框，其属性值与[§16.3.2 裁剪框](###§16.3.2 裁剪框)完全一致，这里不再赘述。

### §16.4.7 蒙版合成(`mask-composite`)

`mask-composite`属性用于规定若干个蒙版之间的布尔运算，可以接收若干个用逗号分隔的属性值。多个布尔运算的顺序是按属性值从后往前的顺序计算的。假设第$i$个`mask`（记为$\text{mask}_i$）的`mask-composite`为$f_i(x,y)$，则有如下递推公式：
$$
\begin{cases}
	\text{output}_{n+1}=空白\\
	\text{output}_i=f_i(\text{mask}_i,\text{output}_{i+1})\\
	\text{output}_1即为最终结果
\end{cases}
$$

| `mask-composite`属性值 | 作用                    |
| ---------------------- | ----------------------- |
| `add`                  | 布尔运算并              |
| `substract`            | 布尔运算减              |
| `intersect`            | 布尔运算交              |
| `exclude`              | 布尔运算并$-$布尔运算交 |

### §16.4.8 蒙版总属性(`mask`)

`mask`属性是以上七个子属性的集合体，语法为若干个由逗号分隔的`[<mask-image> <mask-position>[/<mask-size>]? <mask-repeat> <mask-clip> <mask-origin> <mask-composite> <mask-mode>]`。

### §16.4.9 蒙版类型(`mask-type`)

使用CSS装饰SVG元素时，可以通过`mask-type`设置SVG中的`<mask>`类型。

| `mask-type`属性值 | 作用                             |
| ----------------- | -------------------------------- |
| `luminance`       | 把SVG的alpha通道当作蒙版的透明度 |
| `alpha`           | 把SVG的透明度当作蒙版的透明度    |

如果`mask-type`和`mask-mode`发生了冲突，则最终以`mask-mode`为准。

### §16.4.10 蒙版边框(`mask-border-*`)

目前为止，我们介绍的蒙版都是针对整个元素的。起始CSS1中也规定了只针对边框的蒙版，而且Chrome从第一版起就全部支持了（需要使用`-webkit-`前缀），然而Firefox至今也没有支持。

蒙版边框也有着相似的一系列属性：

- `mask-border-source`：蒙版边框使用的图像
- `mask-border-slice`：定义如何裁切原图分，从而用作各边边框
- `mask-border-width`：各边边框的宽度
- `mask-border-outset`：定义蒙版边框与默认边框的距离，确定蒙版边框绘制的位置
- `mask-border-repeat`：定义蒙版边框图像小于或大于图像切片时的行为
- `mask-border-mode`：定义使用`alpha`蒙版模式还是明度蒙版模式
- `mask-border`：总属性

### §16.4.11 对象填充(`object-fit`)

有一种专门针对置换元素的遮罩。`object-fit`用于规定置换元素在元素框中的填充方式。

| `object-fit`属性值 | 作用                                                         |
| ------------------ | ------------------------------------------------------------ |
| `fill`(缺省)       | 变比例缩放，填满整个元素的内容区，不考虑元素本身的边框长度   |
| `contain`          | 定比例缩放，使得与元素边缘内切，不考虑元素本身的边框长度     |
| `cover`            | 变比例缩放，填满整个元素的内容区，需要考虑元素本身的边框长度 |
| `scale-down`       | 在`none`和`contain`之间选择最小的                            |
| `none`             | 不缩放，直接按照图片原始大小显示                             |

### §16.4.12 对象定位(`object-position`)

`object-position`属性用于控制置换元素在元素框中的对齐方式，接受`<length> <length>`形式的坐标。

# §17 媒体样式

CSS允许针对不同的屏幕，设置不同种类的样式。

## §17.1 媒体查询

### §17.1.1 基本的媒体查询

在HTML中，开发者可以使用`media`属性，为`<style>`和`<link>`标签限制使用的媒体。`media`属性的值可以是若干个由逗号分隔的值。

```html
<link rel="stylesheet" type="text/css" media="print" />
<style type="text/css" media="speech, screen">
    body { font-family: sans-serif; }
</style>
```

开发者也可以在CSS中使用`@import`规则，限制使用的媒体：

```css
@import url(/static/index.css) screen, print;
```

CSS还定义了`@media`块，可以在同一个样式表中定义多个媒体样式：

```css
@media screen {
    body { font-family: sans-serif; }
    h1 { margin-top: 0.5rem; }
}
@media print {
    body { font-family: serif; }
    hi { margin-top: 1rem; }
}
```

> 注意：如果不设定媒体信息，样式将应用于所有媒体。

### §17.1.2 复杂的媒体查询

前面我们提到过，多个媒体类型可以通过逗号分隔，合并在一起。本节将在此基础上更进一步，引入了逻辑运算符。其中逻辑与等价于`and`，逻辑或等价于`,`，逻辑非等价于`not`。

```css
@media all and (min-resolution: 96dpi), screen { ... }
@media not (orientation: landscape) { ... }
```

> 注意：`not`只能在媒体查询的开头使用。

`only`关键字的唯一作用是保证向后兼容性。在不支持媒体查询的浏览器中，我们在媒体类型`xxx`前添加用空格隔开的`only`，会让浏览器认为这是一个名为`onlyxxx`的媒体类型，这显然是无效的，从而让浏览器忽略。在支持的浏览器中，`only`关键字会被忽略。

## §17.2 媒体类型

| 媒体类型 | 作用                                 |
| -------- | ------------------------------------ |
| `all`    | 能呈现所有内容的媒体                 |
| `print`  | 打印给非盲用户的文档/打印预览        |
| `screen` | 显示在屏幕上的媒体                   |
| `speech` | 语音合成器、屏幕阅读器等音频渲染设备 |

HTML4曾经定义了很多媒体类型，但是大多数都被弃用了。

以下是一个实际场景：一般来说，小说网站不希望读者以任何方式复制、传播只有会员才能看的内容。虽然现在的网站使用了“禁用右键”、“审查元素反调试”等措施，但是大部分网站都没有抵抗“`Ctrl+P`打印为PDF”这种行为。我们就可以使用`@media print{}`，让内容在打印时消失：

```html
<html>
    <head>
        <meta charset="utf-8"/>
        <style>
            img {
                border: 1px solid black;
                width: 20rem;
            }
            @media print {
                body > .content {
                    display: none;
                }
            }
        </style>
    </head>
    <body>
        <h1>这是一段小说。现在假设它有版权保护，你打印不了下面的文本。</h1>
        <div class="content">
            <h2>第1章 洪荒重生成了柳树</h2>
            <p>洪荒天地，东荒大山。</p>
            <p>千丈悬崖之上，一颗柳树突然一颤，三万六千条柳枝也随之激烈的颤动起来。</p>
            <p>柳烽难以置信的看着眼前的一切，他竟然重生了，还是最为凶险的洪荒时期，而且还重生成一株柳树。</p>
            <p>活了十亿年的柳树！</p>
           </div>
    </body>
</html>
```

## §17.3 媒体特性描述符

媒体特性描述符必须放在括号中，而且下列所有描述符的值都不能为负数。

- `width`、`min-width`、`max-width`：取值`<length>`，表示用户代理显示区域的宽度，即视区宽度+横向滚动条宽度
- `height`、`min-height`、`max-height`：取值`<length>`，表示用户代理显示区域的高度，即视区高度+纵向滚动条高度
- `device-width`、`min-device-width`、`max-device-width`：取值`<length>`，表示视区宽度
- `device-height`、`min-device-height`、`max-device-height`：取值`<length>`，表示视区高度
- `aspect-ratio`、`min-aspect-ratio`、`max-aspect-ratio`：取值`<integer>/<integer>`，表示`width`与`height`的比值
- `device-aspect-ratio`、`min-device-aspect-ratio`、`max-device-aspect-ratio`：取值`<integer>/<integer>`，表示`device-width`与`device-height`的比值
- `color`、`min-color`、`max-color`：取值`<integer>`，表示用户代理支持的色深
- `color-index`、`min-color-index`、`max-color-index`：取值`<integer>`，表示用户代理支持的色彩数量，数值上等于`2^color`
- `monochrome`、`min-monochrome`、`max-monochrome`：取值`<integer>`，表示显示屏是否为单色，如果是，则表示帧缓冲器的每像素有多少位
- `resolution`、`min-resolution`、`max-resolution`：取值`<integer> dpi`，表示输出设备的分辨率
- `orientation`：取值`portrait|landscape`，表示用户代理设置的显示区域防止方向。横屏为`landscape`，竖屏为`portrait`
- `scan`：取值`progressive|interlace`，表示输出设备使用的扫描方式。CRT和等离子显示器对应`interlace`，现在的液晶显示器对应`progressive`
- `grid`：取值`0|1`，表示是否为基于栅格的输出设备。例如TTY终端对应`1`，否则对应`0`

> 注意：在实际的响应式设计中，一般不使用`orientation`作为判断横屏与竖屏的依据。这是因为`orientation`取决于`aspect-ratio`，而不是`device-aspect-ratio`。

## §17.4 分页媒体(`@page`)

`@page`块用于定义分页相关的特征。

```css
@page ABC {
    ......
}
```

### §17.4.1 定义页面尺寸(`size`)

`size`属性用于定义打印时使用的页面尺寸。**该属性只有在`@page`块中才有效。**

```css
@page {
	size: 7.5in 10in;
    margin: 0.5in;
}
```

| `size`属性值                       | 作用             |
| ---------------------------------- | ---------------- |
| `auto`(缺省)                       |                  |
| `<length>{1,2}`                    | 页面的宽度和高度 |
| `<page-size> [portrait|landscape]` |                  |

`<page-size>`有以下几种常见的取值：`A3`、`A4`、`A5`、`B4`、`B5`。

### §17.4.2 选择页面类型(`page`)

我们可以使用`page`属性控制某个HTML元素使用什么名称的`@page`特征。

例如我们想要竖版打印，但是对于其中一个很长的表格要求横版打印，就可以使用`page`属性：

```css
@page { margin: 0.5in; }
@page normal { size: portrait; }
@page rotate { size: landscape; }

body { page: normal; }
table.long { page: rotate; }
```

除了给`@page <identifier>`取名，我们还能`@page <selector>`指定选择符：

```css
/* 更改书脊两侧的页面的边距 */
@page :left { margin: 0.5in; }
@page :right { margin: 0.5in; }

/* 第一页横向打印 */
@page :first { size: landscape; }
```

### §17.4.3 分页(`page-break-*`)

`page-break-after`和`page-break-before`属性都用于控制元素上下的换页行为，取值为`auto`(缺省)、`always`、`avoid`。

```css
h1.chapter {
	page-break-after: always;
}
h2.section {
    page-break-before: always;
}
```

```html
<h1></h1>
<!-- ========分页 -->
<h2></h2>
......
<!-- ========分页 -->
<h2></h2>
......
```

`page-break-inside`属性顾名思义负责控制元素内的换页行为，取值只有`auto`(缺省)和`avoid`。

以上的三个属性都是建议性的，而不是强制性的。

### §17.4.4 孤行和寡行(`widows`/`orphans`)

`widows`和`orphans`的属性值都是`<integer>`，缺省都是`2`。

- `widows`属性定义：如果在元素的中间插入分页符，那么元素在后一页中至少要有多少行出现在后一页的顶部。
- `orphans`属性定义：如果在元素的中间插入分页符，那么元素在前一页中至少要有多少行出现在前一页的底部。

这两个属性值越大，越不鼓励CSS在元素的中间插入分页符。

# §18 开发规范

经过前17章的学习，我们已经掌握了CSS的基础语法。本节将介绍实际开发中的CSS规范。

## §18.1 CSS文件命名

| CSS文件名    | 含义                                         |
| ------------ | -------------------------------------------- |
| `reset.css`  | 重置样式：重置元素默认样式                   |
| `global.css` | 全局样式：全站共用样式，用于定义页面基础样式 |
| `theme.css`  | 主题样式：用于更换站点皮肤                   |
| `module.css` | 模块样式：用于模块/组件的样式                |
| `master.css` | 母版样式：用于设置模版页的样式               |
| `column.css` | 专栏样式：用于设置顶部栏、侧边栏             |
| `form.css`   | 表单样式：用于设置`<form>`标签的样式         |
| `mend.css`   | 补丁样式：用于维护、修改的样式               |
| `print.css`  | 打印样式：用于打印的样式                     |

## §18.2 `class`命名

| 网页内容 | `class`命名           | 网页内容 | `class`命名     |
| -------- | --------------------- | -------- | --------------- |
| 最外层   | `wrapper`             | 顶导航   | `top-nav`       |
| 头部     | `header`              | 菜单     | `menu`          |
| 内容     | `content`/`container` | 子菜单   | `submenu`       |
| 侧边栏   | `sidebar`             | 标题     | `title`         |
| 栏目     | `column`              | 摘要     | `summary`       |
| 热点     | `hot`                 | 登录条   | `loginbar`      |
| 新闻     | `news`                | 搜索     | `search`        |
| 下载     | `download`            | 标签页   | `tab`           |
| 标志     | `logo`                | 广告     | `banner`        |
| 导航     | `nav`                 | 小技巧   | `tips`          |
| 主体     | `main`                | 图标     | `icon`          |
| 左侧     | `main-left`           | 法律声明 | `siteinfolegal` |
| 右侧     | `main-right`          | 网站地图 | `sitemap`       |
| 底部     | `footer`              | 工具条   | `toolbar`       |
| 友情链接 | `friendlink`          | 首页     | `homepage`      |
| 加入我们 | `joinus`              | 子页     | `subpage`       |
| 版权     | `copyright`           | 合作伙伴 | `partner`       |
| 服务     | `service`             | 帮助     | `help`          |
| 登录     | `login`               | 指南     | `guild`         |
| 注册     | `register`            | 滚动     | `scroll`        |
| 主导航   | `main-nav`            | 提示信息 | `msg`           |
| 子导航   | `sub-nav`             | 投票     | `vote`          |
| 边导航   | `side-nav`            | 相关文章 | `related`       |
| 左导航   | `leftside-nav`        | 文章列表 | `list`          |
| 右导航   | `rightside-nav`       |          |                 |

## §18.3 CSS属性顺序

我们在之前的学习中，已经意识到了CSS属性先后顺序的重要性（例如`::hover`、`::activate`等属性）。下标展示出一些常用的推荐CSS属性顺序。

| CSS属性类别     | 优先级 | 涉及的部分CSS属性                                            |
| --------------- | ------ | ------------------------------------------------------------ |
| 文档流/布局属性 | ++     | `display`、`position`、`float`、`clear`                      |
| 盒模型属性      | +      | `width`、`height`、`padding`、`margin`、`border`、`overflow` |
| 文本属性        | =      | `font`、`line-height`、`text-align`、`text-indent`、`vertical-align` |
| 装饰属性        | -      | `color`、`background`、`opacity`、`cursor`                   |
| 其他属性        | --     | `content`、`list-style`、`quotes`                            |

## §18.4 重置默认样式(CSS Reset)

我们知道，浏览器已经给部分HTML元素设置了默认样式，例如`<input type="button">`、`<ul><li>`等等。每种元素设置的默认样式种类都不一样，因此要重置所有默认样式时，会遇到以下几个问题：

- 某个CSS属性在某个元素上没有默认样式

  你可能会使用`* {margin: 0; padding: 0}`之类的语法重置所有元素。但是有些默认属性没有设置，因此对齐重置CSS属性是没有意义的。这样做会导致性能损耗。

- 难以用一条规则覆盖所有的默认样式

  选择符提供了`*`通配符用于选择所有元素，但是CSS属性名没有提供通配符，所以我们难以覆盖所有的默认样式。

为解决这一问题，开发者Eric Meyer在2011年手动维护并发布了[CSS样式重置表（Reset CSS）](https://meyerweb.com/eric/tools/css/reset/)，覆盖了所有HTML元素的默认样式：

```css
/* http://meyerweb.com/eric/tools/css/reset/ 
   v2.0 | 20110126
   License: none (public domain)
*/
html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed, 
figure, figcaption, footer, header, hgroup, 
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
	margin: 0;
	padding: 0;
	border: 0;
	font-size: 100%;
	font: inherit;
	vertical-align: baseline;
}
/* HTML5 display-role reset for older browsers */
article, aside, details, figcaption, figure, 
footer, header, hgroup, menu, nav, section {
	display: block;
}
body {
	line-height: 1;
}
ol, ul {
	list-style: none;
}
blockquote, q {
	quotes: none;
}
blockquote:before, blockquote:after,
q:before, q:after {
	content: '';
	content: none;
}
table {
	border-collapse: collapse;
	border-spacing: 0;
}
```

在之后的数年内，随着CSS的发展，有很多前端开发者对其进行了改良。例如[Josh Comeau](https://www.joshwcomeau.com/css/custom-css-reset/)的版本：

```css
/*
  1. Use a more-intuitive box-sizing model.
*/
*, *::before, *::after {
  box-sizing: border-box;
}
/*
  2. Remove default margin
*/
* {
  margin: 0;
}
/*
  Typographic tweaks!
  3. Add accessible line-height
  4. Improve text rendering
*/
body {
  line-height: 1.5;
  -webkit-font-smoothing: antialiased;
}
/*
  5. Improve media defaults
*/
img, picture, video, canvas, svg {
  display: block;
  max-width: 100%;
}
/*
  6. Remove built-in form typography styles
*/
input, button, textarea, select {
  font: inherit;
}
/*
  7. Avoid text overflows
*/
p, h1, h2, h3, h4, h5, h6 {
  overflow-wrap: break-word;
}
/*
  8. Create a root stacking context
*/
#root, #__next {
  isolation: isolate;
}
```

# §19 深入CSS概念

本章将深入介绍CSS中的各种概念及其细节。

## §19.1 选择器

### §19.1.1 元素分隔

例题：给定下列菜单，要求相邻两个元素的右边距为`2rem`。

```html
<ul>
    <li>首页</li>
    <li>新闻</li>
    <li>关于</li>
</ul>
```

乍一看，我们可能会想到`li {margin-right: 1rem;}`。但是这样的话，最右侧的元素也会有多余的右边距。如果再用`li:last-of-type {margin-right: inherit}`，虽然也能得到效果，但是太臃肿了。使用相邻选择器只需一行：

```css
li + li { margin-right: 1rem; }
```

## §19.2 盒子模型

### §19.2.1 `border`性能调优

`border: 0`和`border: none`的视觉效果是完全一样的，但在实际开发中我们推荐使用后者，原因有以下两点：

- 性能问题

  如果设置成`border: 0`，那么浏览器依然会对`border`进行渲染，而且确实占用了内存。而`border: none`就能避免这一问题。

- 兼容问题

  `border: 0`的兼容性非常好，在所有浏览器中都表示不显示边框。然而在IE6/IE7中，`border: none`不会产生任何效果，边框依然存在。

### §19.2.2 外边距叠加

外边距叠加指的是两个垂直外边距（`margin`）相遇时，两个外边距会合并在一起，合并后的长度为原先两个外边距的最大值。

外边距叠加可以分为同级元素、父子元素、空元素三种情况：

- 同级元素：上面元素的下外间距与下面元素的上外间距融合

  ```html
  <html>
      <head>
          <style>
              .wrapper {
                  background-color: aquamarine;
                  height: max-content;
                  width: fit-content;
              }
              .wrapper > * {
                  height: 5rem;
                  width: 12rem;
                  background-color: lightblue;
                  text-align: center;
                  line-height: 5rem;
              }
              .wrapper > .box1 { margin-bottom: 30px; }
              .wrapper > .box2 { margin-top: 30px; }
          </style>
      </head>
      <body>
          <div class="wrapper">
              <div class="box1">margin-bottom: 30px</div>
              <div class="box2">margin-top: 30px</div>
          </div>
          最终间距等于max(30px, 30px) = 30px
      </body>
  </html>
  ```

- 父子元素：父元素的上外间距/下外间距与内部子元素的上外间距/下外间距融合

  ```html
  <html>
      <head>
          <style>
              .wrapper {
                  background-color: aquamarine;
                  height: max-content;
                  width: fit-content;
              }
              .wrapper > * {
                  height: 5rem;
                  width: 12rem;
                  background-color: lightblue;
                  text-align: center;
                  line-height: 5rem;
              }
              .wrapper {
                  margin-top: 30px;
              }
              .wrapper > .box { margin-top: 10px; }
          </style>
      </head>
      <body>
          <div class="wrapper">
              <div class="box">margin-top: 30px</div>
              margin-top: 30px<br>
              最终间距等于max(30px, 10px) = 30px
          </div>
      </body>
  </html>
  ```

- 空元素：当元素内容区高度为0时，元素的上外边距和下外边距融合

  ```html
  <html>
      <head>
          <style>
              .wrapper {
                  background-color: aquamarine;
                  height: max-content;
                  width: fit-content;
              }
              .wrapper > * {
                  height: 5rem;
                  width: 12rem;
                  background-color: lightblue;
                  text-align: center;
                  line-height: 5rem;
              }
              .wrapper:nth-of-type(2) {
                  padding-top: 30px;
                  padding-bottom: 30px;
              }
          </style>
      </head>
      <body>
          <div class="wrapper">
              我是上面的wrapper
          </div>
          <div class="wrapper">
              <!-- 内容区高度为0 -->
          </div>
          <div class="wrapper">
              我是下面的wrapper
          </div>
          最终间距等于max(30px, 30px) = 30px
      </body>
  </html>
  ```

  负边距叠加的初衷是希望排版时能保证间距的一致性。例如`article {margin-top:20px; margin-bottom:20px}`保证了第一个`<article>`和顶部、每个`<article>`之间、最后一个`<article>`与底部之间的距离都是`20px`定值。

### §19.2.3 负外边距

我们知道，`margin`的取值也可以是负数。无论`margin`是正是负，该属性都会改变元素的位置，具体规则可以参照下表：

| 为负值的CSS属性                | CSS作用的元素 | 位置发生改变的元素 | 移动方向       |
| ------------------------------ | ------------- | ------------------ | -------------- |
| `margin-top`/`margin-left`     | 文档流元素    | 当前元素           | 反推到相反方向 |
| `margin-right`/`margin-bottom` | 文档流元素    | 后续元素           | 反推到相反方向 |
| `margin-top`/`margin-left`     | 浮动元素      | 当前元素           | 受浮动方向影响 |
| `margin-right`/`margin-bottom` | 浮动元素      | 后续元素           | 受浮动方向影响 |

```html
<html>
    <head>
        <style>
            .container { display: flex; }
            .wrapper {
                width: 200px;
                height: auto;
                text-align: center;
                margin: 10px;
            }
            .wrapper > * { height: 60px; }
            .wrapper > div:nth-of-type(1){ background-color: lightblue; }
            .wrapper > div:nth-of-type(2){ background-color: lightcoral; }
            .wrapper > div:nth-of-type(3){ background-color: lightgreen; }

            .wrapper:nth-of-type(2) > div:nth-of-type(2) {
                margin-top: -10px;
                margin-bottom: -25px;
            }
        </style>
    </head>
    <body>
        <div class="container">
            <div class="wrapper">
                <div>1</div><div>2</div><div>3</div>
            </div>
            <div class="wrapper">
                <div>1</div><div>margin-top: -10px;<br>margin-bottom: -25px</div><div>3</div>
            </div>
        </div>
    </body>
</html>
```

负外边距有以下用途：

- 对齐文本与图片

  在默认情况下，图片和文本的中心线不一定是同一条水平线。例如当图片大于文字时，我们可以给图片设置负底边距：

  ```python
  <html>
      <head>
          <style>
              *:not(body) {
                  border: 1px solid black;
                  margin: 0;
                  padding: 0;
              }
              body > div {
                  background-color: lightblue;
              }
              body > div:nth-of-type(2) > img {
                  margin: 0 0px -10px 0;
              }
          </style>
      </head>
      <body>
          <div>
              <img src="https://www.google.com/favicon.ico">
              <a>Hello, World!</a>
          </div>
          <br>
          <div>
              <img src="https://www.google.com/favicon.ico">
              <a>Hello, World!</a>
          </div>
      </body>
  </html>
  ```

  这种方式的缺点在于减少了“碰撞箱体积”，从而容易与周围元素发生重叠。为解决这一问题，我们可以使用`vertical-align: text-bottom`。

- 双栏自适应布局

  下面的代码定义了一个双栏布局的页面，左侧为主体部分，宽度自适应；右侧为侧边栏部分，宽度固定。

  ```html
  <html>
      <head>
          <style>
              .main, .sidebar {
                  float: left;
                  background: linear-gradient(90deg, rgb(17, 156, 255), rgb(30, 200, 255));
                  border: 1px solid black;
                  box-sizing: border-box; /* 将边框宽度考虑在内,防止float被挤到下一行 */
              }
              .main {
                  width: 100%; 
                  margin-right: -200px;
              }
              .sidebar {
                  width: 200px; 
              }
              .main p {
                  margin-right: calc(200px + 10px); /* 允许主体部分内容区动态调整宽度,防止内容区被遮盖,还可以设置10px间距 */
              }
          </style>
      </head>
      <body>
          <div class="main"><p>这是主体部分，自适应宽度</p></div> 
          <div class="sidebar"><p>这是侧边栏部分，固定宽度</p></div>	
      </body>
  </html>
  
  ```

  让我们来分析这段代码的原理：首先，既然主体和侧边栏都是浮动布局，且主体部分的宽度为100%，说明主题部分的宽度就是整个`<body>`内容区的宽度，会把侧边栏挤到下一行。然后，主体部分通过`margin-right: -200px`缩小了右侧的“碰撞箱体积”，于是这`200px`的空间恰好能被侧边栏填满，于是侧边栏合并到了同一行。最后，主体部分中的`<p>`向右有着`210px`的外边距，因此可以保证主体部分的内容区不会被侧边栏覆盖。

  这种技巧在WordPress中被广泛使用。

- 元素垂直居中

  ```html
  <html>
      <head>
          <style>
              .wrapper {
                  position: relative;
                  width: 200px;
                  height: 160px;
                  border: 1px solid black;
              }
              .wrapper > .item {
                  position: absolute;
                  top: 50%; /* 移动子项左上角 */
                  left: 50%; /* 移动子项左上角 */
                  width: 100px;
                  height: 60px;
                  margin-top: calc(-60px / 2); /* 微调,让中心点位于左上角的位置 */
                  margin-left: calc(-100px / 2); /* 微调,让中心点位于左上角的位置 */
                  background-color: lightblue;
              }
          </style>
      </head>
      <body>
          <div class="wrapper">
              <div class="item"></div>
          </div>
      </body>
  </html>
  ```

### §19.2.4 清除浮动

我们知道，设置了`float`CSS属性的浮动元素是脱离文档流的。这会导致父元素在计算自身尺寸时会忽略浮动元素的尺寸，这一现象称为“高度坍塌”。开发者可以给父元素设置`overflow: hidden`来清除浮动：

```html
<html>
    <head>
        <style>
            .container {
                width: 500px;
                border: 3px solid red;
                padding: 0.2rem;
            }
            .item {
                width: 200px;
                height: 100px;
                border: 1px solid black;
                float: left;
            }
            .item:nth-of-type(1) { float: left; }
            .item:nth-of-type(2) { float: right; }

            .container:nth-of-type(2) { overflow: hidden; }
        </style>
    </head>
    <body>
        <div class="container">
            <div class="item"></div>
            <div class="item"></div>
        </div>
        <div class="container">
            <div class="item"></div>
            <div class="item"></div>
        </div>
    </body>
</html>
```

## §19.3 `display`属性

`display`属性常有以下取值：

| `display`属性值 | 含义           | 尺寸支持 | 外边距支持                                                   | 举例               |
| --------------- | -------------- | -------- | ------------------------------------------------------------ | ------------------ |
| `block`         | 块级元素       | 全支持   | 全支持                                                       | `<div>`            |
| `inline`        | 行内元素       | 不支持   | 只支持`margin-left`/`margin-right`，不支持`margin-top`/`margin-bottom` | `<em>`             |
| `inline-block`  | 行内块级元素   | 全支持   | 全支持                                                       | `<img>`、`<input>` |
| `none`          | 隐藏           |          |                                                              |                    |
| `table-cell`    | 表格单元格元素 |          |                                                              | `<td>`             |

### §19.3.1 表格单元格元素

`display: table-cell`可以让元素以表格单元格的形式显示，具备`<td>`元素的所有特点。其用途有以下三点：

- 图片垂直居中

  ```html
  <html>
      <head>
          <style>
              body { background-color: lightblue; }
              .container {
                  /* display和vertical-align配合使用,实现图片居中 */
                  display: table-cell;
                  vertical-align: middle; 
                  
                  width: 200px;
                  height: 150px;
                  border: 1px solid black;
                  text-align: center; /* 图片水平居中 */
              }
              .container > img {
                  height: 20%;
              }
          </style>
      </head>
      <body>
          <div class="container"><img src="https://www.baidu.com/img/flexible/logo/pc/result.png" alt=""></div>
          <div class="container"><img src="https://www.baidu.com/img/flexible/logo/pc/result@2.png" alt=""></div>
          <div class="container"><img src="https://www.baidu.com/img/flexible/logo/pc/peak-result.png" alt=""></div>
      </body>
  </html>
  ```

- 等高布局

  我们知道，同一行的单元格元素`<td>`的高度相等。同理，`display: table-cell`的元素天生就有等高布局的作用。这种方式的优势在于：我们不用手动指定两列的高度相等。

  ```html
  <html>
      <head>
          <style>
              body { background-color: lightblue; }
              .container {
                  display: table-cell;
                  
                  width: 200px;
                  height: 150px;
                  border: 1px solid black;
                  text-align: center;
              }
              .container > img {
                  height: 20%;
              }
          </style>
      </head>
      <body>
          <div class="container"><img src="https://www.baidu.com/img/flexible/logo/pc/result.png" alt=""></div>
          <div class="container"></div>
          <script>
              document.querySelectorAll('.container:nth-of-type(2)').forEach((element)=>{
                  element.innerHTML = "这是一段多行文本<br>".repeat(15)
              })
          </script>
      </body>
  </html>
  ```

- 自动平均划分元素

  回想一行中的多个单元格挤在同一行的情景，不难发现这些单元格能自动调整宽度，最终占满一行。其行为类似于`display: flex`，会按照原先宽度作为占据宽度时所用的权重。是`display: flex`作用的元素是父元素，而`display: table-cell`作用的元素是子元素。

  ```html
  <html>
      <head>
          <style>
              .container {
                  width: 400px;
                  background-color: lightblue;
              }
              .container > * {
                  display: table-cell;
                  width: 100px;
                  border: 1px solid black;
              }
              .container > *:nth-child(1) { width: 100px; }
              .container > *:nth-child(2) { width: 200px; }
              .container > *:nth-child(3) { width: 300px; }
          </style>
      </head>
      <body>
          <div class="container">
              <div>123</div>
              <div>123</div>
              <div>123</div>
          </div>
      </body>
  </html>
  ```

### §19.3.2 行内块级元素间距

`display: inline-block`是自带“外间距”的。这个“外间距”即使在审查元素上也看不到。要消除这一间距，我们一方面可以给父元素设置`font-size: 0`，然后给子元素单独设置正常的`font-size`；另一方面可以让源代码中的子元素在同一行。

```html
<html>
    <head>
        <style>
            .container {
                width: 400px;
                background-color: lightblue;
            }
            .container > * {
                display: inline-block;
                width: 100px;
                border: 1px solid black;
                font-size: 20px;
            }
            .container:nth-of-type(3) { font-size: 0; }
        </style>
    </head>
    <body>
        <p>display: inline-block</p>
        <div class="container">
            <div>123</div>
            <div>123</div>
            <div>123</div>
        </div>
        <p>display: inline-block, But elements' source codes are in a single line.</p>
        <div class="container">
            <div>123</div><div>123</div><div>123</div>
        </div>
        <p>display: inline-block, But the superelement's font-size is set to 0.</p>
        <div class="container">
            <div>123</div>
            <div>123</div>
            <div>123</div>
        </div>
    </body>
</html>
```

## §19.4 文本效果

### §19.4.1 `text-indent`负值

我们已经知道`text-indent`常用于定义段落的首行多节。例如`text-indent: 2em`能让段落实现首行缩进。其实`text-indent`也能取复制。

考虑下列情景：现要求对网站进行SEO优化。已知SEO特别关注`<h1>`标签，一种常见的思路是将LOGO也放入到`<h1>`标签中。然而搜索引擎只能识别文字，无法识别其中的图片。为解决这一问题，一种思路是让`<h1>`的宽度等于图片的宽度，然后定义`<h1>`的背景图片`background-image`恰为该LOGO图片，最后使用`text-indent: -9999px`来隐藏`<h1>`的文本内容。

```html
<html>
    <head>
        <style>
            h1 {
                background-image: url(https://www.baidu.com/favicon.ico);
                background-repeat: no-repeat;
                text-indent: -9999px;

                border: 1px solid black;
                width: 64px;
                height: 64px;
            }
        </style>
    </head>
    <body>
        <h1>百度一下，你就知道</h1>
    </body>
</html>
```

对于搜索引擎而言，`display: none`的元素会被视为垃圾信息而忽略掉，从而丢失`<h1>`的权重。因此在本例中不推荐使用`display: none`。

### §19.4.2 深入`line-height`

回想英文练习簿的印刷参考线，一行英文的参考线分为四行，从上到下分别是顶线、中线、基线、底线。

<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="915px" height="386px" viewBox="-0.5 -0.5 915 386" content="&lt;mxfile host=&quot;app.diagrams.net&quot; modified=&quot;2023-11-12T17:41:47.848Z&quot; agent=&quot;Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Safari/537.36&quot; etag=&quot;hdgW06tmyefBB6nMuF7n&quot; version=&quot;22.1.0&quot; type=&quot;device&quot;&gt;&#10;  &lt;diagram name=&quot;第 1 页&quot; id=&quot;1swZFBiTSMPvMh5uhhbn&quot;&gt;&#10;    &lt;mxGraphModel dx=&quot;1436&quot; dy=&quot;788&quot; grid=&quot;1&quot; gridSize=&quot;10&quot; guides=&quot;1&quot; tooltips=&quot;1&quot; connect=&quot;1&quot; arrows=&quot;1&quot; fold=&quot;1&quot; page=&quot;1&quot; pageScale=&quot;1&quot; pageWidth=&quot;500&quot; pageHeight=&quot;200&quot; math=&quot;0&quot; shadow=&quot;0&quot;&gt;&#10;      &lt;root&gt;&#10;        &lt;mxCell id=&quot;0&quot; /&gt;&#10;        &lt;mxCell id=&quot;1&quot; parent=&quot;0&quot; /&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-2&quot; value=&quot;&quot; style=&quot;endArrow=none;html=1;rounded=0;fillColor=#f5f5f5;strokeColor=#666666;&quot; edge=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;215&quot; y=&quot;15&quot; as=&quot;sourcePoint&quot; /&gt;&#10;            &lt;mxPoint x=&quot;815&quot; y=&quot;15&quot; as=&quot;targetPoint&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-3&quot; value=&quot;&amp;lt;font style=&amp;quot;font-size: 60px;&amp;quot; face=&amp;quot;Comic Sans MS&amp;quot;&amp;gt;Hello, my Friend!&amp;lt;/font&amp;gt;&quot; style=&quot;text;html=1;align=center;verticalAlign=middle;resizable=0;points=[];autosize=1;strokeColor=none;fillColor=none;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;275&quot; y=&quot;5&quot; width=&quot;490&quot; height=&quot;90&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-5&quot; value=&quot;&quot; style=&quot;endArrow=none;html=1;rounded=0;fillColor=#f5f5f5;strokeColor=#666666;&quot; edge=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;215&quot; y=&quot;45&quot; as=&quot;sourcePoint&quot; /&gt;&#10;            &lt;mxPoint x=&quot;815&quot; y=&quot;45&quot; as=&quot;targetPoint&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-6&quot; value=&quot;&quot; style=&quot;endArrow=none;html=1;rounded=0;fillColor=#f5f5f5;strokeColor=#666666;&quot; edge=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;215&quot; y=&quot;75&quot; as=&quot;sourcePoint&quot; /&gt;&#10;            &lt;mxPoint x=&quot;815&quot; y=&quot;75&quot; as=&quot;targetPoint&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-7&quot; value=&quot;&quot; style=&quot;endArrow=none;html=1;rounded=0;fillColor=#f5f5f5;strokeColor=#666666;&quot; edge=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;215&quot; y=&quot;105&quot; as=&quot;sourcePoint&quot; /&gt;&#10;            &lt;mxPoint x=&quot;815&quot; y=&quot;105&quot; as=&quot;targetPoint&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-8&quot; value=&quot;顶线(top)&quot; style=&quot;text;html=1;align=left;verticalAlign=middle;resizable=0;points=[];autosize=1;strokeColor=none;fillColor=none;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;135&quot; width=&quot;70&quot; height=&quot;30&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-9&quot; value=&quot;中线(middle)&quot; style=&quot;text;html=1;align=left;verticalAlign=middle;resizable=0;points=[];autosize=1;strokeColor=none;fillColor=none;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;135&quot; y=&quot;30&quot; width=&quot;90&quot; height=&quot;30&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-10&quot; value=&quot;基线(baseline)&quot; style=&quot;text;html=1;align=left;verticalAlign=middle;resizable=0;points=[];autosize=1;strokeColor=none;fillColor=none;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;135&quot; y=&quot;60&quot; width=&quot;100&quot; height=&quot;30&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-11&quot; value=&quot;底线(bottom)&quot; style=&quot;text;html=1;align=left;verticalAlign=middle;resizable=0;points=[];autosize=1;strokeColor=none;fillColor=none;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;135&quot; y=&quot;90&quot; width=&quot;90&quot; height=&quot;30&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-13&quot; value=&quot;字符中心线&quot; style=&quot;text;html=1;align=left;verticalAlign=middle;resizable=0;points=[];autosize=1;strokeColor=none;fillColor=none;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;825&quot; y=&quot;45&quot; width=&quot;80&quot; height=&quot;30&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-38&quot; value=&quot;&quot; style=&quot;endArrow=none;html=1;rounded=0;fillColor=#f5f5f5;strokeColor=#666666;&quot; edge=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;215&quot; y=&quot;225&quot; as=&quot;sourcePoint&quot; /&gt;&#10;            &lt;mxPoint x=&quot;815&quot; y=&quot;225&quot; as=&quot;targetPoint&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-39&quot; value=&quot;&amp;lt;font style=&amp;quot;font-size: 60px;&amp;quot; face=&amp;quot;Comic Sans MS&amp;quot;&amp;gt;Hello, my Friend!&amp;lt;/font&amp;gt;&quot; style=&quot;text;html=1;align=center;verticalAlign=middle;resizable=0;points=[];autosize=1;strokeColor=none;fillColor=none;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;275&quot; y=&quot;215&quot; width=&quot;490&quot; height=&quot;90&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-40&quot; value=&quot;&quot; style=&quot;endArrow=none;html=1;rounded=0;fillColor=#f5f5f5;strokeColor=#666666;&quot; edge=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;215&quot; y=&quot;255&quot; as=&quot;sourcePoint&quot; /&gt;&#10;            &lt;mxPoint x=&quot;815&quot; y=&quot;255&quot; as=&quot;targetPoint&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-41&quot; value=&quot;&quot; style=&quot;endArrow=none;html=1;rounded=0;fillColor=#f5f5f5;strokeColor=#666666;&quot; edge=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;215&quot; y=&quot;285&quot; as=&quot;sourcePoint&quot; /&gt;&#10;            &lt;mxPoint x=&quot;815&quot; y=&quot;285&quot; as=&quot;targetPoint&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-42&quot; value=&quot;&quot; style=&quot;endArrow=none;html=1;rounded=0;fillColor=#f5f5f5;strokeColor=#666666;&quot; edge=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;215&quot; y=&quot;315&quot; as=&quot;sourcePoint&quot; /&gt;&#10;            &lt;mxPoint x=&quot;815&quot; y=&quot;315&quot; as=&quot;targetPoint&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-43&quot; value=&quot;&quot; style=&quot;endArrow=none;html=1;rounded=0;fillColor=#f5f5f5;strokeColor=#666666;dashed=1;&quot; edge=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;225&quot; y=&quot;270&quot; as=&quot;sourcePoint&quot; /&gt;&#10;            &lt;mxPoint x=&quot;815&quot; y=&quot;270&quot; as=&quot;targetPoint&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-44&quot; value=&quot;&quot; style=&quot;endArrow=none;html=1;rounded=0;fillColor=#f5f5f5;strokeColor=#666666;dashed=1;&quot; edge=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;215&quot; y=&quot;270&quot; as=&quot;sourcePoint&quot; /&gt;&#10;            &lt;mxPoint x=&quot;225&quot; y=&quot;270&quot; as=&quot;targetPoint&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-45&quot; value=&quot;&quot; style=&quot;endArrow=none;html=1;rounded=0;fillColor=#f5f5f5;strokeColor=#666666;dashed=1;&quot; edge=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;215&quot; y=&quot;165&quot; as=&quot;sourcePoint&quot; /&gt;&#10;            &lt;mxPoint x=&quot;805&quot; y=&quot;165&quot; as=&quot;targetPoint&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-47&quot; value=&quot;&amp;lt;font style=&amp;quot;font-size: 12px;&amp;quot;&amp;gt;半行距分割线&amp;lt;/font&amp;gt;&quot; style=&quot;edgeLabel;html=1;align=center;verticalAlign=middle;resizable=0;points=[];&quot; vertex=&quot;1&quot; connectable=&quot;0&quot; parent=&quot;3-VXkYujrOfkpzglf_jQ-45&quot;&gt;&#10;          &lt;mxGeometry x=&quot;-0.034&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;20&quot; as=&quot;offset&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-48&quot; value=&quot;&quot; style=&quot;endArrow=none;html=1;rounded=0;fillColor=#f5f5f5;strokeColor=#666666;dashed=1;&quot; edge=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;212&quot; y=&quot;375&quot; as=&quot;sourcePoint&quot; /&gt;&#10;            &lt;mxPoint x=&quot;802&quot; y=&quot;375&quot; as=&quot;targetPoint&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-49&quot; value=&quot;&amp;lt;font style=&amp;quot;font-size: 12px;&amp;quot;&amp;gt;半行距分割线&amp;lt;/font&amp;gt;&quot; style=&quot;edgeLabel;html=1;align=center;verticalAlign=middle;resizable=0;points=[];&quot; vertex=&quot;1&quot; connectable=&quot;0&quot; parent=&quot;3-VXkYujrOfkpzglf_jQ-48&quot;&gt;&#10;          &lt;mxGeometry x=&quot;-0.034&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;20&quot; as=&quot;offset&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-50&quot; value=&quot;&quot; style=&quot;strokeWidth=1;html=1;shape=mxgraph.flowchart.annotation_2;align=left;labelPosition=right;pointerEvents=1;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;95&quot; y=&quot;165&quot; width=&quot;20&quot; height=&quot;220&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-51&quot; value=&quot;&quot; style=&quot;strokeWidth=1;html=1;shape=mxgraph.flowchart.annotation_2;align=left;labelPosition=right;pointerEvents=1;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;175&quot; y=&quot;165&quot; width=&quot;20&quot; height=&quot;59&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-52&quot; value=&quot;&quot; style=&quot;strokeWidth=1;html=1;shape=mxgraph.flowchart.annotation_2;align=left;labelPosition=right;pointerEvents=1;spacingTop=0;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;175&quot; y=&quot;227&quot; width=&quot;20&quot; height=&quot;89&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-54&quot; value=&quot;内容区&quot; style=&quot;text;html=1;align=center;verticalAlign=middle;resizable=0;points=[];autosize=1;strokeColor=none;fillColor=none;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;125&quot; y=&quot;256.5&quot; width=&quot;60&quot; height=&quot;30&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-55&quot; value=&quot;半行距&quot; style=&quot;text;html=1;align=center;verticalAlign=middle;resizable=0;points=[];autosize=1;strokeColor=none;fillColor=none;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;125&quot; y=&quot;179.5&quot; width=&quot;60&quot; height=&quot;30&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-56&quot; value=&quot;行框&quot; style=&quot;text;html=1;align=center;verticalAlign=middle;resizable=0;points=[];autosize=1;strokeColor=none;fillColor=none;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;50&quot; y=&quot;260&quot; width=&quot;50&quot; height=&quot;30&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-57&quot; value=&quot;&quot; style=&quot;strokeWidth=1;html=1;shape=mxgraph.flowchart.annotation_2;align=left;labelPosition=right;pointerEvents=1;spacingTop=0;rotation=-180;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;835&quot; y=&quot;105&quot; width=&quot;20&quot; height=&quot;120&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-58&quot; value=&quot;&quot; style=&quot;endArrow=none;html=1;rounded=0;fillColor=#f5f5f5;strokeColor=#666666;dashed=1;exitX=0.811;exitY=-0.035;exitDx=0;exitDy=0;exitPerimeter=0;&quot; edge=&quot;1&quot; parent=&quot;1&quot; source=&quot;3-VXkYujrOfkpzglf_jQ-10&quot;&gt;&#10;          &lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&#10;            &lt;mxPoint x=&quot;225&quot; y=&quot;60&quot; as=&quot;sourcePoint&quot; /&gt;&#10;            &lt;mxPoint x=&quot;815&quot; y=&quot;60&quot; as=&quot;targetPoint&quot; /&gt;&#10;          &lt;/mxGeometry&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-59&quot; value=&quot;&quot; style=&quot;strokeWidth=1;html=1;shape=mxgraph.flowchart.annotation_2;align=left;labelPosition=right;pointerEvents=1;spacingTop=0;rotation=-180;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;905&quot; y=&quot;106&quot; width=&quot;20&quot; height=&quot;210&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-60&quot; value=&quot;行距&quot; style=&quot;text;html=1;align=center;verticalAlign=middle;resizable=0;points=[];autosize=1;strokeColor=none;fillColor=none;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;850&quot; y=&quot;149.5&quot; width=&quot;50&quot; height=&quot;30&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;        &lt;mxCell id=&quot;3-VXkYujrOfkpzglf_jQ-61&quot; value=&quot;行高&quot; style=&quot;text;html=1;align=center;verticalAlign=middle;resizable=0;points=[];autosize=1;strokeColor=none;fillColor=none;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&#10;          &lt;mxGeometry x=&quot;915&quot; y=&quot;196&quot; width=&quot;50&quot; height=&quot;30&quot; as=&quot;geometry&quot; /&gt;&#10;        &lt;/mxCell&gt;&#10;      &lt;/root&gt;&#10;    &lt;/mxGraphModel&gt;&#10;  &lt;/diagram&gt;&#10;&lt;/mxfile&gt;&#10;"><defs/><g><path d="M 165 15 L 765 15" fill="none" stroke="#666666" stroke-miterlimit="10" pointer-events="stroke"/><rect x="225" y="5" width="490" height="90" fill="none" stroke="none" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility" style="overflow: visible; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 1px; height: 1px; padding-top: 50px; margin-left: 470px;"><div data-drawio-colors="color: rgb(0, 0, 0); " style="box-sizing: border-box; font-size: 0px; text-align: center;"><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: nowrap;"><font face="Comic Sans MS" style="font-size: 60px;">Hello, my Friend!</font></div></div></div></foreignObject><text x="470" y="54" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">Hello, my Friend!</text></switch></g><path d="M 165 45 L 765 45" fill="none" stroke="#666666" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 165 75 L 765 75" fill="none" stroke="#666666" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 165 105 L 765 105" fill="none" stroke="#666666" stroke-miterlimit="10" pointer-events="stroke"/><rect x="85" y="0" width="70" height="30" fill="none" stroke="none" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility" style="overflow: visible; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe flex-start; width: 1px; height: 1px; padding-top: 15px; margin-left: 87px;"><div data-drawio-colors="color: rgb(0, 0, 0); " style="box-sizing: border-box; font-size: 0px; text-align: left;"><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: nowrap;">顶线(top)</div></div></div></foreignObject><text x="87" y="19" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px">顶线(top)</text></switch></g><rect x="85" y="30" width="90" height="30" fill="none" stroke="none" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility" style="overflow: visible; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe flex-start; width: 1px; height: 1px; padding-top: 45px; margin-left: 87px;"><div data-drawio-colors="color: rgb(0, 0, 0); " style="box-sizing: border-box; font-size: 0px; text-align: left;"><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: nowrap;">中线(middle)</div></div></div></foreignObject><text x="87" y="49" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px">中线(middle)</text></switch></g><rect x="85" y="60" width="100" height="30" fill="none" stroke="none" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility" style="overflow: visible; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe flex-start; width: 1px; height: 1px; padding-top: 75px; margin-left: 87px;"><div data-drawio-colors="color: rgb(0, 0, 0); " style="box-sizing: border-box; font-size: 0px; text-align: left;"><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: nowrap;">基线(baseline)</div></div></div></foreignObject><text x="87" y="79" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px">基线(baseline)</text></switch></g><rect x="85" y="90" width="90" height="30" fill="none" stroke="none" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility" style="overflow: visible; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe flex-start; width: 1px; height: 1px; padding-top: 105px; margin-left: 87px;"><div data-drawio-colors="color: rgb(0, 0, 0); " style="box-sizing: border-box; font-size: 0px; text-align: left;"><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: nowrap;">底线(bottom)</div></div></div></foreignObject><text x="87" y="109" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px">底线(bottom)</text></switch></g><rect x="775" y="45" width="80" height="30" fill="none" stroke="none" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility" style="overflow: visible; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe flex-start; width: 1px; height: 1px; padding-top: 60px; margin-left: 777px;"><div data-drawio-colors="color: rgb(0, 0, 0); " style="box-sizing: border-box; font-size: 0px; text-align: left;"><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: nowrap;">字符中心线</div></div></div></foreignObject><text x="777" y="64" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px">字符中心线</text></switch></g><path d="M 165 225 L 765 225" fill="none" stroke="#666666" stroke-miterlimit="10" pointer-events="stroke"/><rect x="225" y="215" width="490" height="90" fill="none" stroke="none" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility" style="overflow: visible; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 1px; height: 1px; padding-top: 260px; margin-left: 470px;"><div data-drawio-colors="color: rgb(0, 0, 0); " style="box-sizing: border-box; font-size: 0px; text-align: center;"><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: nowrap;"><font face="Comic Sans MS" style="font-size: 60px;">Hello, my Friend!</font></div></div></div></foreignObject><text x="470" y="264" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">Hello, my Friend!</text></switch></g><path d="M 165 255 L 765 255" fill="none" stroke="#666666" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 165 285 L 765 285" fill="none" stroke="#666666" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 165 315 L 765 315" fill="none" stroke="#666666" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 175 270 L 765 270" fill="none" stroke="#666666" stroke-miterlimit="10" stroke-dasharray="3 3" pointer-events="stroke"/><path d="M 165 270 L 175 270" fill="none" stroke="#666666" stroke-miterlimit="10" stroke-dasharray="3 3" pointer-events="stroke"/><path d="M 165 165 L 755 165" fill="none" stroke="#666666" stroke-miterlimit="10" stroke-dasharray="3 3" pointer-events="stroke"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility" style="overflow: visible; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 1px; height: 1px; padding-top: 166px; margin-left: 470px;"><div data-drawio-colors="color: rgb(0, 0, 0); background-color: rgb(255, 255, 255); " style="box-sizing: border-box; font-size: 0px; text-align: center;"><div style="display: inline-block; font-size: 11px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; background-color: rgb(255, 255, 255); white-space: nowrap;"><font style="font-size: 12px;">半行距分割线</font></div></div></div></foreignObject><text x="470" y="169" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="11px" text-anchor="middle">半行距分割线</text></switch></g><path d="M 162 375 L 752 375" fill="none" stroke="#666666" stroke-miterlimit="10" stroke-dasharray="3 3" pointer-events="stroke"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility" style="overflow: visible; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 1px; height: 1px; padding-top: 376px; margin-left: 467px;"><div data-drawio-colors="color: rgb(0, 0, 0); background-color: rgb(255, 255, 255); " style="box-sizing: border-box; font-size: 0px; text-align: center;"><div style="display: inline-block; font-size: 11px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; background-color: rgb(255, 255, 255); white-space: nowrap;"><font style="font-size: 12px;">半行距分割线</font></div></div></div></foreignObject><text x="467" y="379" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="11px" text-anchor="middle">半行距分割线</text></switch></g><rect x="45" y="165" width="20" height="220" fill="none" stroke="none" pointer-events="all"/><path d="M 65 165 L 55 165 L 55 385 L 65 385" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" pointer-events="all"/><path d="M 45 275 L 55 275" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" pointer-events="all"/><rect x="125" y="165" width="20" height="59" fill="none" stroke="none" pointer-events="all"/><path d="M 145 165 L 135 165 L 135 224 L 145 224" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" pointer-events="all"/><path d="M 125 194.5 L 135 194.5" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" pointer-events="all"/><rect x="125" y="227" width="20" height="89" fill="none" stroke="none" pointer-events="all"/><path d="M 145 227 L 135 227 L 135 316 L 145 316" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" pointer-events="all"/><path d="M 125 271.5 L 135 271.5" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" pointer-events="all"/><rect x="75" y="256.5" width="60" height="30" fill="none" stroke="none" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility" style="overflow: visible; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 1px; height: 1px; padding-top: 272px; margin-left: 105px;"><div data-drawio-colors="color: rgb(0, 0, 0); " style="box-sizing: border-box; font-size: 0px; text-align: center;"><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: nowrap;">内容区</div></div></div></foreignObject><text x="105" y="275" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">内容区</text></switch></g><rect x="75" y="179.5" width="60" height="30" fill="none" stroke="none" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility" style="overflow: visible; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 1px; height: 1px; padding-top: 195px; margin-left: 105px;"><div data-drawio-colors="color: rgb(0, 0, 0); " style="box-sizing: border-box; font-size: 0px; text-align: center;"><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: nowrap;">半行距</div></div></div></foreignObject><text x="105" y="198" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">半行距</text></switch></g><rect x="0" y="260" width="50" height="30" fill="none" stroke="none" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility" style="overflow: visible; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 1px; height: 1px; padding-top: 275px; margin-left: 25px;"><div data-drawio-colors="color: rgb(0, 0, 0); " style="box-sizing: border-box; font-size: 0px; text-align: center;"><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: nowrap;">行框</div></div></div></foreignObject><text x="25" y="279" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">行框</text></switch></g><rect x="785" y="105" width="20" height="120" fill="none" stroke="none" transform="rotate(-180,795,165)" pointer-events="all"/><path d="M 805 105 L 795 105 L 795 225 L 805 225" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" transform="rotate(-180,795,165)" pointer-events="all"/><path d="M 785 165 L 795 165" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" transform="rotate(-180,795,165)" pointer-events="all"/><path d="M 166.1 58.95 L 765 60" fill="none" stroke="#666666" stroke-miterlimit="10" stroke-dasharray="3 3" pointer-events="stroke"/><rect x="855" y="106" width="20" height="210" fill="none" stroke="none" transform="rotate(-180,865,211)" pointer-events="all"/><path d="M 875 106 L 865 106 L 865 316 L 875 316" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" transform="rotate(-180,865,211)" pointer-events="all"/><path d="M 855 211 L 865 211" fill="none" stroke="rgb(0, 0, 0)" stroke-miterlimit="10" transform="rotate(-180,865,211)" pointer-events="all"/><rect x="800" y="149.5" width="50" height="30" fill="none" stroke="none" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility" style="overflow: visible; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 1px; height: 1px; padding-top: 165px; margin-left: 825px;"><div data-drawio-colors="color: rgb(0, 0, 0); " style="box-sizing: border-box; font-size: 0px; text-align: center;"><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: nowrap;">行距</div></div></div></foreignObject><text x="825" y="168" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">行距</text></switch></g><rect x="865" y="196" width="50" height="30" fill="none" stroke="none" pointer-events="all"/><g transform="translate(-0.5 -0.5)"><switch><foreignObject pointer-events="none" width="100%" height="100%" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility" style="overflow: visible; text-align: left;"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 1px; height: 1px; padding-top: 211px; margin-left: 890px;"><div data-drawio-colors="color: rgb(0, 0, 0); " style="box-sizing: border-box; font-size: 0px; text-align: center;"><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; pointer-events: all; white-space: nowrap;">行高</div></div></div></foreignObject><text x="890" y="215" fill="rgb(0, 0, 0)" font-family="Helvetica" font-size="12px" text-anchor="middle">行高</text></switch></g></g><switch><g requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"/><a transform="translate(0,-5)" xlink:href="https://www.drawio.com/doc/faq/svg-export-text-problems" target="_blank"><text text-anchor="middle" font-size="10px" x="50%" y="100%">Text is not SVG - cannot display</text></a></switch></svg>

实际开发中，我们常把`height`和`line-height`设为相同的值，同而实现文字的垂直居中。这种做法的原理是：由于`line-height`与`height`相同，所以容器内只能有一行。而我们知道字符中心线就是一行文本内垂直居中的地方。

当`line-height`的单位为`em`或`%`时，`1em`和`100%`表示一倍的`font-size`。当`line-height`为无单位常数时，表示`font-size`的倍数。

### §19.4.3 深入`vertical-align`

`vertical-align`属性作用于容器元素（且为行内元素、行内块级元素、表格单元格元素），用于定义子元素的垂直对齐方式。

在上图中，我们介绍了顶线、中线、基线、底线。恰好`vertical-align`的取值范围中提供了对应的四种关键词，用于实现垂直对齐：

| `vertical-align`属性值 | 作用       |
| ---------------------- | ---------- |
| `top`                  | 沿顶线对齐 |
| `middle`               | 沿中线对齐 |
| `baseline`             | 沿基线对齐 |
| `bottom`               | 沿底线对齐 |

当`vertical-align`取负值时，表示沿原本的基线向上偏移对齐。

## §19.5 表单

### §19.5.1 单选框/复线框文本对齐

受制于`vertical-align: baseline`的影响，单选框/多选框的按钮与文本并不对齐。我们可以为不同`font-size`的文本手动调整`vertical-align`的数值，从而实现文字居中对齐。

```html
<html>
    <head>
        <style>
            label {
                font-size: 40px;
            }
            input:nth-of-type(2) {
                vertical-align: 5px;
            }
        </style>
    </head>
    <body>
        <form action="post">
            <input type="radio" id="male" value="male" name="gender"><label for="male">Male</label>
            <input type="radio" id="female" value="female" name="gender"><label for="female">Female</label>
        </form>
    </body>
</html>
```

### §19.5.2 禁止拖动`<textarea>`

`<textarea>`的右下角有一个小三角，用户可以拖动这个小三角，以改变`<textarea>`的尺寸。我们常用以下两种方法禁止拖动`<textarea>`：

```html
<html>
    <head>
        <style>
            textarea:nth-of-type(1) {
                min-width: 200px;
                max-width: 200px;
                min-height: 100px;
                max-height: 100px;
            }
            textarea:nth-of-type(2) {
                resize: none;
            }
        </style>
    </head>
    <body>
        <textarea cols="30" rows="10"></textarea>
        <textarea cols="30" rows="10"></textarea>
    </body>
</html>
```

### §19.5.3 表单对齐

设想一个登录表单：用户需要输入用户名、密码和验证码。其中验证码的输入框右边需要有一个发送验证码的按钮。你可能会手动给其中的元素指定左右两侧外间距，但是这种方法难以维护。现在常用的方法是：将表单分为左侧一栏和右侧多栏，右侧多栏的长度之和相等。左栏由一堆`<label>`组成且右对齐，右栏由输入框和按钮组成。所有栏都是向左浮动。

```html
<!DOCTYPE html> <!-- 一定要带,确保使用标准模式,而不是怪异模式 -->
<html>
    <head>
        <style>
            form {
                width: 320px;
                font-family: Arial, Helvetica, sans-serif;
                font-size: 14px;
                font-weight: bold;
                border: 1px solid black;
            }
            form > p { overflow: hidden; } /* 清除浮动 */
            form label {
                float: left;
                width: 60px;
                height: 40px;
                line-height: 40px;
                text-align: right;
                margin-right: 10px;
            }
            form input:not(#submit){
                float: left;
                height: 16px;
                padding: 10px;
            }
            form #username, form #password { width: 210px; }
            form #verifyCode { width: 118px; margin-right: 10px; }
            form #submit { width: 82px; height: 40px; }
        </style>
    </head>
    <body>
        <form action="post">
            <p>
                <label for="username">用户名</label>
                <input type="text" id="username">
            </p>
            <p>
                <label for="password">密码</label>
                <input type="text" id="password">
            </p>
            <p>
                <label for="verifyCode">验证码</label>
                <input type="text" id="verifyCode">
                <input type="submit" id="submit">
            </p>
        </form>
    </body>
</html>
```

## §19.6 浮动布局

要让文档脱离文档流有两种方法，一种是设置`display: float`，另一种是设置`display: absolute`：

```python
<html>
    <head>
        <style>
            .container {
                width: 200px;
                border: 1px solid black;
            }
            .container > .item {
                width: 160px;
                margin: 10px;
                height: 40px;
                border: 1px solid black;
                background-color: lightblue;
                margin-left: auto;
                margin-right: auto;
            }
            .container:nth-of-type(2) > .item:nth-of-type(n+2) {
                float: left;
            }
            .container:nth-of-type(3) > .item:nth-child(3) {
                position: absolute;
                top: 150px;
            }
        </style>
    </head>
    <body>
        <div style="display: flex; gap: 1rem;">
            <div class="container">
                <span>全文档流</span>
                <div class="item"></div>
                <div class="item"></div>
                <div class="item"></div>
            </div>
            <div class="container">
                <span>脱离文档流(float: left)</span>
                <div class="item"></div>
                <div class="item"></div>
                <div class="item"></div>
            </div>
            <div class="container">
                <span>脱离文档流(position: absolute)</span>
                <div class="item"></div>
                <div class="item"></div>
                <div class="item"></div>
            </div>
        </div>
    </body>
</html>
```

### §19.6.1 浮动的影响

如果一个元素设置了浮动，那么它会脱离文档流，从而产生以下影响：

- 对自身而言，元素会被转化成块级元素，从而可以设置`width`、`height`、`margin`、`padding`等属性。
- 对父元素而言，父元素不能包裹子元素，因此可能出现“父元素高度坍塌”的现象。
- 对子元素而言，如果子元素也是浮动元素，那么本元素就不会出现“高度坍塌”的现象。

### §19.6.2 清除浮动

“清除浮动”指的是清除浮动元素脱离文档流之后的影响。例如我们不想让浮动后面的非浮动元素环绕在浮动元素的周围，而是希望它们回归到文档流中，就可以通过清除浮动来实现。

常用的清除浮动方法有以下三种：

- `clear: both`

  `clear`是一个专门用于清除浮动的CSS属性，有三种取值：`left`、`right`、`both`，分别表示清除哪一侧的浮动。**该元素应用于浮动元素后面的非浮动元素。**

  这种方法的缺点是会增加多余的标签，同时破坏了HTML文档的语义。

- `overflow: hidden`

  `overflow: hidden`也可用于清除浮动，但是**作用的对象是浮动元素的父元素**。

  这种方法的缺点是额外承担了`overflow: hidden`的初衷——内容不能外溢。这会导致“清除浮动”与“允许溢出/滚动条”不可兼得。

- `::after`

  给**浮动元素的父元素**的`::after`伪元素同时设置三个CSS属性：`clear: both`、`content: ""`和`display: block`。

  这是实际开发中最好的解决方法。

```html
<html>
    <head>
        <style>
            .container {
                width: 400px;
                text-align: center;
                border: 3px solid black;
                margin-bottom: 5rem;
                background-color: lightcoral;
            }
            .container > .item {
                height: 50px;
                width: 80px;
                background-color: lightblue;
            }
            .container > .item:nth-of-type(1) { float: left; }
            .container > .item:nth-of-type(2) { float: right; }

            .container:nth-of-type(4)::after {
                clear: both;
                content: "";
                display: block;
            } 
        </style>
    </head>
    <body>
        <div class="container">
            父元素高度坍塌
            <div class="item"></div>
            <div class="item"></div>
        </div>
        <div class="container">
            clear: both;
            <div class="item"></div>
            <div class="item"></div>
            <div style="clear: both;"></div> <!-- 一个尺寸为0的元素 -->
        </div>
        <div class="container" style="overflow: hidden;">
            overflow: hidden;
            <div class="item"></div>
            <div class="item"></div>
        </div>
        <div class="container">
            ::after{clear&content&display}
            <div class="item"></div>
            <div class="item"></div>
        </div>
    </body>
</html>
```

## §19.7 定位布局

浮动和定位是CSS的两大布局方式。浮动布局比较灵活，但不容易控制，有时我们更愿意选择定位布局，牺牲灵活性换取精确定位。CSS支持的定位布局有四种，分别是固定定位(`fixed`)、相对定位(`relative`)、绝对定位(`absolute`)、静态定位(`static`)。

定位布局有以下通用规则：

- 默认情况下，固定定位和绝对定位都是相对于`<body>`，而相对定位是相对于原始位置。
- 除静态定位以外，只有在定义`position`属性后，`top`/`bottom`/`left`/`right`属性才能生效。
- 绝对定位会将元素强制转换为块级元素，这一行为与浮动布局是一致的。

要实现子元素相对父元素定位，我们首先需要给父元素定义`position: relative`(相对定位)，然后给子元素定义`position: absolute`(绝对定位)。

```html
<html>
    <head>
        <style>
            .container {
                position: relative;
                width: 300px;
                height: 150px;
                background-color: lightblue;
            }
            .container > .item {
                position: absolute;
                width: 150px;
                height: 80px;
                background-color: lightcoral;

                left: calc(300px / 2 - 150px / 2);
                bottom: -80px;
            }
        </style>
    </head>
    <body>
        <div class="container">
            <div class="item"></div>
        </div>
    </body>
</html>
```

## §19.8 CSS图形

在SVG/Canvas还没流行的年代，矢量图形一直是CSS的一大难题。由于位图占用大量空间，所以开发者尝试给元素添加各种CSS属性，把元素捏成目标形状。本章将介绍这一“传统手艺”，虽然在SVG/Canvas流行的今天，这一手艺已经失去了使用价值，但是我们不妨再见证一下前辈开发者的智慧。

### §19.8.1 三角形

当一个盒模型的两条相邻边走某个角落相交时，CSS会在交界处沿某个角度绘制一条接合线：

```html
<html>
    <head>
        <style>
            div {
                width: 200px;
                height: 100px;
                border: 20px solid;
                border-top-color: darkgoldenrod;
                border-right-color: lightpink;
                border-bottom-color: chartreuse;
                border-left-color: darkblue;
            }
        </style>
    </head>
    <body>
        <div></div>
    </body>
</html>
```

接着，我们让这个`<div>`的尺寸缩小到0，于是四条边框挤在一起，形成一个正方形，最后把左、右、下三条边框的颜色设置成透明，一个三角形就做好了。或者把两个小三角形拼成一个大三角形：

```html
<html>
    <head>
        <style>
            div {
                width: 0;
                height: 0;
                border: 20px solid;
            }
            div:nth-of-type(1) { border-color: lightcoral transparent transparent transparent; }
            div:nth-of-type(2) { border-color: lightcoral lightcoral transparent transparent; }
        </style>
    </head>
    <body>
        <div></div>
        <div></div>
    </body>
</html>
```

开发成通常将这些三角形通过相对定位固定到元素的某个位置，通过拼接创造出更复杂的形状：

```html
<html>
    <head>
        <style>
            body { padding: 1rem; }
            div.sign {
                position: relative;
                width: 400px;
                height: 100px;
                background-color: lightblue;
                padding: 1rem;
                box-sizing: border-box;
                border: 1px solid;
            }
            div.sign::before { /* 创建蓝色三角形的外层黑边框 */
                width: 0;
                height: 0;
                display: block;
                content: "";
                border: 20px solid;
                border-color: transparent transparent black transparent;
                position: absolute;
                top: -41px;
                left: calc((400px - 20px * 2) / 2);
                z-index: -1;
            }
            div.sign::after { /* 创建蓝色三角形 */
                width: 0;
                height: 0;
                display: block;
                content: "";
                border: 20px solid;
                border-color: transparent transparent lightblue transparent;
                position: absolute;
                top: -40px;
                left: calc((400px - 20px * 2) / 2);
            }
        </style>
    </head>
    <body>
        <div class="sign">
            这是告示牌。
        </div>
    </body>
</html>
```

### §19.8.2 圆与椭圆

`border-radius`及其四个角的子属性可以控制圆角半径。配合边框使用可以弯曲边框。

```html
<html>
    <head>
        <style>
            body { padding: 1rem; }
            .box {
                height: 100px;
                width: 100px;
                background-color: rgba(173, 216, 230, 0.3);
                text-align: center;
                font-weight: bolder;
                font-size: 80;
                line-height: 100px;
                
                border-radius: 40px;
                border: 10px solid;
                border-color: transparent black transparent black;
            }
        </style>
    </head>
    <body>
        <div class="box">1</div>
    </body>
</html>
```

### §19.8.3 梯形

在[§19.8.1 三角形](###§19.8.1 三角形)的基础上，我们给元素添加高度或宽度，就能调整上底的长度，从而让三角形变成梯形。

```html
<html>
    <head>
        <style>
            body { padding: 1rem; }
            .box {
                box-sizing: border-box;
                border: 50px solid black;
                border-color: transparent transparent black transparent;
                
                width: 150px;
            }
        </style>
    </head>
    <body>
        <div class="box"></div>
    </body>
</html>
```

### §19.8.4 书签图标

书签的形状相等于一个竖条长方形在底部砍去了一个三角形。使用边框能轻松作出该效果。

```html
<html>
    <head>
        <style>
            body { padding: 1rem; }
            .box {
                border: 10px solid black;
                border-color: black black transparent black;
                width: 0;
                height: 15px;
            }
        </style>
    </head>
    <body>
        <div class="box"></div>
    </body>
</html>
```

### §19.8.5 下载图标

下载图标就是一个向下的箭头。我们使用边框制作三角形，再用`border-shadow`缩小阴影尺寸来制作箭头的正方形箭柄：

```html
<html>
    <head>
        <style>
            body { padding: 1rem; }
            .box {
                border: 16px solid black;
                border-color: black transparent transparent transparent;
                width: 0;
                box-shadow: 0 -24px 0 -8px;
            }
        </style>
    </head>
    <body>
        <div class="box"></div>
    </body>
</html>
```

### §19.8.6 单选图标

`box-shadow`属性能根据边框形状创建对应的阴影。

```html
<html>
    <head>
        <style>
            body {
                padding: 1rem;
            }
            .box {
                width: 50px;
                height: 50px;
                background-color: black;
                border: 1px solid black;
                border-radius: 50px;
                
                box-shadow: 0 0 0 5px white, 0 0 0 10px black;
            }
        </style>
    </head>
    <body>
        <div class="box"></div>
    </body>
</html>
```

### §19.8.7 应用列表图标

“田字型”图表由九个排布均匀的点组成。这里我们使用`outline`创建周围8个点，用`border`创建中间的点：

```html
<html>
    <head>
        <style>
            body {
                padding: 1rem;
            }
            .box {
                width: 0px;
                border: 3px solid black;
                outline: 6px dotted;
                outline-offset: 6px;
            }
        </style>
    </head>
    <body>
        <div class="box"></div>
    </body>
</html>
```

## §19.9 层叠上下文

层叠上下文（Stacking Context）与CSS中的Z轴概念相关。根元素或`z-index`不为`auto`的元素都会创建一个自己的层叠上下文。

在一个层叠上下文中，层叠级别（Stacking Level）决定了图层的显示顺序，如下所示：
$$
\begin{align}
	& 边框和背景 < 负\text{z-index} < 块盒子 < 浮动盒子 < \\
	& 行内盒子 < 零\text{z-index} < 正\text{z-index} \\
\end{align}
$$

## §19.10 BFC

块级格式上下文（BFC，Block Formatting Context）是一个独立的渲染区域，规定了内部的块级元素如何布局，且不受外部影响。

如果一个元素具备以下任意一个条件，则CSS会为该元素创建一个新的BFC：

- 根元素
- `float: left`/`float: right`
- `position: absolute`/`position: fixed`
- `overflow: auto`/`overflow: hidden`/`overflow: scroll`
- `display: inline-block`/`display: table-caption`/`display: table-cell`
- `display: flex`/`display: inline-flex`（CSS3特供）

在一个BFC中，盒子从顶端开始一个个的垂直排列，盒子之间的外边距会叠加。

### §19.10.1 避免外边距叠加

既然同在一个BFC内的两个元素才会产生外边距叠加，我们自然想到可以把两个元素放在两个BFC中，就能避免这一问题。

```html
<html>
    <head>
        <style>
            .container {
                width: 20rem;
                height: 10rem;
                background-color: lightblue;
            }
            .bfc {
                overflow: hidden; /* 创建BFC */
            }
            .item {
                width: 5rem;
                height: 3rem;
                background-color: lightcoral;
                border: 1px solid black;
            }
            .bfc:nth-of-type(1) > .item { margin-bottom: 1rem; }
            .bfc:nth-of-type(2) > .item { margin-top: 1rem; }
        </style>
    </head>
    <body>
        <div class="container">
            <div class="bfc">
                <div class="item"></div>
            </div>
            <div class="bfc">
                <div class="item"></div>
            </div>
        </div>
    </body>
</html>
```

### §19.10.2 清除浮动

计算一个BFC的高度时，内部的浮动元素也要考虑计算。因此，为防止父元素高度坍塌的现象，我们可以让父元素成为BFC。

### §19.10.3 避免文字环绕

一个元素浮动之后，其层叠级别比普通文档流的层流级别高。在BFC内部，如果存在新的新的BFC子元素和新的浮动元素，则新BFC与浮动元素不会重叠。

```html
<html>
    <head>
        <style>
            .container {
                width: 20rem;
                height: 5rem;
                background-color: lightblue;
                border: 1px solid black;
            }
            .item {
                width: 5rem;
                height: 1rem;
                background-color: lightcoral;
                border: 1px solid black;

                float: left;
            }
            .bfc {
                overflow: hidden;
            }
        </style>
    </head>
    <body>
        <div class="container">
            <div class="item" style="display: inline-block;"></div>
            
            <div>
                这是一段文本。这是一段文本。这是一段文本。这是一段文本。
            </div>
        </div>
        <div class="container">
            <div class="item" style="display: inline-block;"></div>
            
            <div class="bfc">
                这是一段文本。这是一段文本。这是一段文本。这是一段文本。
            </div>
        </div>
    </body>
</html>
```

### §19.10.4 自适应列布局

之前我们在[§19.2.3 负外边距](###§19.2.3 负外边距)一节中讲过使用负外边距实现自适应列布局。本节将介绍使用BFC实现。核心原理是将固定宽度的一列设置成浮动元素，然后另一列宽度可变的栏设置成BFC。

```html
<html>
    <head>
        <style>
            .sidebar {
                width: 15rem;
                height: 100%;
                background-color: lightblue;

                float: left;
            }
            .main {
                width: auto;
                height: 100%;
                background-color: lightcoral;

                overflow: hidden;
            }
        </style>
    </head>
    <body>
        <div class="sidebar"> 长度不变</div>
        <div class="main">长度可变,自适应</div>
    </body>
</html>
```

# §20 性能调优

## §20.1 属性简写

属性简写指的是，在实现相同效果的前提下，使用字符更少的CSS属性或属性值。

- 子属性合并为总属性

  子属性不仅字符数量多，占用存储空间，而且条目数量也多，拖慢解析效率。所以能使用总属性，就不要使用子属性。

  | 总属性(部分) | 对应子属性                                                   |
  | ------------ | ------------------------------------------------------------ |
  | `border`     | `border-top-color`、`border-top-width`、`border-top-style`、<br/>`border-right-color`、`border-right-width`、`border-right-style`、<br>`border-bottom-color`、`border-bottom-width`、`border-bottom-style`、<br/>`border-left-color`、`border-left-width`、`border-left-style` |
  | `padding`    | `padding-top`、`padding-right`、`padding-bottom`、`padding-left` |
  | `margin`     | `margin-top`、`margin-right`、`margin-bottom`、`margin-left` |
  | `background` | `background-color`、`background-image`、`background-repeat`、`background-attachment`、`background-position` |
  | `font`       | `font-family`、`font-size`、`font-weight`、`line-height`     |

- 属性值缩写

  例如颜色，`#ffffff`可以简写成`#fff`。

## §20.2 语法压缩

### §20.2.1 空白符

在CSS中，只有属性值之间才必须使用分隔符（例如空格、正斜杠），其余地方的空白符完全可以忽略，从而节省CSS文件空间。

```css
/* 原始版本，例如阅读 */
.box {
	padding: 0px;
    border: 0.5px solid black;
    background: url("https://www.baidu.com/favicon.ico")
}

/* 压缩版本，利于储存和传输 */
.box{padding:0px;border:0.5px solid black;background:url("https://www.baidu.com/favicon.ico");}
```

### §20.2.2 结尾分号

CSS的每一个选择器的样式都是用大括号括起来的。实际上，最后一个属性后面可以不用以分号结尾，从而剩下一个分号的空间。

```css
/* 进一步压缩结尾分号 */
.box{padding:0px;border:0.5px solid black;background:url("https://www.baidu.com/favicon.ico")}
```

### §20.2.3 `url()`引号

我们知道，`url()`作为属性值用于索引目标资源文件。然而索引的路径可以不用添加引号。

```css
/* 删除url()内的引号 */
.box{padding:0px;border:0.5px solid black;background:url(https://www.baidu.com/favicon.ico)}
```

### §20.2.4 `0`/`0.x`属性值

一方面，对于`0`而言，我们不必写出它的单位。另一方面，对于`0.x`的小数，我们可以忽略前导`0`，直接以小数点开头。

```css
/* 优化0/0.x的属性值 */
.box{padding:0;border:.5px solid black;background:url(https://www.baidu.com/favicon.ico)}
```

### §20.2.5 提取相同定义

回想各类压缩算法，如果一段子串重复多次出现，我们可以将其提取出来，用一段编号代替它。这其实就是众多CSS第三方库的思路——将常用的一组CSS属性提取出来，从而节省空间。

为了展示原理，这里我们来手动提取相同的义。

```CSS
/* 原始CSS */
h1 { text-indent: 0rem; color: red; border-left: 3px solid black; }
h2 { text-indent: 1rem; color: red; border-left: 3px solid black; }

/* 提取后的CSS */
h1 { text-indent: 0rem; }
h2 { text-indent: 1rem; }
h1, h2 { color: red; border-left: 3px solid black; }
```

实际上，我们还可以利用CSS的继承特点，将可以继承的CSS属性直接定义到父元素上，这样就不用给子元素单独定义了。

## §20.3 高性能选择器

选择器的匹配过程可能与直觉相悖——CSS会从最右侧的选择器开始，依次向左匹配筛选。

CSS有各类选择器，当它们单独使用时，效率从高到低的排序分别为：
$$
\begin{align}
	& \text{id选择器 > class选择器 > 元素选择器 > 相邻选择器 >} \\
	& 子选择器 > 后代选择器 > 通配符选择器 > 属性选择器 > 伪类选择器
\end{align}
$$
基于此，我们可以总结出以下四个选择器性能调优建议：

1. 不要使用通配符：通配符的工作量非常大。
2. id选择器和class选择器不需要元素名：id能保证唯一，所以不需要元素名；class设计的初衷就是通配，限定元素名会破坏这一初衷。
3. 选择器不要超过三层：选择器的耗时会串联叠加，因此选择器不适宜嵌套多层。
4. 右侧选择器的选择条件应尽可能严苛：CSS会从最右侧的选择器开始，因此从一开始就筛去大部分元素，有利于左侧选择器减少运行时间。
5. 禁用后代选择器，少用子代选择器，多用id选择器和class选择器。

# §21 开发技巧

在CSS的开发流程中，开发人员针对频繁出现的设计需求，总结出了若干优秀的工程实践方法。本节将介绍这些开发技巧。

## §21.1 水平居中

### §21.1.1 文字水平居中

使用`text-align: center`，即可控制文字对齐的行为。

```html
<html>
    <head>
        <style>
            .box {
                border: 1px solid black;
                width: 20rem;
                text-align: center;
            }
            .box > div {
                width: min-content;
            }
        </style>
    </head>
    <body>
        <div class="box">
            <span>Hello</span>
        </div>
    </body>
</html>
```

### §21.1.2 块级元素水平居中

如果在一行之内，只有需要居中的元素，没有其它对齐方向元素的干扰，那么我们就可以考虑使用`margin-left: 0; margin-right: 0`。

```html
<html>
    <head>
        <style>
            .box {
                border: 1px solid black;
                width: 20rem;
            }
            .box > .item {
                border: 1px solid black;
                width: 5rem;
                margin-left: auto;
                margin-right: auto;
            }
        </style>
    </head>
    <body>
        <div class="box">
            <div class="item">Hello</div>
        </div>
    </body>
</html>
```

### §21.1.3 行内元素/复合行内元素水平居中

行内元素（`inline`）、复合行内元素（`inline-block`、`inline-table`、`inline-flex`）对齐的方式依然是`text-align: center`。

## §21.2 垂直居中

### §21.2.1 单行文本垂直居中

要让单行文本垂直居中，只需让`line-height`和`height`的属性值相等即可。

### §21.2.2 多行文本垂直居中

要让多行文本垂直居中，这种方法并不好想。然而程序员的智慧是无穷的——实现方法的关键是把多行文本包含在一个`<span>`标签内，然后定义`<span>`为`display: inline-block`，最后定义父元素为`display: table-cell`、`vertical-align: middle`。

```html
<html>
    <head>
        <style>
            .box {
                border: 1px solid black;
                width: 20rem;
                height: 8rem;

                display: table-cell;
                vertical-align: middle;
            }
            .box > .item {
                display: inline-block;
            }
        </style>
    </head>
    <body>
        <div class="box">
            <span class="item">
                这是两行<br>
                多行文本
            </span>
        </div>
    </body>
</html>
```

### §21.2.3 任意元素垂直居中

无论元素是块级元素还是行内元素，如果父元素和子元素的尺寸均已知，我们就可以使用`position: absolute`、`top: 50%`、`left: 50%`来精确调整子元素左上角相对父元素的位置，然后使用`margin-left`和`margin-right`来让子元素中心点移动到原先左上角所在的位置。

```html
<html>
    <head>
        <style>
            .box {
                border: 1px solid black;
                width: 20rem;
                height: 8rem;

                position: relative;
            }
            .box > .item {
                border: 1px solid black;
                width: 7rem;
                height: 3rem;
                
                position: absolute;
                top: 50%;
                left: 50%;
                margin-left: calc(-7rem / 2);
                margin-top: calc(-3rem / 2);
            }
        </style>
    </head>
    <body>
        <div class="box">
            <div class="item"></div>
        </div>
    </body>
</html>
```

### §21.2.4 行内元素垂直居中

如果元素是行内元素，除了[§21.2.3 任意元素垂直居中](###§21.2.3 任意元素垂直居中)介绍的方法，我们还可以使用[§21.2.2 多行文本垂直居中](###§21.2.2 多行文本垂直居中)介绍的方法——给父元素声明为`display: table-cell`和`vertical-align: middle`，给子元素声明为`display: inline-block`。

```html
<html>
    <head>
        <style>
            .box {
                border: 1px solid black;
                width: 20rem;
                height: 8rem;

                display: table-cell;
                vertical-align: middle;
            }
            .box > .item {
                border: 1px solid black;
                /* display: inline-block; */ /* <img>就是inline-block，不必重复声明 */
            }
        </style>
    </head>
    <body>
        <div class="box">
            <img class="item" src="https://www.baidu.com/favicon.ico">
        </div>
    </body>
</html>
```

## §21.3 CSS Sprite

我们直到，每个图片都对应一次HTTP请求。如果页面中有很多图标图像，那么会对服务器产生可观的请求数量。一个好的解决方法是让后端一次性返回一张包含所有贴图的大图片，然后由前端裁剪得到所需的小图标。这种技术称为CSS Sprite（CSS精灵/CSS雪碧图）。

开发着可以使用`background-iamge`加载精灵图，然后用`background-position`截取需要的部分。

```html
<html>
    <head>
        <style>
            .box {
                border: 1px solid black;
                width: fit-content;

            }
            .box > img.item { 
                border: 1px solid black; 
                background-image: url("http://www.spritecow.com/assets/9/tutorial-sprite.png");
                background-repeat: no-repeat;
                width: 33px;
                height: 33px;
            }

            .box > img.item:nth-of-type(1) { background-position: -433px -51px; }
            .box > img.item:nth-of-type(2) { background-position: -476px -51px; }
            .box > img.item:nth-of-type(3) { background-position: -516px -51px; }
            .box > img.item:nth-of-type(4) { background-position: -560px -49px; }
        </style>
    </head>
    <body>
        <div class="box">
            <img class="item">
            <img class="item">
            <img class="item">
            <img class="item">
        </div>
    </body>
</html>
```

关于CSS Sprite，以下有两款工具：

- CSS Sprite Generator
- [Sprite Cow](http://www.spritecow.com/)

## §21.4 字体图标

字体图标是指将图标做成字体的样式。这种方式的优点在于：一是字体本身就是矢量图，可以通过更改字号的方式随意调整大小；二是字体与CSS精灵图一样都是单文件，只需单次HTTP请求即可获取所有图标；三是可有选择性地获取字体图标的子集，从而节省存储空间。

著名的字体图标提供商有[IcoMoon](https://icomoon.io/app/#/select)和[IconFont](https://www.iconfont.cn/)。这里我们以IconFont为例，介绍字体图标的使用方法：

1. 在IconFont官网选择一组需要的图标，添加到购物车，点击“下载代码”按钮，得到一个`download.zip`压缩文件。

2. 将该压缩文件解压，得到以下文件：

   ```shell
   $ tree
   .
   ├── demo.css
   ├── demo_index.html
   ├── iconfont.css
   ├── iconfont.js
   ├── iconfont.json
   └── iconfont.ttf
   ```

3. 按照`demo_index.html`的指示，创建以下HTML文档，即可查看效果：

   ```html
   <html>
       <head>
           <style>
               @font-face {
                   font-family: 'iconfont';
                   src: url('../font_qu9du1jhqwf/iconfont.ttf') format('truetype');
               }
               .iconfont {
                   font-family: "iconfont" !important;
                   font-size: 16px;
                   font-style: normal;
                   -webkit-font-smoothing: antialiased;
                   -moz-osx-font-smoothing: grayscale;
   
                   font-size: 48px;
               }
           </style>
       </head>
       <body>
           <div class="iconfont">&#xe651;</div>
           <div class="iconfont">&#xe882;</div>
           <div class="iconfont">&#xe66c;</div>
       </body>
   </html>
   ```
