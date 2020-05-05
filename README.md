# notebook

库如其名,笔记本

**每隔一个月回顾一次,能有效巩固知识和消灭未读完的书**


## 重要思想

优秀的设计，简洁实现，满足复杂的需求。
> 花时间花心思做出优秀的设计，好的设计生成出的简洁的程序实现，让简洁的实现能满足复杂的需求。因为简洁所以能提高可维护性和可拓展性。但需要注意程序设计是有边界的，设定边界能减少耦合，也能在实现成本和满足需求之间找到平衡点。

## 文章/教程/书

[深入理解Golang之context](https://github.com/nimoc/notebook/issues/6)

----

[计算广告](https://read.douban.com/reader/ebook/23044373/?from=book)

理解业务才能让技术更好的服务商业,并且这本书中有很多广告的产品设计.

----

[Go Blog 中文翻译](https://learnku.com/docs/go-blog)

> 全,详细

----

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
