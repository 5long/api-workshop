技术主观是个成天上 [lambda-the-ultimate](http://lambda-the-ultimate.org/) 的人。Logger类经他 review 之后立即遭到鄙视：

"这么写明显是 overkill 嘛，既然 Logger 类只有一个方法直接用闭包储存状态不就完了？"

于是他打开 emacs 写了如下实现：

　def make_logger(output_stream):
　　proc do |message|
　　　output_stream.puts message
　　end
　end

　 # Usage
　 my_logger = make_logger(STDERR)
　 my_logger.call "Nah"

"有没听说过代码质量与行数成负相关？你看这个写法就简单多了吧啊哈哈。另外你看 Ruby 丑陋的闭包调用方式：非要写.call()，这也太不 Functional 了！"

你除了点头称是没有别的可以回答。还好这丫也知道旧代码一旦写下去，只要不出问题就没人乐意去改动了。而且他也知道你不会接受他的设计 - 就像那个滥用 log 的同事不接受你的设计一样。

### 总结

"对象是穷人的闭包"，单纯为了 [currying](http://en.wikipedia.org/wiki/Currying) 而新建一个类可能是有那么点过分。而这句话反过来也成立 - 闭包是只有一个 call 方法的对象。

哪种实现方案更好，很难说，但遵循所使用的语言的社区惯例应该不会错。就 logger 的例子而言，考虑到日后扩展性的需求 - 如加入多个 log level - 用类和对象可能更适合。

本店后续的文章依然以 Ruby 为主要目标语言，所以请期待更多面向对象风格的内容而不是 cool kids 们津津乐道的函数式。