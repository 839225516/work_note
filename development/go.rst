Go语言
=========

语言基础
-----------

**Go语言程序设计的一些规则**

Go语言之所以简洁，是因为它有一些默认的行为。

- 大写字母开头的变量是可导出的，即其他包可以读取，是公用变量；小写字母开头的不可导出，是私有变量。

- 大写字母开头的函数也是一样，相当于class中带public关键词的公有函数；小写字母开头就是有private关键词的私有函数。


**Panic和Recover**

Go语言没有像Java那样的异常机制，它不能抛出异常，而是使用了panic和recover机制。一定要记住，你应当把它作为最后的手段来使用，也就是说，你的代码中应当没有，或者很少有panic的东西。

**Panic**

panic是一个内建函数，可以中断原有的控制流程，进入一个令人恐慌的流程中。当函数F调用panic，函数F的执行被中断，但是F中的延迟函数会正常执行，然后F返回到
调用它的地方。在调用的地方，F的行为就像调用了panic。这一过程继续向上，直到发生panic的goroutine中所有调用的函数返回，此时程序退出。恐慌可以直接调用
panic产生，也可以由运行时错误产生，例如访问越界的数组。

**Recover**

recover是一个内建函数，可以让进入令人恐慌的流程中的goroutine恢复过来。recover仅在延迟函数中有效。在正常的执行过程中，调用recover会返回nil，并且没有其他任何
效果。如果当前的goroutine陷入恐慌，调用recover可以捕获到panic的输入值，并且恢复正常的执行。


标准库
-----------


第三方库
-----------


Web框架
-----------

- `Revel <http://robfig.github.io/revel/>`_
- `beego <http://beego.me/>`_

推荐阅读
-----------

- `一步一步学习Revel Web开源框架 <http://www.cnblogs.com/ztiandan/archive/2013/01/17/2864498.html>`_
- `build-web-application-with-golang <https://github.com/astaxie/build-web-application-with-golang>`_
- `gopkg <https://github.com/astaxie/gopkg>`_
- `Network programming with Go <http://jan.newmarch.name/go/>`_
