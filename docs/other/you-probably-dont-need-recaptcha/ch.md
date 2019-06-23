## [你可能不需要ReCAPTCHA](https://www.yuque.com/ysfe/ykx/recaptcha)

<b>[Kevin Davis](https://twitter.com/Nearcyan)@2019-06-11  [原文链接](https://kevv.net/you-probably-dont-need-recaptcha/)</b> 

> *翻译&校验：[freedom](https://github.com/yylifen)* 
> 

当面对阻止垃圾邮件和自动的恶意通信损害其服务的需求时，Google的[reCAPTCHA](https://en.wikipedia.org/wiki/ReCAPTCHA)常常是许多网站管理员使用的首选工具。在这篇文章中，我通过几个原因解释了为什么reCAPTCHA通常不是用于这个目的最佳解决方案，因为它通常是不必要的，还会给用户带来一些不便，并且让用户无法选择跳过他们的密集跟踪和指纹识别。针对ReCAPTCHA的各种威胁模型我提出了几种替代解决方案，以及一般使用验证码的最佳做法。

> [ReCAPTCHA在维基百科的中文链接](https://zh.wikipedia.org/wiki/ReCAPTCHA)

![](https://yylifen.github.io/sundries-trans/other/you-probably-dont-need-recaptcha/images/recaptcha.png)

### reCAPTCHA是有害的

reCAPTCHA是谷歌为任何网站管理员在自己的服务范围内提供的又一免费产品。reCAPTCHA是如何合法地区分人类用户和机器人呢？reCAPTCHA广泛依赖于用户指纹，强调“这个用户是谁？”而不是普通的“这个用户是人类吗？”。值得注意的是，当用户登录了他们的Google帐户时，成功地解决reCAPTCHA多么容易，同时也允许了Google将他们的行为与他们的真实身份联系起来。对于非谷歌浏览器的用户来说，也[经常会出现](https://news.ycombinator.com/item?id=20147015)[类似的效果](https://news.ycombinator.com/item?id=20147015)，他们注意到在Firefox浏览器上完成reCAPTCHA需要花费更多的时间。这与谷歌多年来为提高市场份额而使用的[许多其他反竞争技术](https://wccftech.com/former-mozilla-vp-says-google-intentionally-damaged-firefox/)是一致的。

尽管精准确定ReCAPTCHA的工作方式是非常困难的，谷歌不仅严重混淆了这部分JavaScript，而且还使用其自身的字节代码语言实现了整个虚拟DOM，但仍有许多尝试反向工程一些客户端代码，以及对服务器端逻辑如何操作进行理论。逆向工程ReCAPTCHA的[初步尝试](https://github.com/toogle/InsideReCaptcha)显示了大量已收集的信息，包括但不限于：插件、用户代理、IP地址、屏幕分辨率、执行时间、时区、语言、单击/键盘/触摸信息、许多浏览器特定功能和CSS评估的测试结果、有关画布元素呈现的信息以及cookie，包括在过去6个月内放置在Google帐户的相关信息。

有一个很好的理由可以解释为什么reCAPTCHA使用google.com的域名而不是一个特定于reCAPTcha的域名。这允许Google接收他们已经为你设置的任何cookie，有效地绕过了设置第三方cookie的限制，并允许与大多数用户使用的Google所有其他服务进行流量关联。reCAPTCHA收集了足够多的信息，可以可靠地解除许多用户的匿名性，尽管这些用户只是想证明他们不是机器人。由于JavaScript甚至需要马上查看reCAPTCHA，即使是运行诸如TBB(Tor Browser Bundle)这样的软件的用户也可能发现自己泄露的信息比他们想要的要多，例如，如果他们调整了浏览器窗口的大小(不鼓励使用reCAPTCHA正是出于这个原因)。

相应地，网站管理员要在他们的网站上使用Google的ReCAPTCHA就必须链接到Google的隐私和术语页面（默认情况下，以一个小的8px样式包含在表单中，使它们看起来不可用）。虽然谷歌过去拥有自己的隐私和用于重新捕获的术语页面，但这些链接不再是针对ReCAPTCHA的，一般都是Google服务的所有用户的[隐私](https://policies.google.com/privacy?hl=en)和[术语](https://policies.google.com/terms?hl=en)页面，而不管使用哪个服务，或者如果用户（或甚至希望）Google帐户开始使用。因此，接受这些术语（隐含地，通过尝试证明你不是一个机器人）会授予谷歌允许他们为其正常用户对你的服务所做的**一切**，很少有关于具体完成的信息（GDPR可能在此没有什么作用，因为ReCAPTCHA是为了达到阻止垃圾邮件目的）。不仅在ReCAPTCHA框中的无用链接从未被用户打开，也没有Google徽标或视觉参考来指示ReCAPTCHA是Google服务，因此许多用户有零表示他们刚刚同意所有Google的跟踪，只是因为他们试图在你的网站上留下反馈或创建一个ticket。如果你认为你可以在不使用Google服务的情况下使用Internet，请尝试使用Internet，而无需填写单个ReCAPTCHA，因为某些用户需要支付账单、提交其税费，有时甚至使用政府网站（如果你以某种方式管理此服务，则下一次尝试不向Gmail/GSuite地址发送电子邮件，或使用GoogleAPI进行更令人兴奋的挑战）。

值得注意的是，在此程度上关心用户隐私可能不在大多数网站的关注范围之外。许多网站已经与谷歌的服务（通常包括Google分析、Google广告、GoogleAPI、Google标签管理器、Google静态资源、GoogleOAuth、GoogleComputeEngine等）紧密耦合，因此添加Googlecatcha似乎可以忽略不计。尽管如此，不同的网站有不同的价值和不同的用户，许多人不想要求用户同意谷歌的跟踪和基本使用。除了谷歌之外，ReCAPTCHA强制集权对任何人都是不好的。

除了reCAPTCHA使用的隐私含义之外，对于许多类用户来说，实际的验证码非常繁琐，有时变得非常困难，以至于用户发现自己根本无法完成验证码验证。[用户讨厌reCAPTCHA](https://github.com/google/recaptcha/issues/286)。[他们](https://www.reddit.com/r/rant/comments/8yezeq/fuck_recaptcha/)[真的](https://devrant.com/rants/1754031/fuck-recaptcha-fuck-google-for-making-recaptcha-fuck-it-dont-be-evil-my-foot)[很讨厌](https://www.neogaf.com/threads/the-select-all-images-with-a-street-sign-recaptcha-is-a-godless-creation-of-evil.1410504/)[reCAPTCHA](https://news.ycombinator.com/item?id=19059390)。[reCAPTCHA](https://recaptcha.sucks/)[是](https://strugee.net/blog/2019/04/make-recaptchas-im-not-a-robot-accurate)[如此](https://uxdesign.cc/recaptcha-is-uxs-worst-nightmare-c28b7341e88a)[令人讨厌](https://www.reddit.com/r/unpopularopinion/comments/9haexn/googles_recaptcha_needs_to_be_fucking_made_illegal/)，以至于[一些网站](https://www.4chan.org/pass)的盈利模式是，每年向用户收取20美元的费用，以禁用reCAPTCHA，而这是成千上万的用户支付的。如果这听起来像一个伟大的新商业模式对你来说，现在你想添加reCAPTCHA到你的网站的每一页，以争取最大的利润，我会告诉你。每次你想要食物或水的时候，我都会强迫你完成一个reCAPTCHA，直到你在第一周后死于营养不良。我读过无数用户的帖子，他们对一项过度使用reCAPTCHA的服务感到非常沮丧，以至于他们发誓再也不会使用这个令人不快的网站了。这些人通常是[无障碍](https://www.w3.org/TR/turingtest/#version-2-are-you-a-robot)的智能用户，他们厌倦了被当作垃圾用户对待，浪费时间。善待你的用户，并帮助将他们必须解决的reCAPTCHA数量降到最低，这样才能允许他们使用Internet。

如果用户试图通过使用妨碍谷歌服务和跟踪的软件(如VPN、TBB和许多反跟踪浏览器加载项和修改)来“选择”谷歌的服务和跟踪，那么reCAPTCHA就会变得更加困难。为了演示“非常无聊”的含义，下面是我使用TBB解决一个reCAPTCHA的实时记录：

<!-- ![](https://yylifen.github.io/sundries-trans/other/you-probably-dont-need-recaptcha/images/recaptcha-1.webm) -->
<video width="90%" controls="" src="https://yylifen.github.io/sundries-trans/other/you-probably-dont-need-recaptcha/images/recaptcha-1.mp4"></video>

我很幸运，只需要完成两项挑战。有时有十个甚至更多。看了上面的视频，你可能会想：“我知道网络很慢，但我不知道它有那么慢！”你注意到这一差异是正确的。如果打开WebDeveloper控制台，我们可以看到，对于新的验证码映像的HTTP请求只需要几百毫秒。尽管如此，谷歌混淆的JavaScript故意每次将新图像的出现推迟几秒钟，我确信这与机器人被迫等待时放弃的事实有关。这不是一个好的方式来对待那些不想做无报酬劳动的用户，并且被谷歌进行指纹识别。请记住，上面的视频展示了reCAPTCHA用户体验(一些[用户脚本](https://greasyfork.org/en/scripts/31088-morecaptcha/code)可能改进的)最坏的情况之一，并且普通用户的体验要快得多，只要他们不试图阻止Google的任何跟踪，并且不会犯很多错误。

除了这种乏味的事情之外，用户正在执行的实际劳动直接用于使Google受益。不过，不要担心，因为谷歌急于吹嘘你所从事的无私的人道主义，选择了reCAPTCHA，并在其主[页面](https://developers.google.com/recaptcha/)中说明了以下内容：

> "数以亿计的上限每天都被人们解决。reCAPTCHA积极利用了人类的努力，将解决卡普查问题所花费的时间用于数字化文本、注释图像、建立机器学习数据集。"
>

这无疑是一种非常好的方式，可以说服你对强迫你的用户从事直接有益于世界上最强大的监视公司的无偿劳动感到满意。reCAPTCHA是可以自由选择也是有原因的。

最后，reCAPTCHA还是很受欢迎。这带来了一些好处，但这也意味着需要花很大的努力来打破reCAPTCHA，而这些努力都可能影响到你的网站，你的验证码实现与其他一百万个完全相同。由于这一点，多年来他们[发表了](https://uncaptcha.cs.umd.edu/papers/uncaptcha_woot17.pdf)[许多](https://www.blackhat.com/docs/asia-16/materials/asia-16-Sivakorn-Im-Not-a-Human-Breaking-the-Google-reCAPTCHA-wp.pdf)[论文](https://www.lancaster.ac.uk/staff/wangz3/publications/ccs18.pdf)，[他们](https://github.com/ecthros/uncaptcha)[破坏了](https://github.com/ecthros/uncaptcha2)[reCAPTCHA](https://science.sciencemag.org/content/358/6368/eaag2612)，通常Google会对其进行修改，以改进自己的验证码。也有[有偿](https://www.deathbycaptcha.com/)[服务](https://www.deathbycaptcha.com/)，使用人工来解决收费客户的限额问题，每人不到一分钱。有关绕过reCAPTcha的现代和用户友好的示例，请参见Buster。[Buster](https://github.com/dessant/buster)是一个现代的浏览器扩展(Firefox Chrome Opera)，它通过使用语音识别为你完成reCAPTCHA音频挑战来帮助你解决困难的上限。


### 并不总是需要验证码

在实现验证码之前，首先要考虑的是否有必要这样做。要帮助评估这个问题，请考虑你的威胁模型是否关心自定义或未定制的垃圾邮件。未定制的垃圾邮件普遍存在于许多Internet协议中，在服务器上启用HTTP、SSH或许多其他协议之后，你将很快遇到它。它通常是不明智的，执行成本低，容易阻止，即使没有上限。然而，定制的垃圾邮件是为特定的公司、服务、网站或用户而编写的垃圾邮件。由于定制的垃圾邮件是由能够为你的服务量身定做的参与者创建的，因此它比未定制的垃圾邮件更危险，需要更多的努力才能有效地限制它。

**许多开发人员大大高估了定制垃圾邮件的可能性。**作为一个有能力的程序员，很容易想象如果你的服务有足够的奉献精神，人们可以毫不费力地用垃圾邮件来摧毁你的服务。你可以想象一个恶意的程序员写了一个简单的脚本，通过使用Curl和bash就可以让你的网站无法拒绝垃圾邮件。即使你有一个验证码，你可以想象他们使用OCR或机器学习自动绕过它，然后使用代理和VPN自动绕过你的IP速率限制。在这种富有想象力的恍惚状态中，你已经忘记了99%的用户根本不知道如何做到这一点，甚至不知道Curl或HTTP是什么。此外，你的服务很可能给那些有能力的攻击者提供很少的潜在回报。

仅仅因为有人可能花几个小时（或几分钟）编写程序来给你的网站发送垃圾邮件，但这并不意味着每个人都会。你关于最新的蔬菜熏肉的个人博客对任何人来说都不是一个高优先级的目标。将ReCAPTCHA添加到你的联系我页面是一个很好的方法，可以让任何人都不能与你交谈。我已经运行了几个拥有数百万页面视图的网站，这些网页视图已收到零自定义滥用，并与其他具有类似体验的网站管理员交谈。codingferror.com的JeffAtwood[曾写过](https://blog.codinghorror.com/captcha-effectiveness/)：

> 我的博客的评论形式受到我所说的“纯验证码”的保护，在这种情况下，验证码每次都是相同的。这必须是有史以来最无效的验证码，但它阻止了99.9%的评论垃圾邮件。
>

这不是建议什么都不做，忽视基本安全，对攻击毫无准备，而是现实地考虑你的威胁模型，并只吧验证码应用到必要的地方。

### 未定制垃圾邮件的reCAPTCHA替代方案

对于未定制的垃圾邮件，很少需要完整的验证码实现。本节列出一些简单有效的技巧，以阻止大多数未定制的垃圾邮件影响你的网站。

**隐藏表单元素**

未定制的垃圾邮件不够智能，无法知道何时应该填写表单元素或不应该填写表单元素。例如，添加名为“url”的表单元素并将其隐藏在CSS中，允许你拒绝使用它填充的任何请求（垃圾邮件渴望执行）。要保持可访问性，请确保向该元素添加标签，以便使用屏幕阅读器的用户不会填写该标签。其他良好的隐藏表单元素名称包括“网站”、“firstname”、“lastname”、“email”和“name”，除非它们已合法使用。

**静态问题**

未定制的垃圾邮件也是如此的不智能，以至于他们不能正确回答简单的问题，比如“什么是23？”，或者“这个网站的名字是什么？”这些问题有效地阻止了几乎所有未定制的垃圾邮件。常用的软件栈，如WordPress和Drupal都有[免费的](https://wordpress.org/plugins/wp-math-captcha/)[插件](https://www.drupal.org/project/riddler)，可以让你快速创建这样的问题。

**社区特定问题**

如果你的网站是以社区为中心的，如论坛或博客，你可以问一个社区特定的问题，你社区的潜在成员应该知道答案。这是阻止用户加入你认为不应该参与的社区的一个简单而伟大的方法，要么是因为他们缺乏基本的相关知识，要么是因为他们无法或不愿意学习。例如，一群数学家可能会要求用户给出一个简单的公式或求解一个方程，给出它的图像。

![](https://yylifen.github.io/sundries-trans/other/you-probably-dont-need-recaptcha/images/complex_captcha.gif)

另一个例子是，一群小众媒体鉴赏家社区可能会要求用户识别他们认为对他们共同文化很重要的某个特征。

![](https://yylifen.github.io/sundries-trans/other/you-probably-dont-need-recaptcha/images/anime_captca.jpg)

> 社会成员的质素是最重要的。

**JavaScript**

我是否提到了未经定制的垃圾邮件是不科学的？基础的JavaScript不是由大多数未自定义的垃圾邮件执行或解析的，因此使用它计算表单元素的值也是有效的。JavaScript还可以用于提交表单本身、正确设置CSRF令牌或执行许多其他简单任务。如果你的站点有许多用户禁用了JavaScript，则最好提供另一种解决方案。

**第三方服务**

从WordPress插件(如[AKismet](https://akismet.com/get/))、垃圾邮件检测API(如[StopForumSpam](https://www.stopforumspam.com/))，以及评估用户或IP(如[abuseIPDB](https://www.abuseipdb.com/about))的API，有许多免费(和付费)第三方服务可以帮助你以大多数用户看不到的方式阻止垃圾邮件。

### 自定义垃圾邮件的reCAPTCHA替代方案

如果你的经营规模足够大，或者是如果自动使用你的网站本身就足够有利可图，那么定制的滥用最终会因为某种原因而发生。**请记住，验证码只是帮助验证给定用户是人的工具。它不是唯一的工具来解决这个问题，它也不是适合每一个用例的工具。没有任何解决方案是完美的，只希望可以阻止更多的攻击者滥用你的服务。**本节按复杂程度大致增加的顺序列出了reCAPTCHA的一些替代方案。

**Django简单的验证码**

[Django简单的验证码](https://github.com/mbi/django-simple-captcha)为Django项目提供了一个简单的验证码。

![](https://yylifen.github.io/sundries-trans/other/you-probably-dont-need-recaptcha/images/django_simple_captcha.png)

> Django简单的验证码，这实际上已经阻止了许多攻击者。

**Laravel5的验证码**

[Laravel5的验证码](https://github.com/mewebstudio/captcha)为Laravel项目提供了一个可定制的验证码。

![](https://yylifen.github.io/sundries-trans/other/you-probably-dont-need-recaptcha/images/laravel_captcha.png)

> Laravel5的验证码，是可定制的

**特定于CMS的验证码**

流行的CMS解决方案通常具有适合于基本目的的至少一个简单的验证码插件。以下是[WordPress](https://wordpress.org/plugins/really-simple-captcha/)、[Drupal](https://www.drupal.org/project/draggable_captcha)和[通用PHP](https://www.phpcaptcha.org/try-securimage/)的一些示例。

![](https://yylifen.github.io/sundries-trans/other/you-probably-dont-need-recaptcha/images/phpcaptcha.png)

> PHP安全图像验证码

![](https://yylifen.github.io/sundries-trans/other/you-probably-dont-need-recaptcha/images/drupal_captcha.png)

> Drupal匹配+幻灯片验证码

**自定义JavaScript脚本功能**

正如基础的JavaScript脚本停止大多数未定制的垃圾邮件一样，更高级的脚本也可以阻止大量定制的垃圾邮件。例如，有些网站要求你滑动jQuery滑块元素才能成功提交表单。对于[WordPress](https://wordpress.org/plugins/slider-captcha/)、[jQuery](https://github.com/ist-dsi/jquery-ui-slider-captcha)([jQueryUI滑块](https://jqueryui.com/slider/)、[引导滑块](https://github.com/seiyria/bootstrap-slider))、[Prestashop](https://catalogo-onlinersi.net/en/add-ons-prestashop-modules/264-slide-captcha-prestashop-module.html)、[Node](https://www.npmjs.com/package/slider-puzzle-captcha)等等这样的例子，尽管这些示例可能不适合生产使用，而且我还没有对它们进行测试。

![](https://yylifen.github.io/sundries-trans/other/you-probably-dont-need-recaptcha/images/slider.png)

> 滑到解锁

只要将真正的JavaScript评估作为一个需求，就会提高攻击者的门槛，并且无需用户执行任何操作即可完成。如果你选择编写大量自定义前端代码来评估用户，请确保对每种类型的设备和日志故障进行广泛的用户测试，以便对它们进行分析以进一步消除错误。

**Capy拼图验证码**

[Capy](https://www.capy.me/products/puzzle_captcha/)提供了一个简单的拼图验证码，它要求用户将一个拼图块拖到一个空槽中。

![](https://yylifen.github.io/sundries-trans/other/you-probably-dont-need-recaptcha/images/capy.png)

> 不费吹灰之力就能完成拼图的所有乐趣

**SolveMedia**

[SolveMedia](https://www.solvemedia.com/publishers/captcha-type-in)为各种流行的软件栈提供了验证码和相应的插件，包括vBulletin、WordPress、MediaWiki、Dupal、Joomla等。验证码可以根据用户的威胁分数来缩放其难度。

![](https://yylifen.github.io/sundries-trans/other/you-probably-dont-need-recaptcha/images/solvemedia.png)

> 他会在你最不经意的时候来的

如果出于某种原因，你觉得有必要从验证码的实现中获利，请不要担心，因为也有适合小部分的[资本主义](https://i.imgur.com/TEdBeDa.png)[反乌托邦](https://i.imgur.com/dgGvgKF.png)的版本：

![](https://yylifen.github.io/sundries-trans/other/you-probably-dont-need-recaptcha/images/solvemedia_evil.png)

> 请回答这个验证问题

**Geetest**

[Geetest](https://www.geetest.com/en/)似乎使用了一些指纹技术，但其他方面的效果类似于大多数拼图验证码。它因为在世界上最大的加密货币交易所之一上使用而闻名。

![](https://yylifen.github.io/sundries-trans/other/you-probably-dont-need-recaptcha/images/geetest.png)

这个列表比较全面，许多类似的捕获被排除在其中。如果你是一个软件工程师，你可能认为许多这些捕获可以通过软件解决，虽然是正确的，但却没有得到解决。从理论上来说，验证码应该是一个完美的turing测试，但在实践中，它们只是用来使攻击你的服务更加困难，从而使垃圾邮件不再具有成本效益。即使是完美的捕获，也不能保证阻止一切攻击。尽管如此，除非你运行非常受欢迎的服务，否则你可能会感到惊讶的是，攻击者愿意执行JavaScript脚本或执行OCR以自动攻击你的网站。

### 验证码的最佳做法

如果你已经确定需要一个验证码，那么请考虑是否真的有必要在所有你想要控制自动化的地方实现它。向用户显示更少的验证码不仅提供了更好的用户体验，而且还提高了KPI(如用户体验和用户留存)。

**在可能的情况下限制使用频率**

由于验证码的目的是确认最终用户是人，用户通常只需正确解决一次捕获。如果你希望限制某个操作以确保用户不会频繁执行该操作，请考虑使用频率限制作为备选方案（或与captchcha结合使用）。

**验证码使用合理的阈值**

为要用验证码限制的操作设置合理的阈值。不要在一次失败的登录尝试之后向用户提供验证码，而是允许多次尝试。以这种方式强行使用安全密码是不可行的，而且如果数据库泄漏的凭据自动与你的服务交叉验证，登录后失败的验证码甚至都不会起作用。

停止向试图读取内容的用户显示验证码。如果你的博客要求我完成一个验证码仅仅因为我使用一个超级可怕的VPN作为你的CDN的“高级军事级机器人保护”功能，我将关闭标签。有时，验证码对于只读操作更合理，比如停止活跃的应用程序级DDoS攻击。但是你的博客并不是其中之一。

**不需要重复验证码的解决方案**

如果验证码是可能失败验证并在失败时重新加载的窗体的一部分，则如果用户正确地解决了第一个验证问题，就不要强迫用户解决另一个验证码问题。这就避免了用户在修改输入时不得不连续几次求解验证码(例如，坚持你的革命性密码策略，该策略至少需要一个不可打印字符、一个埃及象形文字和一个仅限iOS的表情符号)。

**明智地使用其他验证来源**

考虑一下，你是否已经合理地验证了一个用户在以前与他们的交互中很可能是一个人。如果一个用户有一个确定的电子邮件地址和电话号码或正确的双因素认证，它可能没有必要给他们一个验证码。同样，如果一个用户已经成为付费客户几个月了，没有问题，并且试图用他们现有的账单信息进行新的购买，那么现在也不是让他们填写验证码的时候。我提到这个只是因为我以前必须这样做。

### 未来的验证方式

需要注意的是，**一个资源充足的攻击者可以在某种程度上绕过任何你现有的机制**。当一项服务拥有10亿用户(facebook和twitter)或其他原因而导致滥用(任何与加密货币有关的内容)时，在试图验证用户时必须做出艰难的权衡。

面对这种情况，一些规模非常大的服务不仅使用reCAPTCHA，而且还执行电话或电子邮件验证，并采用大量的自定义自动检测启发式方法。Twitter就是一个很好的例子，因为新用户需要同时完成reCAPTCHA并(通常)验证一个电话号码。最重要的是，Twitter的整个团队都致力于阻止垃圾邮件的滥用，然而这个平台仍然存在着数百万垃圾邮件的问题，就像Facebook一样。虽然要求电话验证会给匿名带来不幸的后果，但大多数平台一开始并不打算匿名使用。一个更大的挑战是在需要用户匿名的环境中试图阻止垃圾邮件，我在本节的末尾提供了一些例子。

随着机器学习的现状，构建一个用户友好的验证码变得越来越困难。对高级验证码的一些最[有效的](https://uxplanet.org/accessibility-vs-security-breaking-captchas-by-exploiting-their-accessibility-features-d0c805839144)攻击，如reCAPTCHA，只需接受给定的挑战并查询机器学习API来自动解决。现在我们有了[许多](https://cloud.google.com/products/ai/)[API](https://aws.amazon.com/machine-learning/)[服务](https://azure.microsoft.com/en-us/services/cognitive-services/)来准确地标记音频、图像、视频等等，这只是变得更加强大，就像机器学习一样。

尽管不可能有完美的验证码，有人写了一些文章谴责验证码已经死了[十多年](https://blog.codinghorror.com/captcha-is-dead-long-live-captcha/)，因为真正的负面(作为人类传递的软件)的可能性越来越大。尽管如此，大多数互联网并没有被垃圾邮件淹没。智能软件工程师在FAANG工作赚更多的钱，而不是用未经请求的假广告来覆盖互联网，至少目前是这样。对于物理安全的一个潜在的错误类比，请记住，我们有可以打破门、窗、相机、传感器、锁等更多的物理物品。然而，这些保护措施仍然是实体安全系统的基本特征。它们通常不是不可能被破坏的，而是使攻击者的工作变得更加困难，使努力和回报比率达到足以阻止大多数攻击者的程度。

不管即将到来的人工智能霸主地位，更大的系统倾向于支持的当前路径包括验证一个特定的用户是谁，而不仅仅是试图验证他们是否是人。电话验证，有时甚至图片、身份证或地址验证，都是在有很大可能被滥用的大型服务中发现的，以及我们好朋友的reCAPTCHA。在尝试更好地保持匿名性的同时验证用户是比较困难的，但是那些被确定的用户通常会找到聪明的方法来做到这一点。一些很好的例子包括[隐私通行证](https://privacypass.github.io/)([协议文件](https://www.petsymposium.org/2018/files/papers/issue3/popets-2018-0026.pdf))，允许用户匿名跳过密码，如果他们已经解决了一个问题，苹果的新的[查找我的设备](https://www.wired.com/story/apple-find-my-cryptography-bluetooth/)功能，允许苹果设备用[BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy)广播他们的位置，这样它只能被原始设备的所有者读取，以及众所周知的安全系统，例如[非对称密码](https://en.wikipedia.org/wiki/Public-key_cryptography)，[密码散列](https://en.wikipedia.org/wiki/Cryptographic_hash_function)，[差别化隐私](https://en.wikipedia.org/wiki/Differential_privacy)[等等](https://en.wikipedia.org/wiki/Shamir%27s_Secret_Sharing)，这些系统通常可以在系统中巧妙地实现，以提高安全性，并且常常是匿名的。可以用来帮助验证用户和减少垃圾邮件的其他一些技术包括[工作证明](https://en.wikipedia.org/wiki/Proof_of_work)和[小额支付](https://en.wikipedia.org/wiki/Micropayment)，这两种技术在大多数流行的加密货币(如比特币和电子货币)中已经成功地使用了十多年，尽管在日常场景中仍然很难实现。

如果你是Twitter或Facebook，则任何验证码都不会解决你的所有问题。对于其他每个人来说，仍然有很多简单的工具和启发方式，它们在帮助阻止滥用方面取得了很大的作用。对你的用户表现很好，尽量不要强迫他们花时间完成谷歌的ReCAPTCHA。他们会更青睐你的网站。

如果你喜欢这篇文章或者指出不足了，可以在[Twitter](https://twitter.com/Nearcyan)上和我打个招呼。

> 这篇文章发布在[software](https://kevv.net/category/software/)和[WebDev](https://kevv.net/category/webdev/)，标签有：[CAPTCHA](https://kevv.net/tag/captcha/)、[Google](https://kevv.net/tag/google/)、[reCAPTCHA](https://kevv.net/tag/recaptcha/)、[安全性](https://kevv.net/tag/security/)、[垃圾邮件](https://kevv.net/tag/spam/)、[UI](https://kevv.net/tag/ui/)和[UX](https://kevv.net/tag/ux/)。
