# 【待校验+封面】@rule 有多特别呢，例如@keyframes 和@media？

前几天我遇到这个问题，我认为这个问题很奇怪，特异性是关于选择性，而 at-rules 不是选择器，所以这两者不相关？

为了证明这一点，我们可以在 at-rules 的内部和外部使用相同的选择器，看看它是否会影响特异性。
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

背景是黑色的。但是…是因为媒体查询增加了特异性吗?我们把它们调换一下。

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

背景是红色的，所以不是。红色背景被获取，只是因为它在样式表的后面。媒体查询不会影响特异性。

> 如果感觉选择器增加了特异性并用相同的选择器覆盖了其他样式，那么很可能只是因为它出现在样式的后面。


尽管如此，最初问题中的 `@keyframe` 还是让我开始思考。当然，关键帧可以影响样式。不是特异性，但如果样式最终被覆盖，就会感觉像是特异性。

我们看下面这个[小例子](https://codepen.io/chriscoyier/pen/EqargG)：

```css
@keyframes winner {
  100% { background: green; }
}
body {
  background: red !important;
  animation: winner forwards;
}
```

你可能认为背景会是红色，尤其它带了 `!important` 规则。（顺便说一句， `!important` 并不影响特异性；这是每个规则的事情。）它在Firefox中是红色的，但在Chrome中是绿色的。所以这是需要注意的奇怪东西。(据埃斯特尔·威尔[Estelle Weyl]说，至少从2014年开始，它就是一个bug。）

原文：[https://css-tricks.com/how-much-specificity-do-rules-have-like-keyframes-and-media/](https://css-tricks.com/how-much-specificity-do-rules-have-like-keyframes-and-media/)
作者：Chris Coyier
