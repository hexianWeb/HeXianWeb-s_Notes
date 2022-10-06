# CSS学习

> 作者何贤

## 语言基础知识（完成）

插入样式表的方法有三种:

- 外部样式表(External style sheet)
- 内部样式表(Internal style sheet)
- 内联样式(Inline style)

优先级如下：

**（内联样式）Inline style > （内部样式）Internal style sheet >（外部样式）External style sheet > 浏览器默认样式**

### 浏览器如何渲染网页

浏览器是如何从一个简单的，只包含文本的 `HTML` 文件渲染出这个好看的网页呢？为此，我们需要了解什么是 `DOM`、`CSSOM` 和 `Render Tree`

#### Document Object Model (DOM)

当浏览器读取 HTML 代码时，只要遇到像`html`,等这样的 HTML 元素`body`，`div`它就会创建一个称为[**Node**](https://developer.mozilla.org/en-US/docs/Web/API/Node)的 JavaScript 对象。最终，所有 HTML 元素都将转换为 JavaScript 对象。

由于每个 HTML 元素都有不同的属性，因此 Node 对象将由不同的类（*构造函数*）创建。例如，`div`元素的 Node 对象是从其`HTMLDivElement`继承**类** `Node`创建的。

在浏览器从 HTML 文档创建节点之后，它必须创建这些节点对象的**树状结构**。由于 HTML 文件中的 HTML 元素相互嵌套，浏览器需要复制它，所以使用之前创建的 Node 对象。这将有助于浏览器在其整个生命周期内有效地呈现和管理网页。

<img src="https://miro.medium.com/max/910/1*YSA8lCfCVPn3d6GWAVokrA.png" alt="img" style="zoom:67%;" />

我们早期的 HTML 文档的[**DOM**](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)树如上所示。DOM 树从最顶层元素开始，该元素是`html`元素，并根据文档中 HTML 元素的出现和嵌套进行分支。每当找到 HTML 元素时，它都会从其各自的类（*构造函数）创建一个 DOM 节点（* *Node* ）对象。

> *💡* DOM 节点并不总是必须是 HTML 元素。当浏览器创建 DOM 树时，它还会将诸如[注释](https://developer.mozilla.org/en-US/docs/Web/API/Comment)、[属性](https://developer.mozilla.org/en-US/docs/Web/API/Attr)、[文本](https://developer.mozilla.org/en-US/docs/Web/API/Text)之类的内容保存为树中的单独节点。但是为了简单起见，我们将只考虑 HTML 元素的 DOM 节点 AKA **DOM element**。[**这**](https://www.w3schools.com/jsref/prop_node_nodetype.asp)是所有 DOM 节点类型的列表。

#### CSS Object Model (CSSOM)

在构建了 `DOM` 之后，浏览器从所有的源头（外部的、嵌入式的、内嵌的、用户代理的等）读取 `CSS` ，并构建一个 [CSSOM](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FAPI%2FCSS_Object_Model)。`CSSOM` 是 `CSS` 对象模型的缩写，它和 `DOM` 一样是一个树状结构。

这个树中的每个节点都包含 `CSS` 样式信息，这些信息将被应用到它所针对的 `DOM` 元素上（由选择器指定）。然而，`CSSOM` 并不包含那些不能在屏幕上打印的 `DOM` 元素，如`<meta>、<script>、<title>`等。

即使某个 `HTML` 元素的 `CSS` 属性（如 `display` ）没有被开发者或浏览器定义，其值也会被设置为 [W3C CSS](https://link.juejin.cn/?target=https%3A%2F%2Fwww.w3.org%2FStyle%2FCSS%2F) 标准规定的该属性的默认值。

我们可以用下图来使前面例子的 `CSSOM` 树可视化。为了简单起见，我们将忽略用户代理样式，而专注于前面提到的 `CSS` 样式。

![1618747003004.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b889d71c808f45aebf0dd104785334f8~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

从上图中可以看出，我们的 `CSSOM` 树中不包含`<link>、<title>、<script>`等不会被打印到屏幕上的元素。*红色的 `CSS` 属性值是从顶部层叠下来的，而灰色的属性值则是覆盖了继承的值。*

#### Render Tree

`Render-Tree` 也是一个将 `DOM` 树和 `CSSOM` 树组合在一起构建的树状结构。浏览器要计算每个可见元素的布局，并把它们画在屏幕上，为此浏览器使用了这个 `Render-Tree`。因此，如果没有构建 `Render-Tree`，那么任何东西都不会被打印在屏幕上，这就是为什么我们同时需要 DOM 和 CSSOM 树。

由于 `Render-Tree` 是对最终将被打印在屏幕上的内容的低级表示，它不会包含在像素矩阵(页面)中不包含任何区域(一定情况下可认为是：不占空间)的节点。例如，`display:none`;元素的尺寸为`0px X 0px`，因此它们不会出现在 `Render-Tree` 中。

![1618747752887.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6b7fb2b2f8ae4d628f5b498c09866c10~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

从上图可以看出，`Render-Tree` 结合了 `DOM` 和 `CSSOM`，生成了一个树状结构，其中只包含了要打印在屏幕上的元素

#### 渲染顺序

当一个网页被加载时，浏览器首先读取 `HTML` 文本并从中构建 `DOM` 树。然后它处理 `CSS`，无论是内嵌的、嵌入式的、还是外部的 `CSS`，并从中构建 `CSSOM` 树。

构建完这些树后，它再从中构建 `Render-Tree`。一旦构建了 `Render-Tree`，浏览器就会开始在屏幕上打印各个元素。

#### 布局操作

首先浏览器创建每个单独的 `Render-Tree` 节点的布局。布局是由每个节点的像素点指定尺寸和他们将会被打印在屏幕上的位置，这个过程被称为布局，因为浏览器正在计算每个节点的布局信息。这个过程也被称为回流或浏览器回流(reflow)，当你滚动、调整窗口大小或操作 DOM 元素时，也会发生这个过程。[这里](https://link.juejin.cn/?target=https%3A%2F%2Fstackoverflow.com%2Fa%2F27637245%2F2790983)是可以触发元素布局/回流的事件列表。

#### **回流的相关补充**

> 作者：腰花
> 链接：https://juejin.cn/post/6844903569087266823

> 当`Render Tree`中部分或全部元素的尺寸、结构、或某些属性发生改变时，浏览器重新渲染部分或全部文档的过程称为回流。

会导致回流的操作：

- 页面首次渲染
- 浏览器窗口大小发生改变
- 元素尺寸或位置发生改变
- 元素内容变化（文字数量或图片大小等等）
- 元素字体大小变化
- 添加或者删除**可见**的`DOM`元素
- 激活`CSS`伪类（例如：`:hover`）
- 查询某些属性或调用某些方法

一些常用且会导致回流的属性和方法：

- `clientWidth`、`clientHeight`、`clientTop`、`clientLeft`
- `offsetWidth`、`offsetHeight`、`offsetTop`、`offsetLeft`
- `scrollWidth`、`scrollHeight`、`scrollTop`、`scrollLeft`
- `scrollIntoView()`、`scrollIntoViewIfNeeded()`
- `getComputedStyle()`
- `getBoundingClientRect()`
- `scrollTo()`

重绘 (Repaint)

当页面中元素样式的改变并不影响它在文档流中的位置时（例如：`color`、`background-color`、`visibility`等），浏览器会将新样式赋予给元素并重新绘制它，这个过程称为重绘


#### 绘制操作（完全不理解 要命）

创建图层可以帮助浏览器在网页的整个生命周期中高效地执行绘制操作，比如在滚动或调整浏览器窗口大小的时候。拥有图层还可以帮助浏览器按照开发者的意图，正确地按照堆叠顺序（沿 z 轴）绘制元素。

#### 合成操作

每次回流（布局）或重绘时都要将整个图层送去绘制，这显然是很低效的。因此，一个图层被分解成不同的块，然后将其绘制在屏幕上。你也可以在 Chrome 的 DevTool Rendering 面板中看到这些块。

![](https://miro.medium.com/max/1100/1*yQJkz12sPxS-kJoMDqzbEQ.png)

### CSS求值过程

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-21_21-12.png)

## 选择器（完成）

> 选择器用来指定网页上我们想要样式化的[HTML](https://developer.mozilla.org/zh-CN/docs/Glossary/HTML)元素。选择器分为基础选择器和复合选择器两大类

### 基础选择器

#### [通用选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Universal_selectors)（[Universal selector](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Universal_selectors)）

选择所有元素。（可选）可以将其限制为特定的名称空间或所有名称空间。 **语法：**`*` `ns|*` `*|*` **例子：**`*` 将匹配文档的所有元素。

#### [元素选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Type_selectors)（[Type selector](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Type_selectors)）

按照给定的节点名称，选择所有匹配的元素。 **语法：**`elementname` **例子：**`input` 匹配任何 [``](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/Input) 元素。

#### [类选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Class_selectors)（[Class selector](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Class_selectors)）

按照给定的 `class` 属性的值，选择所有匹配的元素。 **语法**：`.classname` **例子**：`.index` 匹配任何 `class` 属性中含有 "index" 类的元素。

#### [ID 选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/ID_selectors)（[ID selector](https://developer.mozilla.org/zh-CN/docs/Web/CSS/ID_selectors)）

按照 `id` 属性选择一个与之匹配的元素。需要注意的是，一个文档中，每个 ID 属性都应当是唯一的。 **语法：**`#idname` **例子：**`#toc` 匹配 ID 为 "toc" 的元素。

#### [属性选择器](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Attribute_selectors)（[Attribute selector](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Attribute_selectors)）

按照给定的属性，选择所有匹配的元素。 **语法：**`[attr]` `[attr=value]` `[attr~=value]` `[attr|=value]` `[attr^=value]` `[attr$=value]` `[attr*=value]` **例子：**`[autoplay]` 选择所有具有 `autoplay` 属性的元素（不论这个属性的值是什么）。

#### 伪类选择器

**伪类**

> 是添加到选择器的关键字，指定要选择的元素的特殊状态
>
> 
>
> MDN伪类大全：https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/Selectors/Pseudo-classes_and_pseudo-elements

*这里无法列出所有的伪类选择器，只能列出部分常用的伪类选择器*

##### 动态伪类选择器

- :link。元素被定义了超链接但并未被访问过
- :visited。元素被定义了超链接并已被访问过
- :active。元素被激活
- :hover。鼠标悬停
- :focus。元素获取焦点

> 在 CSS 定义中，a:hover 必须被置于 a:link 和 a:visited 之后；a:active 必须被置于 a:hover 之后。

##### UI 元素状态伪类选择器

- :checked。选中的复选按钮或者单选按钮表单元素
- :enabled。所有启用的表单元素
- :disabled。所有禁用的表单元素

> UI 元素状态伪类选择器主要是针对 Form 表单元素进行操作，最常见的使用方式如设置 "type="text" 有 enable 和 disabled 两种状态，enable 为可写状态，disabled 为不可状态。

##### 结构伪类选择器

> 思否伪类总结：https://segmentfault.com/a/1190000019122105 （**有图示**）

- :fisrt-child。父元素的第一个子元素
- :last-child。父元素的最后一个子元素的元素
- :root。元素所在文档的根元素。在 HTML 文档中，根元素始终是 html，此时该选择器与 html 类型选择器匹配的内容相同
- :nth-child(n)。父元素的第 n 个子元素。其中 n 可以是整数（1，2，3）、关键字（even，odd）、也可以是公式（2n+1）,而且 n 值起始值为 1，而不是 0。
- :nth-last-child(n)：父元素的倒数第 n 个子元素。此选择器与 :nth-child(n) 选择器计算顺序刚好相反，但使用方法都是一样的，其中 :nth-last-child(1) 始终匹配最后一个元素，与 last-child 等同。
- :nth-of-type(n) 。父元素内具有指定类型的第 n 个元素
- :nth-last-of-type(n)。父元素内具有指定类型的倒数第 n 个元素
- :first-of-type。父元素内具有指定类型的第一个元素，与 nth-of-type(1) 等同
- :last-of-tye 。父元素内具有指定类型的最后一个元素，与 :nth-last-of-type(1) 等同
- :only-child 。父元素只包含一个子元素，且该子元素匹配元素
- :only-of-type。选择父元素只包含一个同类型子元素，且该子元素匹配选择元素
- :empty。选择没有子元素的元素，而且该元素也不包含任何文本节点

使用结构伪类选择器的好处是可以根据元素在文档中所处的位置，来动态地选择元素，从而减少 HTML 文档对 id 或类的依赖，有助于保持代码干净整洁。
另外需要注意的是，在结构伪类选择器中，子元素的序号是从 1 开始的。

#####  :not 

一个否定伪类，用于匹配不符合参数选择器的元素。

#### 伪元素选择器

> 伪元素的前缀是两个冒号 (::) ， 同样是添加到选择器后面去选择某个元素的某个部分。伪元素创建了不存在 DOM 树中的元素，并为其添加样式。例如，::after 选择元素后，在其内容后使用 content 添加内容。虽然可以看到添加的内容，但是这些内容实际上不存在 DOM 树中。

##### ::after(:after)

使用`::after`会创建一个伪元素，该伪元素会成为选中元素的最后一个子元素

```html
<div class="bulb-lists">
  <div class="bulb"></div>
  <div class="bulb"></div>
  <div class="bulb"></div>
  <div class="bulb"></div>
  <div class="bulb"></div>
  <div class="bulb"></div>
  <div class="bulb"></div>
  <div class="bulb"></div>
  <p>哇塞，好亮呀！</p>
</div>

p::after{
    content: '✨✨✨'
}
```



### 分组选择器

[选择器列表](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Selector_list)（[Selector list](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Selector_list)）

**<font color="red">,</font>** 是将不同的选择器组合在一起的方法，它选择所有能被列表中的任意一个选择器选中的节点。 **语法**：`A, B`

[选择器列表无效化](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Selector_list#选择器列表无效化)

使用选择器列表的一个缺点是，以下两段 CSS 代码并不不等价：

```
h1 { font-family: sans-serif }
h2:maybe-unsupported { font-family: sans-serif }
h3 { font-family: sans-serif }
```

```
h1, h2:maybe-unsupported, h3 { font-family: sans-serif }
```

这是因为，在选择器列表中如果有一个选择器不被支持，那么整条规则都会失效。

解决这个问题的一个方法是使用 [`:is()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:is) 选择器，它会忽视它的参数列表中失效的选择器，但是由于 [`:is()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:is) 会影响优先级的计算方式，这么做的代价是，其中的所有选择器都会拥有相同的优先级。

### 组合器

| 名字           | 语法   | 作用                                   |
| -------------- | ------ | -------------------------------------- |
| 后代组合器     | A B    | 匹配所有位于任意 A元素之内的B元素。    |
| 直接子代组合器 | A > B  | 匹配直接嵌套在A 元素内的 B元素         |
| 一般兄弟组合器 | A ~ B  | 匹配同一父元素下，A元素后面所有的B元素 |
| 紧邻兄弟组合器 | A + B  | 匹配所有**紧邻**在A元素后面的B元素     |
| 列组合器       | A\|\|B | 属于表格行节点 会匹配A作用域内的B元素  |



## 继承和层叠（完成）

### 层叠

> 层叠是指当元素应用了多个样式规则，哪个规则优先应用。 如果你曾经遇到过无法理解为什么某些CSS似乎没有应用的情况，那可能是层叠没有运用好。 层叠与继承紧密相关，继承定义了子元素可以继承父元素的样式属性。 它还与特异性有关，不同的选择器具有不同的特异性，当有几个选择器可以应用于一个元素时，继承可以决定应用哪个规则。注意：为了理解所有这些内容，我建议阅读MDN CSS简介中的 层叠和继承。如果你正在尝试将一些CSS应用于一个元素，那么你的浏览器开发者工具是开始最好的地方。看看下面的例子，我用元素选择器 h1  将 h1 标题设置为橙色。同时，我也使用类选择器设置h1 设置为紫色。 由于类更具体，因此h1是紫色的。 在开发者工具中，您可以看到元素选择器被划掉，因为它没有被应用。 一旦你看到浏览器正在获取你的CSS（但其他东西已经推翻了它），那么你可以开始找出原因。

![](https://img-blog.csdnimg.cn/2020052507071679.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzM0MjEwNQ==,size_16,color_FFFFFF,t_70)

1. 内联样式表的权值最高 1000；
2. ID 选择器的权值为 100
3. Class 类选择器的权值为 10
4. HTML 标签选择器的权值为 1

#### CSS 优先级法则：

A 选择器都有一个权值，权值越大越优先；
B 当权值相等时，后出现的样式表设置要优于先出现的样式表设置；
C 创作者的规则高于浏览者：即网页编写者设置的CSS 样式的优先权高于浏览器所设置的样式；
D 继承的CSS 样式不如后来指定的CSS 样式；
E 在同一组属性设置中标有“!important”规则的优先级最大；

### CSS样式继承

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-19_16-56.png)

将属性应用在ul元素上时，这个元素会采用该声明，并且将这个值再沿着树向下传播到后代元素，并一直继续，直到再也没有更多的后代元素继承这个值为止。

**大多数框模型都不能继承** 如属性border

## 盒子模型(完成)

### CSS 盒子模型(Box Model)

IE模型和标准模型唯一的区别是内容计算方式的不同，如下图所示：

![图2.IE模型宽度计算示意图](https://segmentfault.com/img/remote/1460000013069520)

**IE模型元素宽度width=content+padding**，高度计算相同

![图3.标准模型计算示意图](https://segmentfault.com/img/remote/1460000013069521)

**标准模型元素宽度width=content**，高度计算相同



```
所有HTML元素可以看作盒子，在CSS中，"box model"这一术语是用来设计和布局时使用。

CSS盒模型本质上是一个盒子，封装周围的HTML元素，它包括：边距，边框，填充，和实际内容。

盒模型允许我们在其它元素和周围元素边框之间的空间放置元素。
```



下面的图片说明了盒子模型(Box Model)：


![CSS box-model](https://www.runoob.com/images/box-model.gif)

不同部分的说明：

- **Margin(外边距)** - 清除边框外的区域，外边距是透明的。
- **Border(边框)** - 围绕在内边距和内容外的边框。
- **Padding(内边距)** - 清除内容周围的区域，内边距是透明的。
- **Content(内容)** - 盒子的内容，显示文本和图像。

为了正确设置元素在所有浏览器中的宽度和高度，你需要知道的盒模型是如何工作的。

## 格式上下文（完成）

> Block Formatting Context, 块级格式化上下文，一个独立的块级渲染区域，该区域拥有一套渲染规格来约束块级盒子的布局，且与区域外部无关



<img src="/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-19_21-37.png" style="zoom:25%;" />

前置知识

首先了解一下常见的定位方案

<img src="/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-19_21-44.png" style="zoom:25%;" />

<img src="/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-19_21-38.png" style="zoom:25%;" />

<img src="/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-19_21-39.png" style="zoom:25%;" />

**BFC可以看做是元素的一种属性，一旦元素拥有了BFC属性后，这个元素可以看作是隔离了的独立容器**

### BFC的作用

#### 外边距重叠现象

*同属于一个BFC块级上下文的元素，margin会重叠并取最大值，往往会干扰原本布局规划*需要给两个元素分配不同的BFC

<img src="/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-19_21-47.png" style="zoom:75%;" />

创建了两个不同的BFC

<img src="/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-19_21-46.png" style="zoom:75%;" />

#### 清除浮动

> 计算BFC的高度时，浮动元素也参与计算

<img src="/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-19_22-02.png" style="zoom:75%;" />

#### 阻止元素被浮动元素覆盖

> BFC的区域不会与float box重叠。

<img src="/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-19_22-05.png" style="zoom:75%;" />

#### 如何创建BFC

```bash
1、postion 为absolute 和fixed的元素
2、float不为none的元素
3、overflow不为visible的元素
4、弹性元素（display为 flex 或 inline-flex元素的直接子元素）
5、网格元素（display为 grid 或 inline-grid 元素的直接子元素）
6、内联块元素，即display的值为inline-block的元素；
7、流式布局根元素，display值为flow-root的元素；
8、表格单元格（元素的 display为 table-cell，HTML表格单元格默认为该值）
```

## 标准流（完成）

>作者：忧忧自恼
>链接：https://www.jianshu.com/p/4921ba9e101d
>来源：简书
>著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

> **文档流**指的是元素排版布局过程中，元素会**默认**自动从左往右，从上往下的**流式排列方式**。并最终窗体自上而下分成一行行，并在每行中从左至右的顺序排放元素。

### 标准流的微观现象

- 空白折叠现象。

```html
// 比如，如果我们想让img标签之间没有空隙，必须紧密连接.
<img src="img/00.jpg"/><img src="img/02.jpg"/>
```

- 高矮不齐，底边对齐
- 自动换行，一行写不完时，换行写

### 标准文档流等级

------

***分为两种等级：块级元素和行内元素；\***

```js
 //**块级元素：**--------------------------------------------------------
 1).霸占一行，不能与其他任何元素并列
 2).能接受宽、高
 3).如果不设置宽度，那么宽度将默认变为父亲的100%，即和父亲一样宽
 //**行内元素：**--------------------------------------------------------
 1).与其他元素并排
 2).不能设置宽、高。默认的宽度就是文字的宽度
 在HTML中，标签分为：文本级和容器级；
 文本级：p、span、a、b、i、u、em//文本p标签是块级元素
 容器级：div、h系列、li、dt、dd
```



<img src="https://upload-images.jianshu.io/upload_images/11161012-0486c38c90e22cd2.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/758/format/webp" style="zoom: 50%;" />





## 进入或离开流

## 布局

## 对齐

## 尺寸

### CSS单位（完成）

![](https://www.w3cplus.com/sites/default/files/blogs/2019/1901/figure-3.png)



## 响应式设计

## 字体和排版

## 变形和动画

