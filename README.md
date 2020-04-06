# notebook

库如其名,笔记本

## 文章教程

[跟着 mosh 学编程](https://programmingwithmosh.com/)

> 国外不废话视频教程 ,可在哔哩哔哩寻找中字版

----

[在Go中使用文件-转载](https://github.com/nimoc/notebook/issues/5)

> 一个字全

----

[MySQL锁总结-转载](https://github.com/nimoc/notebook/issues/3) 

> 此文易于理解

----

[Mysql锁：灵魂七拷问-转载](https://github.com/nimoc/notebook/issues/4) 

>此文能做到知其所以然

----

[使用dig进行依赖注入-机翻转载](https://github.com/nimoc/notebook/issues/2)

> 如果你觉得 dig 合胃口,你应该使用 [fx](https://pkg.go.dev/go.uber.org/fx?tab=doc). 

> 由浅入深的介绍了依赖注入,关于为什么要依赖注入应该看 [**架构整洁之道**](http://gdut_yy.gitee.io/doc-cleanarch/)

go 依赖注入导致必须使用 interface 模拟测试的问题可以通过 [monkey](https://github.com/bouk/monkey) 直接修改函数指针。因为使用 interface 会导致大量为了满足 interface 实现的测试struct和 func 。

---

[使用Wire进行依赖注入-机翻转载](https://github.com/nimoc/notebook/issues/1)

## 各种要注意的坑

### 时间时区

先记录下来参考资料,明天再详细整理一篇文章出来.从客户端提交时间格式到服务端逻辑,到数据库.

<details>

[UTC]([https://zh.wikipedia.org/wiki/%E5%8D%8F%E8%B0%83%E4%B8%96%E7%95%8C%E6%97%B6](https://zh.wikipedia.org/wiki/协调世界时))

[UTC+8]([https://zh.wikipedia.org/wiki/%E5%8C%97%E4%BA%AC%E6%97%B6%E9%97%B4](https://zh.wikipedia.org/wiki/北京时间))

[夏令时]([https://zh.wikipedia.org/wiki/%E5%A4%8F%E6%97%B6%E5%88%B6](https://zh.wikipedia.org/wiki/夏时制))

[GMT]([https://zh.wikipedia.org/wiki/%E6%A0%BC%E6%9E%97%E5%B0%BC%E6%B2%BB%E6%A8%99%E6%BA%96%E6%99%82%E9%96%93](https://zh.wikipedia.org/wiki/格林尼治標準時間))

[时间格式]([https://chenoge.github.io/2018/12/26/%E6%97%B6%E9%97%B4%E6%88%B3%E3%80%81%E6%97%B6%E5%8C%BA%E4%BB%A5%E5%8F%8A%E6%97%B6%E9%97%B4%E6%A0%BC%E5%BC%8F/](https://chenoge.github.io/2018/12/26/时间戳、时区以及时间格式/))

[go mysql 时区](https://www.jianshu.com/p/3f7fc9093db4)

[mysql teamstamp datetime](https://www.cnblogs.com/ivictor/p/5028368.html)


</details>

### sqlx

因为使用者不谨慎同时使用 `SELECT * ` 加上 `StructScan` 或 `db.Select`等方法 会导致数据库如果新增了字段,而 `struct` 没有新增会报错

`panic: missing destination name created_at in *f_test.User [recovered]`

这个问题我在 [gofree](http://github.com/og/gofree) 封装解决了,但是如果直接使用 sqlx 一定要注意, 有空了我再写个完整文章复现问题.

## 简短的编程技巧

### 布尔值的逻辑运算使用积极拒绝式

> 积极拒绝的意思就是一旦任何一个逻辑判断不通过就立即中断并不通过,只在函数最后编写通过代码



<details>

```go
type Event struct {
	AnyTime bool
	StartTime time.Time
	EndTime time.Time
	Disabled bool
}
// 积极拒绝式(好的)
func (v Event) Available() bool {
	if v.Disabled { return false }
	if !v.AnyTime {
		now := time.Now()
		if now.Before(v.StartTime) { return false }
		if now.After(v.EndTime) { return false}
	}
	return true // 积极拒绝的意思就是一旦任何一个逻辑判断不通过就立即中断并不通过,只在函数最后编写通过代码
}
// 排除式(有隐患)
func (v Event) BadCodeAvailable() bool {
	if v.Disabled { return false }
	if v.AnyTime {
		return true // 此行代码是个隐患,nimo 称之为排除式代码(排除AnyTime 为 true 时通过)
	} else {
		now := time.Now()
		if now.Before(v.StartTime) { return false }
		if now.After(v.EndTime) { return false}
		return true
	}
}
```
思考如何最开始没有 `Disabled` 而   `if v.Disabled { return false }`是后加的,如果在  `BadCodeAvailable` 中将 `Disabled` 的判断放在 v.AnyTime 后面是不是会出bug.而即使放在前面也会导致后续维护可能出现bug.

也肯呢个是积极通过,比如

```
pass := false
if v.Password == "some" {
  pass = true
}
if v.IP == "localhost" {
  pass = true
}

if !pass {
   response("拒绝访问")
}
// 注意这是是为了演示,实际情况下不可能密码硬编码写在代码中
```
</details>

### 不要让前端通过 url 获取参数

如果需要后端负责页面渲染,当遇到 `/user/{user}` `/user/nimo` 时,在前后端的角度都要告知对方不要在前端获取 `nimo` ,而是通过页面渲染时后端传递渲染参数告知前端数据
```js
{
  "user": "nimo",
}
```
此举可以增加维护性,当后端对 URL 做出调整时不会导致前端获取 URL 失败,如果你的项目是 SPA 完全由前端控制路由,则不需要注意这一点.

### switch 函数

`switch` 务必处理 `default` 你以为永远不会出现 `default` 实际上并不是绝对的. 如果认为绝对不会出现 `default` 就在 `default` 抛出友好消息的异常.另外应该通过封装 `switch` 函数来避免 `switch`遗漏 `case`. 后续出详细文章详细论证

### 消除泛型

净一切可能消除泛型，除非是 json 解析这种就是要用泛型的场景， 另外对于函数可变参数需要注意，如果参数必须偶数出现，则使用更明确的由2个参数组成的数组来限定。从而到达编译期检查到错误。

### 消除 null nil undefined 

各个语言都会有 `null` `nil` `undefined` 这样的设计,要净一切可能在代码层面消除他们.列举几个例子

go 的 sql.NullTime 类型

```go
type NullTime struct {
	Time  time.Time
	Valid bool // Valid is true if Time is not NULL
}
```


### 悲观编程

不要总是设想代码是按预期运行的,尽可能的写一些判断日志来帮助自己排除发现问题. (并发,涉及IO的情况下都可能没有按预期运行)

` if (balance <0) { log("余额不应该小于0,可能是并发没控制好出错了") }`

甚至两个相关业务模块中任一模块要预计其他模块出现bug的情况下,尽可能的让当前模块也不受到影响.


### 确保编译期尽可能的类型检查

保证编译器类型检查始终有效非常重要,在一定要用泛型时候也应该写一些只是用来编译检查的代码

甚至有时需要一些函数接口设计来防范

例如 typejson 中有一个设计
```js
function equalString(type string, value string) {
///
}
tj.equalString(v.name, "nimo") // v.name 是个字符串类型
```

## 小技巧

对于一些垃圾内容过多的网站比如 csdn 可以在搜索引擎使用 `-域名` 的方式进行过滤.

> 关键词 -csdn.net

在浏览器设置搜索引擎的网址格式为 `https://www.bing.com/search?q=%s -csdn.net` 这种就可以免去每次输入 `-csdn.net` 防止每次都恶心自己.

---

## 想法认知

### 推动团队技能成长

从团队负责人角度发动所有同事在业余时间学习了解**公司业务所需的技能和未来的技术方向**,推动鼓励分享.通过分享归纳,让参与者基于分享者的分享对某个自己不了解的点有初步了解,通过分享少走弯路,因为分享者已经筛选过学习资源.然后去深入学习. 技术一把手需要对分享内容进行总结,谈论发表观点是民主的,但是对于分享的总结和深入哪个技术方向是由**技术负责人独断**的.对于有争议的点可以用时间去观察分析,很多点不着急有定论.重在建立合作分享式学习氛围.

### 多尝试不同的语言和不同的编码方式

程序员不要将自己限定在一门语言中,多去尝试不同的语言.会打开视野会解决很多以前弄不明白的问题.会让自己的积累爆发出来.并且切换语言时应该接收新语言的编程范式,千万不要带入自己之前常用语言的思维去写另外一个语言的代码.在对不同语言了解足够多之后会总结出自己认为好的编码风格,并且在各个语言中都会有应用.

### 不要绝对相信权威教条

很多事情权威或者官方的话会让你不解,比如某个编码规则.一些权威或教条解释的不清楚导致知其然不知其所以然.在一个不熟悉的领域,比如新的编程语言,新的学科时,可以在开始时候努力思考和遵循权威和教条,因为大部分权威和教条是对的.但是当发现权威和教条与自己的想法冲突时,可以多去思考.通过努力和自身足够聪明就能看到问题的本质,自己悟道所以然.此时就可以按照自己的想法解决问题.而不受限与权威或教条.


> 我认为go 官方提供的 error 规范和方式好,强制自己理解和实践后还是觉得不好,最终按照自己的想法实行我觉得更好的错误处理方式
> https://github.com/og/x/tree/master/error

### 要努力做到完美但是也要暂时接受不完美

在遇到一个问题时,不一定有完美的解决方案,不要把大量的世界花在想完美的解决方案上.自己根据当时的情况决定寻找完美解决方法的时间.选择当前能想到的的最好的解决方案,不要顾虑当前想到的是不是最好的.要**努力追寻完美但接受很多事物是做不到完美的**,  可以在事后时间允许的情况或者有更多经验阅历的情况下再寻找更完美的解决方案. 为了防止忘记,所有要有 notebook 这个项目记录.

> web服务器端会需要类似 php die 的函数,但是go建议的其实是一层层的传递错误.我选择了个目前不完美的通过 panic 传递服务拒绝响应消息的解决方法 https://github.com/og/x/blob/master/reject/reject_test.go

### 编程语言是某几类场景下的解决方案

编程语言是语言作者将解决某一类问题所需要采用的编程方式提炼成编程语言，并加上很多限制来确保使用此语言时候尽可能的与作者设想的编程方式靠拢。（语言太过自由在不同的场景下不一定是好事）

> 最常见的是面向对象编程和函数式编程，如果硬是要在一个纯函数式编程的语言中加入面向对象，会举步艰难。

### TypeScript 与 JavaScript 

1. TypeScript 只是个类型注解
2. 使用 TypeScript 需要转换思维
3. node 中一定要使用 TypeScript
4. 在前端代码中不一定非要使用 TypeScript
5. 面对现实做出明确定义

<details>

TypeScript 赋予了 JavaScript 静态类型的能力但是由于 JavaScript 的语法向前兼容的历史包袱和 TypeScript 不想限制 JavaScript 本身的灵活性.导致 **TypeScript 只是个类型注解**,并不是 强类型语言.又因为浏览器运行环境导致在运行时还是以 JavaScript 运行,会出现命名定义了类型是 number 结果运行时候还是可能会是 undefined . [响应数据 undefined](https://github.com/nimoc/blog/issues/33)

母语是 JavaScript 的开发人员去学习 TypeScript 比学习一门新的静态类型语言还要难.因为始终会以 JavaScript 动态灵活的思维去写 TypeScript 代码,而 TypeScript 为了满足 JavaScript 的灵活性,有大量的高级类型语法.有些已经高级到复杂的程度.并且因为复杂度高经常出现新手看不懂的 TypeScript 编译报错.

 TypeScript 很优秀.但是历史包袱和依附 JavaScript 本身会导致 TypeScript 很难写. 思维逻辑要转换,要 "自废武功" 去将一些以前 JavaScript 写起来非常简单的代码,用 TypeScript 使用很"啰嗦的"语法去实现.这种"啰嗦"真是静态语言严谨的代码风格. 所以**使用 TypeScript 需要转换思维**.最好学习一门跟 JavaScript 很像的强静态类型语言,比如我推荐 golang.

在 **node 中一定要使用 TypeScript** ,因为后端的各个函数各个数据之间的调用是关联性很强的.相比前端而言,我认为后端是要解决整个面的复杂度,前端是要解决单个点的复杂度,点与点之间的复杂度不是特别高.难度不分高低,根据场景决定.

> 跑个题: 大部分前端代码对依赖注入的需求没有那么高,而依赖注入基于 TypeScript 才能更好的发挥作用

**在前端代码中不一定非要使用 TypeScript**, 在开发组件和开发核心高复用的模块时一定要用 TypeScript ,因为要确保稳定型和可维护性.但有些页面逻辑代码,将响应数据转换为渲染数据,并根据事件修改渲染数据的这些简单繁琐的逻辑是可以不用 TypeScript 的因为有时候时间不等人,项目 dealline 会逼得你 any 满天飞.

我们要实事求是,如果你时间允许的情况下应该全部 TypeScript 加上类型,如果在页面琐碎的ui逻辑中时间来不及,是允许使用 any 的.因为现实会让你还是写 any.不如我们**面对现实做出明确定义**,而不是一刀切的不允许用 any 或者一刀切不用 TypeScript.

</details>

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

----

[面向对象葵花宝典：思想、技巧与实践](https://book.douban.com/subject/26674160/)
