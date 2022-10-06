img标签在固定高宽的div里的显示，除了JS，以及背景图片，可以试一下使用css属性object-fit属性。
它的几个属性

```scss
fill（不保持纵横比缩放图片，使图片完全适应）
contain（保持纵横比缩放图片，使图片的长边能完全显示出来）
cover（保持纵横比缩放图片，只保证图片的短边能完全显示出来）
none（保持图片宽高不变）
scale-down（当图片实际宽高小于所设置的图片宽高时，显示效果与none一致；否则，显示效果与contain一致）
inherit
initial
unset
```

box-sizing

| content-box | 默认值。如果你设置一个元素的宽为 100px，那么这个元素的内容区会有 100px 宽，并且任何边框和内边距的宽度都会被增加到最后绘制出来的元素宽度中。 |
| ----------- | ------------------------------------------------------------ |
| border-box  | 告诉浏览器：你想要设置的边框和内边距的值是包含在 width 内的。也就是说，如果你将一个元素的 width 设为 100px，那么这 100px 会包含它的 border 和 padding，内容区的实际宽度是 width 减 去(border + padding) 的值。大多数情况下，这使得我们更容易地设定一个元素的宽高。 **注：**border-box 不包含 margin。 |
| inherit     | 指定 box-sizing 属性的值，应该从父元素继承                   |



padding-top 获取高度

当margin/padding取形式为`百分比`的值时，无论是left/right，还是`top/bottom`，都是以`父元素的width`为参照物的！



Window.getComputedStyle()

[摘要](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/getComputedStyle#摘要)

`Window.getComputedStyle()`方法返回一个对象，该对象在应用活动样式表并解析这些值可能包含的任何基本计算后报告元素的所有 CSS 属性的值。 私有的 CSS 属性值可以通过对象提供的 API 或通过简单地使用 CSS 属性名称进行索引来访问。

[语法](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/getComputedStyle#语法)

```
let style = window.getComputedStyle(element, [pseudoElt]);
```

- element

  用于获取计算样式的[`Element`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element)。

- pseudoElt 可选

  指定一个要匹配的伪元素的字符串。必须对普通元素省略（或`null`）。

**备注：** 在 Gecko2.0 (Firefox 4 / Thunderbird 3.3 / SeaMonkey 2.1)之前版本，参数 pseudoElt 是必要的。如果为 null，则不指定其他主要浏览器必须指定此参数。Gecko 已经更改为匹配其他浏览器的行为。。

返回的`style`是一个实时的 [`CSSStyleDeclaration`](https://developer.mozilla.org/zh-CN/docs/Web/API/CSSStyleDeclaration) 对象，当元素的样式更改时，它会自动更新本身。