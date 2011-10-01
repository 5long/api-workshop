Okay 在参数化一文中我们给自己写了个可扩展的logger, 使用起来像这样:

　my_own_log_file = File.new('/tmp/debugging_shit', 'w')
　log_the_shit('there it goes', my_own_log_file)
　log_the_shit('shit happens', my_own_log_file)
　log_the_shit('zOMG not again', my_own_log_file)

虽然我们解决了调整输出流的问题，但是目前至少又增加了两个新问题：

* 参数 my_own_log_file 需要反复传入，而这部分并不是我们很关心的东西。相比之下 log 信息才是重要的部分，第二个参数纯属视觉噪音。

* 参数顺序需要记忆。你能不翻文档/源码一下子想起来日志信息和输出流哪个在前哪个在后么？就算现在能记住，周末 Party 喝多了之后还能么？十一放假回来还能么？

接下来我们的设计决策就着手解决这两个问题。

### 状态 State

我们希望找到这样一种机制，可以帮我们“记住”要向 log_the_shit 函数传入的第二个参数 -- 一方面记住参数本身(亦即输出流)来减少使用中的重复，还要记住调用时放在*第二个位置*。这种“从某一时刻起，世界开始变得不一样”的抽象机制就是状态[1]。

那么我们用什么来储存状态？好吧标题里明显已经剧透了。我们这里就用 Ruby 对象的实例属性来储存这个状态。实现见下面：

　class Logger
　　def initialize(output_stream=STDERR)
　　　@stream = output_stream
　　end
　　
　　def log(message)
　　　log_the_shit(message, @stream)
　　end
　end
　
　# Usage
　my_logger = Logger.new File.new('/tmp/debugging_shit', 'w')
　my_logger.log "This could work, huh?"
　my_logger.log "Really"

为了方便那个王八蛋同事迁移到新的 Logger 设计上来，我们还保留了同样的默认参数=STDERR设计。当然事实上他是不会迁移的，新代码对他来说比以前要多打很多字而这次他拒绝动用sed。算了。

### 总结：属性与状态

来跟我一起反复念下面这段：

　从某一时刻起，世界要开始变得不一样
　从my_logger被创建的时刻起，它用@stream属性来记住以后调用 log_the_shit 时所需的第二个参数是自己初始化时传入的那个东西

简单吧。

[1]: 当然这个描述很不严谨，更谈不上是定义。而且本文中的状态从来就没有改变过 -- Logger对象始终向同一个输出流写入字符。一个不错的入门解释是 SICP 的课堂视频：[SICP 5A](http://v.youku.com/v_show/id_XMTM0OTI5OTQ0.html)