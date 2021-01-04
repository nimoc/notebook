# notebook

库如其名,笔记本

**每隔一个月回顾一次,能有效巩固知识和消灭未读完的书**

## 重要思想

封装的意义不能只是复用，最重要的是对程序设计逻辑分层的体现。

优秀的设计，简洁实现，满足复杂的需求。
> 花时间花心思做出优秀的设计，好的设计变成简洁的程序实现，让简洁的实现能满足复杂的需求。
> 因为简洁所以能提高可维护性和可拓展性。

程序设计是有边界的
> 设定边界能减少耦合，也能在实现成本和满足需求之间找到平衡点。

发散与收敛
> 程序设计可以在思考阶段天马行空的发散思维想到最复杂的实现和设计，然后逐步收敛。最终得到优秀的设计和简单的实现。

规范的意义
>在实现阶段应通过代码规范和代码分层设计让编码实现简单不易出错。（类型安全，代码分层，编码风格）

## 文章/教程/书

(Golang 新手可能会踩的 50 个坑-转载翻译)[https://github.com/nimoc/notebook/issues/7]

信息量大干货多

-------

[Go语言趣学指南](https://book.douban.com/subject/34951096/)

入门数据，简单的介绍了一些go特性。

[Go程序设计语言](https://book.douban.com/subject/27044219/)

系统的学习go,官方性质书籍。

------

[微服务设计](https://book.douban.com/subject/26772677/)

带着项目经验去看，防止在微服务中踩坑。从而导致做出一团乱麻的微服务。

-----

[深入理解计算机系统](https://book.douban.com/subject/26912767/)

通过了解“底层”知识让“高层”代码写的更好。

-----

[算法](https://book.douban.com/subject/19952400/)

由浅入深，看得懂的算法书

----

[上下文 Context](https://draveness.me/golang/docs/part3-runtime/ch06-concurrency/golang-context/)

[深入理解Golang之context](https://github.com/nimoc/notebook/issues/6)

----

[Go Blog 中文翻译](https://learnku.com/docs/go-blog)

> 全,详细

----

[跟着 mosh 学编程](https://programmingwithmosh.com/)

> 国外不废话视频教程 ,可在哔哩哔哩寻找中字版

----

[在Go中使用文件-转载](https://github.com/nimoc/notebook/issues/5)

> 一个字：全

---

## 各种要注意的坑

### 时间时区

参考资料

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

这个问题我在 [gofree](http://github.com/goclub/sql) 封装解决了,但是如果直接使用 sqlx 一定要注意。

## 简短的编程技巧

### 挖掘需求

举例：要求用户输入内容模糊搜索 name 和 mobile 时。编写SQL `SELECT name,mobile from user WHERE name like "%?%" OR name like "%?%"`可以满足要求。
还可以判断请求的字符串是不是手机号，使用 `SELECT name,mobile from user WHERE name like "%?%"` 或 `SELECT name,mobile from user WHERE mobile like "%?%"`。
即了解需求为：根据用户输入的内容使用不同条件的查询。

这这是个简单的例子，实际解决日常需求时。有隐藏的更深的需求。需求方可能提出的是要求，而**透过要求了解需求**。这样能事半功倍，并且降低复杂度。

### 好的变量函数的命名是对逻辑的准确表达

售出 `GoodsSell(count)` 和 减少库存 `GoodsReduceInventory(count)` 看似差不多，实则不同，好的函数名能提高可维护性。避免后续维护时候混淆。 供逻辑层使用的函数 GoodsSell 这个命名更好。

### 如何规划代码分层

多想，多实验，多思考。不要抱有固有偏见，永远认为自己代码分层完美就永远无法找到更好的方法。社区很多方法都很粗糙，不要先入为主被误导。

比如随处可见的 MVC 很糙。相对更清晰的可以是： 

1. 客户端协议层（http rpc）
2. 业务逻辑 （service） 
3. 持久化存储（database）

更细致还有 消息队列 高速缓存 （mq cache） 在 MVC的 model 都无法体现。

要基于目前工作环境做出自己对分层的理解，并谨慎的试验。代码架构永无止境。

### SQL审核 where 范围

在业务中如果漏掉关键性 `where` 条件会导致非常严重的事故。例如:

`SELECT * FROM event_user WHERE event_id = ? AND user_id = ? LIMIT 1`

如果漏掉 event_id sql依然能运行，但是数据不属于当前活动的，且不容易被发现。
可以通过单元测试检查出错误，但严谨的做法是通过多处设卡防止错误。
方法：
在SQL审核流程由DBA基于对表的理解，判断是否漏掉了 event_id。（上例中 where 只有 user_id 是没意义的，因为 event_id 和 user_id 是联合主键）
负责review的同事也进行相同的检查。

这需要 ORM 或者 QueryBuilder 对SQL审查支持良好，链式ORM想要做到SQL审查很麻烦。

[gofree](http://github.com/goclub/sql) 在接口设计上就考虑到了SQL审查

### 减少重复逻辑,而不是减少看似重复的代码

判断代码是否要封装,要基于逻辑是否重复,而不是代码重复.有时看似代码重复,实则是不一样的逻辑.

### 消除 Go 中的 _ =

```go
func ArticleFirst () struct {ID string} {

}
func AuthorByArticleID () (struct{}, bool) {

}
aricle, hasAricle := ArticleFirst()
if !hasAricle { panic(errors.New("不存在"))) }

// bad code
author, _ := AuthorByArticleID(aricleID)

// good code
author, hasAricle := AuthorByArticleID(aricleID)
```

假定 AuthorByArticleID 第二个参数 永远都是返回 true，是不严谨的，一旦出现 false 就会难以发现和排查的 bug.

正确的做法是不忽略任何返回值，必须需要  _ = 的情况则需要注释说明

```go
// 不需要获取 Content-Length
_, err = w.Write(bytes) ; if err != nil {panic(err)}
```

像  `AuthorByArticleID` 这种情况如果觉得99%的情况下不会出现 has false,则在内部进行 panic 处理
```go
func AuthorByArticleID () (model struct{}) {
    has := false
    // some code
    if !has {
    	panic(errors.New("当前作者不存在"))
    }
}
```

至于是返回 bool 还是内部处理，需要自己根据业务场景去决定。没有绝对的教条。

> 不允许忽略任何 error, 如果不知道怎么处理就  `if err != nil { panic(err) }` 。 千万不要直接出现  `jsonb, _ := json.Marshal(v)` 的代码

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


### 猜测bug

遇到复杂bug时先猜一下为什么会出现bug,什么地方出现了不符合预期的结果


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

### 打破固定思维

我母语编程语言是 JavaScript，JavaScript 中有一个 [Array.prototype.some()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/some) 方法,切换其他语言，例如切换到 go 语言中可能会下意识的实现

```go
type StringList []string
func (sList StringList) Some(callback func(index int, item string) bool) bool {
	for index, item := range sList {
		if callback(index, item) {
			return true
		}
	}
	return false
}
hasA := sList.Some(func (index, item) {
	if item == "a" {
		return true
	}
	return false
})
```

实际上 JavaScript 的some实现的不好，名字取得不好。应该是

```go
func (sList StringList) Find(callback func(index int, item string) (find bool) ) (foundIndex int, found bool) {
	foundIndex = -1
	for index, item := range sList {
		if callback(index, item) {
			found = true
			foundIndex = index
			return 
		}
	}
	return
}
foundIndex, found := sList.Find(func (index, item) (find bool) {
	if item == "a" {
		return true
	}
	return false
})
```

### 自我沟通和共同阅读

代码大全 软件架构设计这类抽象概念的书籍条件允许的情况下寻找与自己水平相当的人共同阅读交流心得,基于编码经验来讨论书中观点更能有效的理解.

### 推动团队技能成长

从团队负责人角度发动所有同事在业余时间学习了解**公司业务所需的技能和未来的技术方向**,推动鼓励分享.通过分享归纳,让参与者基于分享者的分享对某个自己不了解的点有初步了解,通过分享少走弯路,因为分享者已经筛选过学习资源.然后去深入学习. 技术一把手需要对分享内容进行总结,谈论发表观点是民主的,但是对于分享的总结和深入哪个技术方向是由**技术负责人独断**的.对于有争议的点可以用时间去观察分析,很多点不着急有定论.重在建立合作分享式学习氛围.

### 多尝试不同的语言和不同的编码方式

程序员不要将自己限定在一门语言中,多去尝试不同的语言.会打开视野会解决很多以前弄不明白的问题.会让自己的积累爆发出来.并且切换语言时应该接收新语言的编程范式,千万不要带入自己之前常用语言的思维去写另外一个语言的代码.在对不同语言了解足够多之后会总结出自己认为好的编码风格,并且在各个语言中都会有应用.

### 不要绝对相信权威教条

很多事情权威或者官方的话会让你不解,比如某个编码规则.一些权威或教条解释的不清楚导致知其然不知其所以然.在一个不熟悉的领域,比如新的编程语言,新的学科时,可以在开始时候努力思考和遵循权威和教条,因为大部分权威和教条是对的.但是当发现权威和教条与自己的想法冲突时,可以多去思考.通过努力和自身足够聪明就能看到问题的本质,自己悟道所以然.此时就可以按照自己的想法解决问题.而不受限与权威或教条.

### 要努力做到完美但是也要暂时接受不完美

在遇到一个问题时,不一定有完美的解决方案,不要把大量的世界花在想完美的解决方案上.自己根据当时的情况决定寻找完美解决方法的时间.选择当前能想到的的最好的解决方案,不要顾虑当前想到的是不是最好的.要**努力追寻完美但接受很多事物是做不到完美的**,  可以在事后时间允许的情况或者有更多经验阅历的情况下再寻找更完美的解决方案. 为了防止忘记,所有要有 notebook 这个项目记录.

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

## 程序设计案例

### 选择和禁用
选择某些区域发红包，某些区域不发红包。 若采取选择指定区域的方式则会造成数据库查询量大（1000多个省市区信息），通过分析采用选中加禁用可减少数据量。并注意不要交由前端去进行选择数据的“压缩”
