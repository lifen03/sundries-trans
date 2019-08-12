## [JavaScript性能开销之2019](https://www.yuque.com/ysfe/ykx/js)

Addy Osmani ([@addyosmani](https://twitter.com/addyosmani)) 发布于2019年6月25日。[原文地址](https://v8.dev/blog/cost-of-javascript-2019)

*翻译&校验：[freedom](https://github.com/yylifen)*

> <b>注：</b>如果你喜欢看演示文稿而不是阅读文章，那就享受下面的视频吧！如果没有，跳过视频并继续阅读。

<iframe width="713" height="426" src="https://www.youtube.com/embed/X9eRLElSW1c" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<!-- [Youtube视频地址：https://www.youtube.com/embed/X9eRLElSW1c](https://www.youtube.com/embed/X9eRLElSW1c) -->

*Addy Osmani在2019年PerfMatters大会上的现场演讲《[JavaScript性能开销](https://www.youtube.com/watch?v=X9eRLElSW1c)》.*

在过去几年中，JavaScript性能开销的一个重大变化是浏览器解析和编译脚本的速度有所提高。**在2019年，处理脚本现在的主要开销在于下载和CPU执行时间。**

如果浏览器的主线程忙于执行JavaScript脚本时可能会延迟响应用户交互，因此优化脚本执行时间和网络瓶颈可以改善用户体验。

### 高级实用指南

这对Web开发人员意味着什么？解析和编译成本**不再**像我们曾经想象的**那么慢**。关于优化JavaScript包的三个重点是：

* **提高下载速度**
    
    + 确保JavaScript包的尽可能小，特别是对于移动设备。小包可以提高下载速度，降低内存使用率并降低CPU成本。

    + 避免只有一个大包；如果一个包超过50-100kB，将它分割成独立的小包。(具有HTTP/2多路复用、多个请求和响应消息可以同时运行，从而减少了额外请求的开销。)

    + 在移动端尽可能较少包的大小，这主要是出于对网络带宽的考虑，同时也有利于降低内存使用率。

* **缩短执行时间**
    + 避免主线程一直忙于执行长时间下载、影响页面交互响应速度的[长任务](https://w3c.github.io/longtasks/)。下载后，脚本执行时间是现在主要的性能成本之一。

* **避免使用大型内联脚本**（因为它们仍然在主线程上进行了解析和编译）。 一个好的经验法则是：如果脚本超过1kB，请避免内联（因为1kB是[代码缓存](https://v8.dev/blog/code-caching-for-devs)为外部脚本启动时）。

### 为什么重视JavaScript脚本的下载和执行时间？

为什么优化JavaScript脚本的下载和执行时间很重要？下载时间对于低端网络至关重要。尽管4G(甚至5G)在世界各地逐渐普及，但我们网络的[有效连接类型](https://developer.mozilla.org/en-US/docs/Web/API/NetworkInformation/effectiveType)仍然不一致，我们中有许多人感觉网络速度像3G(甚至更糟)。

对于CPU速度慢的手机来说，JavaScript执行时间非常重要。由于CPU、GPU和热节流的不同，高端和低端手机的性能存在巨大差异。这对JavaScript的性能影响很大，因为JavaScript的执行速度取决于CPU。

事实上，一个页面在Chrome这样的浏览器中加载的总时间，有30%的时间可能是花在JavaScript执行上。下面是一个页面加载时工作负载相当典型的站点(Reddit.com)，它运行在高端桌面计算机上：

![](https://yylifen.github.io/sundries-trans/js/cost-of-javascript-2019/images/reddit-js-processing.svg)

*在V8中JavaScript处理时间占了页面加载期间所花费的时间的10-30%。*

在移动设备上，(Pixel3)Reddit的JavaScript执行时间，与高端设备相比，在中端设备(MotoG4)需要3–4倍，在低端设备（售价低于100美元的Alcatel 1X）需要6倍：

![](https://yylifen.github.io/sundries-trans/js/cost-of-javascript-2019/images/reddit-js-processing-devices.svg)

*Reddit的JavaScript执行时间在几个不同的设备类(低端、中端和高端)中的性能开销*

> <b>注：</b>Reddit对于桌面和移动网络的体验不同，因此在MacBookPro上的结果不能与其他设备上的结果相比。

当你试图优化JavaScript执行时间时，要关注可能长期独占UI线程的[长任务](https://web.dev/long-tasks-devtools/)。即使页面看上去已经准备好了，这些长任务可能会阻止关键任务的执行。把它们分解成更小的任务。通过拆分代码并对加载的顺序进行优先级排序，这样就可以使页面具有较低的输入延迟，更快地进行交互。

![](https://yylifen.github.io/sundries-trans/js/cost-of-javascript-2019/images/long-tasks.png)

### V8为解析和编译的性能提升做了什么？

从Chrome 60开始，V8中的原生JavaScript解析速度提高了2×10。与此同时，由于Chrome中其他并行化的优化工作，原生的解析(和编译)性能开销变得不那么明显和重要。

V8通过在Worker线程上解析和编译，将主线程上的解析和编译工作量平均减少了40%(例如在Facebook上减少了46%，在Pinterest上减少了62%)，性能能提升最大的是81%(YouTube)。这是除了现有的主线程之外的非主线程流解析和编译带来的性能提升。

![](https://yylifen.github.io/sundries-trans/js/cost-of-javascript-2019/images/chrome-js-parse-times.svg)

*不同版本的V8解析时间*

我们还可以把对不同版本V8的CPU时间影响的变化进行可视化。在相同的时间内，Chrome 61解析完Facebook的JS脚本时，Chrome75已经解析完Facebook的JS脚本和Twitter的JS脚本6次了。

![](https://yylifen.github.io/sundries-trans/js/cost-of-javascript-2019/images/js-parse-times-websites.svg)

*在Chrome 61解析Facebook的JS脚本时，Chrome 75可以同时解析Facebook的JS脚本和Twitter的JS脚本6次了。*

让我们深入研究这些变化是如何被解锁的。简而言之，脚本资源可以在Worker线程上进行流解析和编译，这意味着：

+ V8可以在不阻塞主线程的情况下解析和编译JavaScript。

+ 当完整的HTML解析器遇到`<script>`标记时，就开始进行流解析和编译。对于解析器阻塞脚本，HTML解析器产生，而异步脚本则继续。

+ 对于大多数现实世界中的连接速度，V8的解析速度比下载速度快，所以V8是在下载最后一个脚本字节后几毫秒完成解析和编译的。

说的具体一点就是，更老版本的Chrome会在开始解析脚本之前下载完整的脚本，这种方法很简单，但它并没有充分利用CPU。Chrome在41和68之间的版本，一旦下载开始，Chrome就开始在独立的线程上解析异步脚本和延迟脚本。

![](https://yylifen.github.io/sundries-trans/js/cost-of-javascript-2019/images/script-streaming-1.svg)

*脚本包含多个块。至少30kB后V8开始流式传输。*

在Chrome 71中，我们转到了一个基于任务的设置，调度程序可以同时解析多个异步或延迟脚本。这一变化的影响是主线程解析时间减少了20%，在实际网站上测量到的TTI/FID总体上提高了2%。

![](https://yylifen.github.io/sundries-trans/js/cost-of-javascript-2019/images/script-streaming-2.svg)

*Chrome 71移动到基于任务的设置中，调度程序可以同时解析多个异步或延迟脚本。*

在Chrome 72中，我们转而使用流作为主要的解析方式：现在也以这种方式解析常规同步脚本(但不是内联脚本)。如果主线程需要，我们也停止取消基于任务的解析，因为这只是不必要地重复了已经完成的任何工作。

[以前的Chrome版本](https://v8.dev/blog/v8-release-75#script-streaming-directly-from-network)支持流解析和编译，从网络输入的脚本源数据必须先进入Chrome的主线程，然后才能转发到流线程。

这通常导致流解析器等待已经从网络到达的数据，但由于在主线程上的其他工作(如HTML解析、布局或JavaScript执行)阻止了流任务，因此尚未转发到流任务。

我们现在正在试验在预加载上开始解析，而主线程反弹是一个预先阻止这一点的工具。

Leszek Swirski的演示更详细：

<iframe width="764" height="455" src="https://www.youtube.com/embed/D1UJgiG4_NI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<!-- [Youtube视频地址：https://www.youtube.com/embed/D1UJgiG4_NI](https://www.youtube.com/embed/D1UJgiG4_NI) -->

*LeszekSwirski在Blink 10上提出的[《零时间解析JavaScript》](https://www.youtube.com/watch?v=D1UJgiG4_NI)。*

### 这些变化你在DevTools中如何看到相关内容？

除了上面的内容之外，[DevTools中还有一个问题](https://bugs.chromium.org/p/chromium/issues/detail?id=939275)，它以一种暗示它正在使用CPU(完整块)的方式呈现了整个解析器任务。但是，每当需要数据(需要遍历主线程)时，解析器就会阻塞。由于我们从单一的流线程转移到流任务，这个问题变得非常明显。下面是你在Chrome 69中看到的内容：

![](https://yylifen.github.io/sundries-trans/js/cost-of-javascript-2019/images/devtools-69.png)

*DevTools问题，它以一种暗示它正在使用CPU(完整块)的方式呈现整个解析器任务。*

“解析脚本”任务显示耗时1.08秒。但是，解析JavaScript并没有那么慢！大部分时间除了等待数据遍历主线程之外，什么也不做。

Chrome 76描绘了一幅截然不同的画面：

![](https://yylifen.github.io/sundries-trans/js/cost-of-javascript-2019/images/devtools-76.png)

*在Chrome 76中，解析被分解成多个较小的流任务。*

一般来说，DevTools性能面板非常适合从宏观上分析页面上发生的事情。对于特定于V8的详细指标，如JavaScript解析和编译时间，我们建议[使用Chrome跟踪和运行时调用统计(RCS)](https://v8.dev/docs/rcs)。在RCS结果中，`Parse-Background`和`Compile-Background`告诉你在主线程上解析和编译JavaScript花费了多少时间，而`Parse`和`Compile`则捕获主线程度量。

![](https://yylifen.github.io/sundries-trans/js/cost-of-javascript-2019/images/rcs.png)

### 这些变化的实际影响有多大？

让我们来看看一些真实站点的例子，以及脚本流是如何应用的。

![](https://yylifen.github.io/sundries-trans/js/cost-of-javascript-2019/images/reddit-main-thread.svg)

*主线程与Worker线程在MacBookPro上解析和编译Reddit的JS时间*

Redid.com有几个100kB包，它们被包装在外部函数中，导致在主线程上大量的[懒编译](https://v8.dev/blog/preparser)。在上面的图表中，主线程时间才是关键，因为保持主线程的忙碌会延迟交互。Reddit的大部分时间都花在主线程上，而Worker线程和后台线程的使用率最低。

它们将一些较大的包拆分成更小的包(例如，每个包50kB)，这样就可以并行化最大化，这样每个包都可以被流解析，单独编译，并且在启动过程中减少主线程解析和编译。

![](https://yylifen.github.io/sundries-trans/js/cost-of-javascript-2019/images/facebook-main-thread.svg)

*主线程与Worker线程在MacBookPro上解析和编译Facebook的JS所花费的时间*

我们可以看看像Facebook.com这样的网站。它在292个请求中加载约6MB的压缩JS，其中一些是异步的，有些是预加载的，还有一些是以较低的优先级获取的。它们的许多脚本都非常小，粒度很小——这有助于后台和辅助线程上的整体并行化，因为这些较小的脚本可以同时进行流解析和编译。

请注意，你的网站或者应用程序可能不像facebook有这么多的脚本，也可能没有像facebook或gmail这样长期频繁使用，在桌面上，facebook或gmail这样包含很多脚本的页面这样处理可能是合理的。但是，一般来说，尽可能优化你的JS包，并且只加载所需的内容。

虽然大多数JavaScript解析和编译工作都可以在后台线程上以流式方式进行，但仍有一些工作必须在主线程上进行。当主线程繁忙时，页面无法响应用户输入。需要特别注意下载和执行代码对用户体验的影响。

> <b>注：</b>目前，并不是所有的JavaScript引擎和浏览器都将脚本流作为加载优化来实现。我们仍然相信，这里的总结指南能带来良好的用户体验。

### 解析JSON的性能开销

因为JSON语法比JavaScript语法简单得多，所以JSON可以比JavaScript更高效地解析。这些知识可以应用于提高Web应用程序的启动性能，这些应用程序提供了类似JSON的大型配置对象文本(例如内联Redux存储)。而不是将数据内联为JavaScript对象文本，如下所示：

```JavaScript
const data = { foo: 42, bar: 1337 }; // 🐌
```

…它可以用JSON字符串化的形式表示，然后在运行时解析JSON：

```JavaScript
const data = JSON.parse('{"foo":42,"bar":1337}'); // 🚀
```

只要计算JSON字符串一次，JSON.Analysis方法就比JavaScript对象文本要快得多，特别是对于冷负载。一个好的经验法则是将此技术应用于10kB或更大的对象，但是，与性能建议一样，在进行任何更改之前，要度量实际影响。

当对大量数据使用简单的对象文本时，还有一个额外的风险：它们可以被解析*两次*！

* 1.第一次传递发生在文字准备就绪时。

* 2.第二次传递发生在文字被延迟解析时。

第一关是无法避免的。幸运的是，通过将对象文本放置在顶层或[PIFE](https://v8.dev/blog/preparser#pife)中，可以避免第二次传递。

### 重复访问时解析和编译的开销如何？

V8的(字节)代码缓存优化可能会有所帮助。当第一次请求脚本时，Chrome会下载它并将其提供给V8进行编译。它还将文件存储在浏览器的磁盘缓存中。当第二次请求JS文件时，Chrome从浏览器缓存中获取该文件，并再次将其交给V8进行编译。但是，这次编译的代码是序列化的，并作为元数据附加到缓存的脚本文件中。

![](https://yylifen.github.io/sundries-trans/js/cost-of-javascript-2019/images/code-caching.png)

*V8中代码缓存工作方式的可视化*

第三次，Chrome从缓存中获取文件和文件的元数据，并将两者都交给V8。V8取消序列化元数据并跳过编译。如果前两次访问发生在72小时内，则代码缓存启动。如果服务工作者用于缓存脚本，Chrome还具有热切的代码缓存。在[Web开发人员的代码缓存](https://v8.dev/blog/code-caching-for-devs)中，你可以阅读更多关于代码缓存的信息。


### 结束语

2019年，下载和执行时间是加载脚本节省性能开销的主要瓶颈。为上面的折叠内容准备一小段同步(内联)脚本，并为页面的其余部分提供一个或多个延迟脚本。分解你的大型捆绑包，以便你只关注于用户需要时所需的发送代码。这样可以V8中实现并行化最大化。

在移动设备上，由于网络、内存消耗和较慢CPU的执行效率，你需要尽可能减少脚本的大小和数量。平衡延迟和缓存能力，以最大限度地增加主线程上可能发生的解析和编译工作量。

### 扩展阅读

+ [惊人的快速解析，第1部分：优化扫描器](https://v8.dev/blog/scanner)

+ [惊人的快速解析，第2部分：懒解析](https://v8.dev/blog/preparser)
