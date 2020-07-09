作者： Vladimir Kovpak | 译者：henrycjchen

# 像专家一样使用 panic

![i do it cause i need to](https://hackernoon.com/hn-images/1*GZkzQ4sJtZ8sP9eInIfz3w.jpeg)

(i do it cause i neet to...)

## 预备知识

本文假定你已经熟悉 go 语言及其 panic/recorer 函数、以及任何其他具有异常（try-catch）概念的编程语言。



## 介绍

你可能已经在[《The Little Go Book》](https://www.openmymind.net/The-Little-Go-Book/)中看到诸如这样的句子：

> *Go 处理错误的首选方式是 return values，而不是抛出错误*

也许你在 go wiki 上看到过[《CodeReviewComments》](https://github.com/golang/go/wiki/CodeReviewComments#dont-panic)页面，上面写着：

> 不要在平常的错误处理中使用 panic，而应使用 error 和多参数返回*

另外，你可能已经看过[《Effective Go》](https://golang.org/doc/effective_go.html#panic) 的文章，上面说：

> *向调用者报告错误的通常方法，是将错误作为额外的返回值返回*

此外，你可能已经在 Dave Cheney 的博客[《Why Go gets exceptions right》](https://dave.cheney.net/tag/panic) 上看到了：

> *当你在 Go 中使用 panic，小心被吓坏。出问题可别想甩锅了，完蛋的肯定是你*



似乎 panic 最好在自己的项目中避免...

但这是否就意味着没人使用 panic 呢？

查查就知道了！我们对流行的 go 项目执行下面的指令，看是否真的没人使用 panic

```
grep "panic(" -r --include=*.go . | wc -l
```

结果：

```
+-------------+-----------------+
| name        | count of panics |
+-------------+-----------------+
| go          |            4050 |
| kubernetes  |            4087 |
| gin         |              46 |
| prometheus  |             693 |
| terraform   |            1161 |
| echo        |              14 |
| dep         |             157 |
| gorilla mux |               9 |
| mysql       |               5 |
| pq          |              46 |
+-------------+-----------------+
```

嗯哼...



## 应如何对待 panic 

乍一看，文档、书本和文章都说不要使用 panic，但事实却正相反，到处都是 panic...

希望你能同意到：panic 不是简单的说“用或不用”就可以的。

因此，让我们试着深入探讨，分清用与不用的界限，为什么在 github 上有如此多的 panic，以及为什么所有的书和文档都不喜欢 panic。



## 什么是 panic

[官方文档](https://golang.org/pkg/builtin/#panic)：

> *内置函数 panic 停止当前 goroutine 的正常执行*

[PanicAndRecover](https://github.com/golang/go/wiki/PanicAndRecover) wigi:

> panic 和 recover 函数的表现与类似于一些其他语言中的异常和 try/catch

[Go by Example](https://gobyexample.com/panic): 

> panic 通常意味着出人意料的错误。大多数情况下，我们使用它来快速处理正常操作中不应该出现的错误。



好吧...现在感觉 panic 就像是其他语言中的异常，这也解释了前面提到的 github 的项目中有那么多的 panic 的原因。

但是，如果你看过 Dave Cheney 的博文 [《Why Go gets exceptions right》](https://dave.cheney.net/tag/panic)，你可能会看到:

> *你可能会以为 panic 就是 throw，但你错了*

这意味着 panic 与其他语言中的 `throw exception` 略有不同，并且有自己的优缺点。



### 优点

1. 与其他语言的 `throw exception` 一样，停止程序执行，释放整个函数的调用栈
2. 无需处理多个返回值，无需编写枯燥的检查（`if err != nil { // handle error }`），代码更容易阅读

### 缺点

1. 当你没使用 recover 的话，程序将终止
2. 当 go 执行释放堆栈时，它收集有关整个调用堆栈的信息，并且可能变慢
3. recover 函数返回 interface{}，因此你需要对获得的值做类型检查，这可能会变慢（特别是在 reflection 的情况下）。它不像其他语言直接 catch 到特定的异常
4. recover 不会捕获到 goroutinue 中的 panic，这也不像其他语言中的 try-catch



## 什么时候使用 panic

现在很明显 panic 是把利器，你在使用它之前必须三思。前面介绍中提到的那些警告也就都可以理解了。

[Effective Go](https://golang.org/doc/effective_go.html#panic) 中提到：

> 一个可能的反例就是初始化： 若某个库真的不能让自己工作，且有足够理由产生 panic，那就由它去吧。

如果在某些情况下，程序无法继续执行，你可以使用 panic 来停止程序



### 还有一个使用 panic 的理由

假如你的应用程序有复杂的业务逻辑和分层架构（更甚者，使用领域驱动模型），你则应该使用 panic。

你可能会恨我，但我相信这是唯一使你不被错误处理淹没的方法，业务逻辑也会更清晰。



## 哪里都是 panic

首先，介绍部分提到的数字告诉我们必需始终处理 panic（即使我们并没有在我们的代码中显式地使用 panic），因为我们调用的下游可能会 panic，甚至语言本身也会 panic，为了防止程序中断，我们必需使用 panic 处理函数，也即 `recover`。

这必须引起重视，由其当你的项目是面向用户的接口（从用户、其他服务中获取命令、请求，并提供结果/响应），即使在出现未处理的关键错误下，我们也必须保证能以确定的格式提供结果/响应。

因此，我们应该在 `main.go` 中如下处理：

```go
func main() {
    defer func() {
        if r := recover(); r != nil {
            // handle panic
        }
    }()
    // ...
}
```

这只是个简单的例子，你可以在[这里](https://blog.golang.org/defer-panic-and-recover)了解更多的信息。

同样重要的是，当你开始新的 goroutine 时，你必须使用 `defer-recover`，否则你将处理不了来自 goroutine 的 panic。

你可以在《Go in Pratice》一书中的《Handling errors and panics》一章了解更多信息，这里我截取了其中最有趣的图片：

![](https://hackernoon.com/hn-images/1*kgprUaP5FPqKdbP4z93f6g.png)

![](https://hackernoon.com/hn-images/1*RFQsIXxixfheh4tDCMUIsg.png)



## 语法糖

一旦开始更频繁地使用 panic，你还必须更频繁地执行 recover。为了更优雅地做到这点，你可以使用一些类似于 [recover](https://github.com/thepkg/recover) 的程序包。该程序包的主要思想是简化 panic 的恢复，并可以以下面的方式执行 recover：

```go
// Performs recover in case of panic with error ErrorUsernameBlank
// otherwise panic won't be recovered and will be propagated.
defer recover.One(ErrorUsernameBlank, func(err interface{}) {
  fmt.Printf("got error: %s", err)
})

// Performs recover in case of panic with error ErrorUsernameBlank or ErrorUsernameAlreadyTaken
// otherwise panic won't be recovered and will be propagated.
defer recover.Any([]error{ErrorUsernameBlank, ErrorUsernameAlreadyTaken}, func(err interface{}) {
  fmt.Printf("got error: %s", err)
})

// Performs recover for all panics.
defer recover.All(func(err interface{}) {
  fmt.Printf("got error: %s", err)
})
```

你可能会发现这种语法与其他语言中传统的异常捕获非常相似，但是其的主要目标是简单明了，并且容易阅读、理解和预测代码。



## 对照

我们来比较下两种方法：

1. return error
2. panic

为了进行比较，我们使用一个简单的示例，假设我们有：

1）facade：在 Facebook，Twitter 和 Pinterest 上创建用户的服务
2）controller：调用 facade 服务的控制器，检查错误并打印结果
序列图如下所示：

![](https://hackernoon.com/hn-images/1*6lepeB7q6MCWy-Xao_Mnmw.png)

#### 实现 1

```go
// controller

func SignUp(username string) {
	msg := "ok"
	if err := service.SignUp(username); err != nil {
		msg = err.Error()
	}
	fmt.Printf("[error] SignUp: %s \n", msg)
}
```

```go
// service

func SignUp(username string) error {
	if err := validation(username); err != nil {
		return fmt.Errorf("validation failed, error: %s", err)
	}
	if err := signUpFacebook(username); err != nil {
		return fmt.Errorf("facebook sign up failed, error: %s", err)
	}
	if err := signUpTwitter(username); err != nil {
		return fmt.Errorf("twitter sign up failed, error: %s", err)
	}
	if err := signUpPinterest(username); err != nil {
		return fmt.Errorf("pinterest sign up failed, error: %s", err)
	}
	return nil
}

func validation(username string) error {
	if len(username) == 0 {
		return fmt.Errorf("username cannot be blank")
	}
	return nil
}

func signUpFacebook(username string) error {
	if username == "bond" {
		return fmt.Errorf("username already taken")
	}
	return nil
}

func signUpTwitter(username string) error {
	if username == "leiter" {
		return fmt.Errorf("username already taken")
	}
	return nil
}

func signUpPinterest(username string) error {
	if username == "q" {
		return fmt.Errorf("username already taken")
	}
	return nil
}
```

（源码在[这里](https://github.com/cn007b/eop/tree/master/src/app/examples/error)）

在这里，你可以看到 controller 中的简单函数 SignUp，它调用 service.SignUp，然后检查服务中的错误，打印结果（清晰，简单明了）。

众所周知，此代码是通用的，可以处理 go 中的错误。 太好了！

但是当涉及到 service 时——你可以发现很多重复的代码，感觉没那么清爽…



#### 实现 2

```go
// controller

func SignUp(username string) {
	defer recover.All(func(err interface{}) {
		fmt.Printf("[pro_panic] SignUp: %s \n", err)
	})
	service.MustSignUp(username)
	fmt.Printf("[pro_panic] SignUp: %s \n", "ok")
}
```

```go
// service

func MustSignUp(username string) {
	mustValidation(username)
	mustSignUpFacebook(username)
	mustSignUpTwitter(username)
	mustSignUpPinterest(username)
}

func mustValidation(username string) {
	if len(username) == 0 {
		panic(c.ErrorUsernameBlank)
	}
}

func mustSignUpFacebook(username string) {
	if username == "bond" {
		panic(c.ErrorUsernameAlreadyTaken)
	}
}

func mustSignUpTwitter(username string) {
	if username == "leiter" {
		panic(c.ErrorUsernameAlreadyTaken)
	}
}

func mustSignUpPinterest(username string) {
	if username == "q" {
		panic(c.ErrorUsernameAlreadyTaken)
	}
}
```

（源码在[这里](https://github.com/cn007b/eop/tree/master/src/app/examples/pro_panic)）

在这里，你可以在 controller 中看到相同的函数 SignUp，该函数调用 service.MustSignUp，然后执行 recover（通过 [recover](https://github.com/thepkg/recover) 包），并打印结果（相同流程）。

如果你查看一下 service，你可能会发现它现在看起来更短、更简单，而且更容易阅读和理解其中的业务逻辑。



### 真的很糟糕吗

从技术上讲，这两种实现都是相同的，并提供相同的功能、相同的错误和相同的结果（你可以在[这里](https://github.com/cn007b/eop)查看）。

但一对比代码量——很明显，第二个更简单，您可以在下一张图片中看到它：

![](https://hackernoon.com/hn-images/1*15_iXfTggdlDDAxGWOU7QA.png)

另外，第一种实现没有 recover，但它应该要有，因为每个对用户友好的项目都必须有 recover，这意味着第一种实现将有更多的代码。



### panic 慢不慢

在小例子上执行 benchmarking 可能看起来很愚蠢，但不管怎样，让我们看看它看起来如何，并找出是否有异常的数字：

```
+---------------------------------+----------+----------+
| case                            | imp. #1  | imp. #2  |
+---------------------------------+----------+----------+
| error: username cannot be blank | 53000 ns | 45000 ns |
| error: username already taken   | 51000 ns | 46000 ns |
| ok                              | 32000 ns | 34000 ns |
+---------------------------------+----------+----------+
```

（你可以在[这里](https://github.com/cn007b/eop/blob/master/src/app/main.go)找到与此 benchmarking 相关的源代码）。

看起来在出错的情况下——panic 更快，但在成功的情况下，recover 需要一些开销…

请注意，所有提供的数字都以纳秒表示时间，

这意味着：对于这种特殊的情况，两种方法之间并没有很大的区别…



## Go 2 草案

你可能已经知道，在 go 2 中，错误处理将通过 `check-handle` 组合得到改进（如果不了解的话，可以[看一下](https://go.googlesource.com/proposal/+/master/design/go2draft-error-handling-overview.md)），它将以非常优雅的方式简化所有事情！

但它是否有助于构建复杂的分层应用程序？

对于非常简单的应用程序，比如我们的案例（controller-service），答案是肯定的。但不幸的是，对于大型应用程序，特别是对于支持领域驱动设计的应用程序，`check-handle` 没有帮助，相信你还是要用 panic…



## 总结

这篇文章的重点，是要表明 panic 只是一个工具，你不必害怕这个工具，你必须知道什么时候和如何使用 panic…

一旦你知道这个工具的优点和缺点，你就可以利用它来决定是否使用它。



## PS

你可以在[这里](https://github.com/cn007b/monitoring)找到具有分层架构（不是 DDD 而是许多层）的 demo 项目，它的构建思想是到处 panic，也许它是具有说明性的。

此外，你可以在[这里](https://github.com/cn007b/eop)找到更多使用这两种方法（errors vs panic）的例子。

如果你不喜欢 panic，您可能会找到[另一种方法](https://blog.golang.org/errors-are-values)：如何以另一种方式简化错误处理。



## 英文原文

[Panic like a PRO](https://hackernoon.com/panic-like-a-pro-89044d5a2d35)