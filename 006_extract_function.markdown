[好像很久没写了....先来一篇短的试着恢复节奏什么的 - -]

"怎么可以没有 fatal?" 新来的同事指出 Logger 缺少的方法。

"缺什么方法 monkey patch 下不就好了，毕竟我们用的是 ruby 嘛。"你开始学着 tech lead 的语气说话。

"No way." 新同事不吃这一套，"应该有一种途径使得 Logger 类可扩展，不然出了问题难免会 point finger，来给我看下代码先。"

你已经开始讨厌这人说话不夹 English 会 die 的属性了。

"啊哈这不是已经很接近充分可扩展的程度了嘛，来把这部份底层的东西单独抽取出来："

    class Logger
    　def self.define_log_level(level)
    　　prefix = level.to_s.upper
    　　define_method level do |msg|
    　　　@stream.puts "[#{prefix}] #{msg}"
    　　end
    　end
    　
    　%w(error info log debug).each do |level|
    　　define_log_level level
    　end
    end

    # Out of the scope
    Logger.define_log_level 'fatal'

"这样以后也可以直接使用 Logger.defind_log_level 自行扩充 Logger 类了嘛"

### 总结

把原本仅内部使用的功能提取成函数, 作为低级 API 暴露给使用者, 可以很大程度上提高库/框架的可定制性. 而且保证了用户自行扩展的 fatal 方法与预先定义的 error/info 等方法行为可以确保一致, 不至于因为库版本升级, error/info 等方法逻辑改变但 fatal 没能及时跟进.
