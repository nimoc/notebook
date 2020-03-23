# notebook

库如其名,笔记本

## 文章教程

[使用dig进行依赖注入-机翻转载](https://github.com/nimoc/notebook/issues/2)

> 如果你觉得 dig 合胃口,你应该使用 [fx](https://pkg.go.dev/go.uber.org/fx?tab=doc). 

> 由浅入深的介绍了依赖注入,关于为什么要依赖注入应该看 [**架构整洁之道**](http://gdut_yy.gitee.io/doc-cleanarch/)

go 依赖注入导致必须使用 interface 模拟测试的问题可以通过 [monkey](https://github.com/bouk/monkey) 直接修改函数指针。因为使用 interface 会导致大量为了满足 interface 实现的测试struct和 func 。

---

[使用Wire进行依赖注入-机翻转载](https://github.com/nimoc/notebook/issues/1)

## 简短的编程技巧

不要总是设想代码是按预期运行的,尽可能的写一些判断日志来帮助自己排除发现问题. (并发,涉及IO的情况下都可能没有按预期运行)

` if (balance <0) { log("余额不应该小于0,可能是并发没控制好出错了") }`

## 小技巧

对于一些垃圾内容过多的网站比如 csdn 可以在搜索引擎使用 `-域名` 的方式进行过滤.

> 关键词 -csdn.net

在浏览器设置搜索引擎的网址格式为 `https://www.bing.com/search?q=%s -csdn.net` 这种就可以免去每次输入 `-csdn.net` 防止每次都恶心自己.

---

## 想法认知

### 编程语言是某几类场景下的解决方案

编程语言是语言作者将解决某一类问题所需要采用的编程方式提炼成编程语言，并加上很多限制来确保使用此语言时候尽可能的与作者设想的编程方式靠拢。（语言太过自由在不同的场景下不一定是好事）

> 最常见的是面向对象编程和函数式编程，如果硬是要在一个纯函数式编程的语言中加入面向对象，会举步艰难。

### TypeScript 与 JavaScript 

TypeScript 赋予了 JavaScript 静态类型的能力但是由于 JavaScript 的语法向前兼容的历史包袱和 TypeScript 不想限制 JavaScript 本身的灵活性.导致 **TypeScript 只是个类型注解**,并不是 强类型语言.又因为浏览器运行环境导致在运行时还是以 JavaScript 运行,会出现命名定义了类型是 number 结果运行时候还是可能会是 undefined . [响应数据 undefined](https://github.com/nimoc/blog/issues/33)

母语是 JavaScript 的开发人员去学习 TypeScript 比学习一门新的静态类型语言还要难.因为始终会以 JavaScript 动态灵活的思维去写 TypeScript 代码,而 TypeScript 为了满足 JavaScript 的灵活性,有大量的高级类型语法.有些已经高级到复杂的程度.并且因为复杂度高经常出现新手看不懂的 TypeScript 编译报错.

 TypeScript 很优秀.但是历史包袱和依附 JavaScript 本身会导致 TypeScript 很难写. 思维逻辑要转换,要 "自废武功" 去将一些以前 JavaScript 写起来非常简单的代码,用 TypeScript 使用很"啰嗦的"语法去实现.这种"啰嗦"真是静态语言严谨的代码风格. 所以**使用 TypeScript 需要在转换思维**.最好学习一门跟 JavaScript 很像的强静态类型语言,比如我推荐 golang.

在 **node 中一定要使用 TypeScript** ,因为后端的各个函数各个数据之间的调用是关联性很强的.相比前端而言,我认为后端是要解决整个面的复杂度,前端是要解决单个点的复杂度,点与点之间的复杂度不是特别高.难度不分高低,根据场景决定.

> 跑个题: 大部分前端代码对依赖注入的需求没有那么高,而依赖注入基于 TypeScript 才能更好的发挥作用

**在前端代码中不一定非要使用 TypeScript**, 在开发组件和开发核心高复用的模块时一定要用 TypeScript ,因为要确保稳定型和可维护性.但有些页面逻辑代码,将响应数据转换为渲染数据,并根据事件修改渲染数据的这些简单繁琐的逻辑是可以不用 TypeScript 的因为有时候时间不等人,项目 dealline 会逼得你 any 满天飞.

我们要实事求是,如果你时间允许的情况下应该全部 TypeScript 加上类型,如果在页面琐碎的ui逻辑中时间来不及,是允许使用 any 的.因为现实会让你还是写 any.不如我们**面对现实做出明确定义**,而不是一刀切的不允许用 any 或者一刀切不用 TypeScript.

## 纯函数依赖注入

纯函数式编程无法便捷的实现依赖注入,硬要实现会变成形式主义.没有解决问题反而导致代码更难以维护.所以一定要基于对象去管理依赖,并且包含依赖的对象由顶层控制.如果母语编程语言是 Java 这种对依赖注入支持的意见非常完善的向理解依赖注入会非常容易,如果母语编程语言是`javascript` `php` 这种语言想理解依赖注入则需要多看文章,或者直接学习其他对依赖注入支持的好的语言.否则会云里雾里永远不得要领.

理解

> 模块之间的依赖要基于接口而不是基于实现

非常重要,这句话有点像Go的名言

> 不要通过共享内存来通信，要通过通信来共享内存

他们都有共同的特点,因为他们都是对一个概念的简短描述.所以 **必须通过足够了解依赖注入和 go channel 后才能理解**

## 书籍列表

[**架构整洁之道**](http://gdut_yy.gitee.io/doc-cleanarch/)

> 一些观点印证了我的想法,并一些观点让我不清晰的观点更加明确,一些观点让给了我新的视角

[Robert C·Martin](https://book.douban.com/author/104377/books?sortby=collect&format=pic) 有很多作品可以都看一遍
