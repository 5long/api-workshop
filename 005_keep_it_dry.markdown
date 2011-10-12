"Nah, 既然已经实现成了类，干脆就扩展成市面上常见的 logger 接口算了，把多个 log level 都加上去。" Tech lead 如是说。尽管你觉得他这是在做无用功，不过能暂时无视产品需求去写一点 plumbing 还是很惬意的。于是半杯咖啡之后[注1]：

　class Logger
　　def error(msg)
　　　@stream.puts "[ERROR] #{msg}"
　　end

　　def info(msg)
　　　@stream.puts "[INFO] #{msg}"
　　end

　　def debug(msg)
　　　@stream.puts "[DEBUG] #{msg}"
　　end
　end

"No no no"，tech lead 又发话了，"这代码一看就是复制粘贴写出来的，难道你没注意那种重复出现的 pattern？我们用的是 ruby 不是 python 啊同学！假如有朝一日我们把那一对方括号换成别的字符怎么办？求助那个成天聊 sed 脚本的家伙吗？"

虽然你觉得换掉方括号的可能性微乎其微，但避免与之前那个衰人交流这件事真是说到你心上去了，于是你请他给你写个简单实例：

　class Logger
　　%w(error info log debug).each do |level|
　　　define_method level do |msg|
　　　　@stream.puts "[#{level.upper}] #{msg}"
　　　end
　　end
　end

"你看这才叫 DRY 嘛。"

### 总结

* 同一份信息在代码的不同位置出现，修改的时候就要一并修改。今天的勤奋复制粘贴往往会带来明天的维护成本，Don't Repeat Yourself 是值得坚持的。

* Ruby 的 class scope 中我们可以写任意的语句，同时 Ruby 又具有 define_method 这样的元编程能力，最终我们才得以如此简化 Logger 类的定义。

* 所使用的编程语言如果不能鼓励 DRY 那么就默默地鄙视它吧。别忘了懒惰是程序员的美德。

* 旁观者清，多一个人来审视代码可能会带来质的改善。所以 code review 的收效不可低估。Pair programming 更是把 code review 提前到了代码刚从键盘敲出来的时间而不是等到提交之后，更有效地避免“已经写完了就懒得修改”的情况出现。

[1]: 省略了前文“属性与状态”中的 initialize 和 log 方法的定义。但我们这里的实现已经放弃了 log_the_shit　的使用而直接调用 @stream.puts。

P.S. 虽然尽力避免提及流行的概念但写到这里已经有"重构示例"的感觉了。但拿不同质量的代码放在一起对比才更容易看出问题所在，不是么。
