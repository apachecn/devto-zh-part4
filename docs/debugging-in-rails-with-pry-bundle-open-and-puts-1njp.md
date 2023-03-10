# 在 Rails 中使用 Pry、' bundle open '和' puts '进行调试

> 原文：<https://dev.to/cassidycodes/debugging-in-rails-with-pry-bundle-open-and-puts-1njp>

本周我开始钻研一些我非常喜欢的调试。嗯，如果你在中途问我，我可能不会开心，但我找到了解决办法！我使用了一些工具来进行调试，它们都给了我更多的信息来解决我以前没有的问题。

以下是关于每个工具的一点信息，以及我觉得有用的地方。如果您有其他 Ruby 或 Rails 调试技巧，请告诉我！

## 撬！

[撬](https://pryrepl.org/)对 Ruby 来说是一个神奇的工具。它给你各种各样的工具，像`? my_method`向你展示文档，或者`ls MyObject`向你展示你可以对一个对象调用的所有方法。你甚至可以使用`cd`来移动一个物体！

这里有一个很小的类，里面有一个 pry 绑定，向你展示一些方法是如何工作的。

```
class User
  def initialize(name)
    @name = name
  end

  def name
    binding.pry
    @name
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我从命令行启动并初始化了一个新用户。当我调用`name`方法时，Pry 打印出绑定周围的行，并给出一个交互式提示。

```
[1] pry(main)> me = User.new('cassidy')
=> #<User:0x00007fd807ed87a8 @name="cassidy">
[2] pry(main)> me.name

From: (pry) @ line 9 User#name:

     7: def name
     8: binding.pry
 => 9: @name
    10: end
[1] pry(#<User>)> 
```

Enter fullscreen mode Exit fullscreen mode

此时，我可以输入`@name`来查看我的实例变量的值。

```
[1] pry(#<User>)> @name
=> "cassidy" 
```

Enter fullscreen mode Exit fullscreen mode

太好了，这正是我所期待的。让我们使用`step`将执行向前移动一帧。

```
[2] pry(#<User>)> step
From: /Users/cassidy/.rbenv/versions/2.3.8/lib/ruby/gems/2.3.0/gems/pry-0.12.2/lib/pry/pry_instance.rb @ line 388 Pry#evaluate_ruby:

    383: def evaluate_ruby(code)
    384: inject_sticky_locals!
    385: exec_hook :before_eval, code, self
    386:
    387: result = current_binding.eval(code, Pry.eval_path, Pry.current_line)
 => 388: set_last_result(result, code)
    389: ensure
    390: update_input_history(code)
    391: exec_hook :after_eval, result, self
    392: end

[1] pry(#<Pry>)> 
```

Enter fullscreen mode Exit fullscreen mode

整洁！看来下一帧是撬自己了！想知道撬是怎么工作的吗？查看这里的所有方法！

```
[1] pry(#<Pry>)> ls
Pry#methods:
  add_sticky_local commands= eval extra_sticky_locals= last_dir memory_size print quiet? should_print?
  backtrace complete eval_string hooks last_dir= memory_size= print= raise_up show_result
  backtrace= config eval_string= hooks= last_exception output process_command raise_up! sticky_locals
  binding_stack current_binding evaluate_ruby inject_local last_exception= output= process_command_safely raise_up_common suppress_output
  binding_stack= current_context exception_handler inject_sticky_locals! last_file output_array prompt repl suppress_output=
  color custom_completions exception_handler= input last_file= output_ring prompt= reset_eval_string update_input_history
  color= custom_completions= exec_hook input= last_result pager push_binding run_command
  command_state editor exit_value input_array last_result= pager= push_initial_binding select_prompt
  commands editor= extra_sticky_locals input_ring last_result_is_exception? pop_prompt push_prompt set_last_result
instance variables:
  @backtrace @command_state @custom_completions @indent @last_exception @last_result_is_exception @prompt_stack @suppress_output
  @binding_stack @config @eval_string @input_ring @last_result @output_ring @stopped
locals: _ __ _dir_ _ex_ _file_ _in_ _out_ _pry_ code result 
```

Enter fullscreen mode Exit fullscreen mode

我不打算在这里深究太多，但是让我们看看`next`做了什么。

```
From: /Users/cassidy/.rbenv/versions/2.3.8/lib/ruby/gems/2.3.0/gems/pry-0.12.2/lib/pry/pry_instance.rb @ line 390 Pry#evaluate_ruby:

    383: def evaluate_ruby(code)
    384: inject_sticky_locals!
    385: exec_hook :before_eval, code, self
    386:
    387: result = current_binding.eval(code, Pry.eval_path, Pry.current_line)
    388: set_last_result(result, code)
    389: ensure
 => 390: update_input_history(code)
    391: exec_hook :after_eval, result, self
    392: end 
```

Enter fullscreen mode Exit fullscreen mode

它在当前帧中向前移动了一行。所以在幕后，它执行了第 38 行的所有内容，并将我们返回到同一个堆栈帧，因为这里有更多的代码要执行。整洁！

好了，别再瞎扯了。让我们输入`continue`，我们将最终看到`name`方法的结果。

```
[1] pry(#<Pry>)> continue
=> "cassidy" 
```

Enter fullscreen mode Exit fullscreen mode

## 捆开

当你开始在 pry 中遍历一个调用堆栈，发现自己在属于你正在使用的 Ruby Gem 的堆栈帧中徘徊时，会发生什么呢？当这种情况发生在我身上时，我喜欢用`bundle open`去检查 gem 中的代码实际上是什么样子。

这是我今天处理的错误。您将看到错误是在第三方库中引发的。我可能犯了一个导致它的错误，但是我的错误在调用堆栈中是如此之低，以至于我没有看到它！

```
2019-08-01T21:42:43.628Z 34126 TID-ower3a8uc WARN: NoMethodError: undefined method `body' for nil:NilClass
2019-08-01T21:42:43.628Z 34126 TID-ower3a8uc WARN: /Users/cassidy/.rbenv/versions/2.3.8/lib/ruby/gems/2.3.0/gems/carrierwave-0.10.0/lib/carrierwave/storage/fog.rb:228:in `read'
/Users/cassidy/.rbenv/versions/2.3.8/lib/ruby/gems/2.3.0/gems/carrierwave-0.10.0/lib/carrierwave/uploader/cache.rb:77:in `sanitized_file'
/Users/cassidy/.rbenv/versions/2.3.8/lib/ruby/gems/2.3.0/gems/carrierwave-0.10.0/lib/carrierwave/uploader/cache.rb:116:in `cache!'
/Users/cassidy/.rbenv/versions/2.3.8/lib/ruby/gems/2.3.0/gems/carrierwave-0.10.0/lib/carrierwave/uploader/versions.rb:226:in `recreate_versions!'
/Users/cassidy/.rbenv/versions/2.3.8/lib/ruby/gems/2.3.0/gems/carrierwave_backgrounder-0.4.2/lib/backgrounder/workers/process_asset.rb:12:in `perform'` 
```

Enter fullscreen mode Exit fullscreen mode

我不知道这里有什么是不应该的，所以我运行`bundle open carrierwave`在文本编辑器中打开 gem 的代码库。一旦我到了那里，我就找到了出现错误的那一行，并在其中添加了一个`binding.pry`,这样我就可以更好地了解发生了什么。

## [T2`puts`！！！](#-raw-puts-endraw-)

有时候窥探对你没用。如果您使用多线程 web 服务器或 Sidekiq 来处理后台作业，这一点尤其正确。当这种情况发生时，您将得到几秒钟的交互式提示，然后您将被引导，因为另一个进程正试图向同一个主进程写入日志。啊！这可能会令人沮丧。

我在这里最喜欢做的事情是打印我自己的日志。我试着让这些突出来，这样我就不会在原木经过的时候错过它们。这里有一些伪代码，类似于我今天解决的问题。

```
def filename
   puts '---------------------'
   puts 'in filename'
   puts model.attributes['image']
   puts model.persisted?
   puts model.image_changed?
   puts '---------------------'
   return model.attributes['image'] if model.persisted? && !model.image_changed?

   new_filename = "#{SecureRandom.hex}.#{file&.extension}"
   puts new_filename
   new_filename
end 
```

Enter fullscreen mode Exit fullscreen mode

这给了我一些清晰的输出，所以我可以看到每次调用这个方法时发生了什么。关于`puts`调试的伟大之处在于，当一个方法被多次调用时，你永远不会怀疑自己是否忘记了正在进行哪一次迭代。

## 结论

当我今天解决问题时，我同时使用了这三种工具。窥探是我的专长，但当这还不够时，我会去做别的事情。我最终学到了很多关于载波生命周期的知识，这将使我剩下的任务变得容易得多！我甚至知道了一些 CarrierWave 没有记录的方法。