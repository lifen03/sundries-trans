## 像@keyframes和@media这样的@rule有多特别？

作者：[Chris Coyier](https://css-tricks.com/author/chriscoyier/) @2019-07-30  [原文地址](https://css-tricks.com/how-much-specificity-do-rules-have-like-keyframes-and-media/)

> 翻译：[林毅](https://www.yuque.com/gzlinyi)  校验：[freedom](https://github.com/yylifen)

前几天我遇到一个很奇怪的问题，特殊性是关于选择器的，而@rules不是选择器，所以这两者时没有关系的吗？

为了证明这一点，我们可以在@rules的内部和外部使用相同的选择器，看看它是否会影响特殊性。

```css
body {
  background: red;
}
@media (min-width: 1px) {
  body {
    background: black;
  }
}
```

背景是黑色的。但是……那是因为媒体查询增加了特殊性吗？让我们把它们调换一下看看。

```css
@media (min-width: 1px) {
  body {
    background: black;
  }
}
body {
  background: red;
}
```

背景是红色的，所以不是。红色背景被获取，只是因为它在样式表中的位置更后面。媒体查询不会影响特殊性。

> 如果感觉选择器增加了特殊性并用相同的选择器覆盖了其他样式，那么很可能只是因为它出现在样式的后面。

尽管如此，最初问题中的 `@keyframe` 还是让我开始思考。当然，关键帧可以影响样式。不是特殊性，但如果样式最终被覆盖，就会感觉像是特殊性。

我们看看下面这个[小例子](https://codepen.io/chriscoyier/pen/EqargG)：

```css
@keyframes winner {
  100% { background: green; }
}
body {
  background: red !important;
  animation: winner forwards;
}
```

你可能认为背景会是红色，尤其它带了 `!important` 规则。（顺便说一句， `!important` 并不影响特殊性；这是每个规则的事情。）它在Firefox浏览器中是红色的，但在Chrome浏览器中是绿色的。所以这是需要注意的怪事。(据埃斯特尔·威尔[Estelle Weyl]称，至少从2014年开始，它就是一个bug。）


