---
title: Kaminari View 中增加总数显示以及修改显示逻辑
description: 使用猴子补丁修改 Kaminari，达到我们想要的效果。
typora-root-url: ..
typora-copy-images-to: ../images
date: 2020-08-10 22:12:24
categories:
- Ruby On Rails
tags:
- Ruby On Rails
- Gems
- Kaminari
---

最近有两个需求都是关于修改分页器的展示效果的，但是 `Kaminari` 这个 gem 内置的方法限定了效果，所以要覆盖内置的方法，我们使用 `Monkey Patch` 来实现，就是所谓的猴子补丁。

### 什么是 Monkey Patch？

> 一种在不改变原始源代码的情况下扩展或修改动态语言的运行时代码的方法。
>
> 使用猴子补丁的目的：
> 1、追加功能
> 2、功能变更
> 3、修正程序错误
> 4、增加钩子，在执行某个方法的同时执行一些其他的处理，如打印日志，实现AOP等
> 5、缓存，在计算量很大，结算之后的结果可以反复使用的情况下，在一次计算完成之后，对方法进行替换可以提高处理速度。

### 实现方案

#### 增加总条数显示

Kaminari 分页器在 View 中所使用的 `paginate` 方法是不返回 total_count 的，当然也有人提交了 [Pull Requests](https://github.com/kaminari/kaminari/pull/711)，但是被 Closed 掉了，所以我们这里把它实现回来。

首先需要在创建 `config/initializers/kaminari_ext.rb` 这么一个文件，然后覆盖 Kaminari 的 paginate 方法。

原方法在 `gems/kaminari-core-1.2.0/lib/kaminari/helpers/helper_methods.rb` 中，感兴趣的可以根据自己使用的版本看看源码。

```ruby
module Kaminari
  module Helpers
    module HelperMethods
      def paginate(scope, paginator_class: Kaminari::Helpers::Paginator, template: nil, **options)
        options[:total_count] ||= scope.total_count # 返回 total_count
        options[:total_pages] ||= scope.total_pages
        options.reverse_merge! current_page: scope.current_page, per_page: scope.limit_value, remote: false

        paginator = paginator_class.new (template || self), **options
        paginator.to_s
      end
    end
  end
end
```

把以上代码添加进去之后，我们还需要修改一下 Kaminari 的 `app/views/kaminari/_paginator.html.erb` 模板文件，让页面可以显示总数。

```html
<%= paginator.render do %>
  <nav>
    <ul class="pagination">
      <span class="col-md-2 text-left" style="font-size:15px;padding-top:5px">
        每页 <%= per_page %> 条
        总共 <%= total_count %> 条
      </span>
    </ul>
  </nav>
<% end %>
```



#### 始终显示分页器

Kaminari 分页器设定是大于一页时才会显示分页器，我们这里改动一下 `render` 方法的逻辑，使页面始终显示分页器。

这里的修改一样是在 `config/initializers/kaminari_ext.rb` 中覆盖 `render` 方法。

原方法在 `gems/2.6.0/gems/kaminari-core-1.2.0/lib/kaminari/helpers/paginator.rb` 中，感兴趣的可以根据自己使用的版本看看源码。

```ruby
module Kaminari
  module Helpers
    def render(&block)
      instance_eval(&block) if @options[:total_pages] >= 1
      @output_buffer.presence
    end
  end
end
```


最后我们重启 Rails 服务，使我们刚刚的修改生效。
![显示效果](https://i.imgur.com/hwXtTI4.jpg)

------

参考资料

- [add total_count to Paginator default view](https://github.com/kaminari/kaminari/pull/711)
- [Always show pagination controls for Kaminari](https://stackoverflow.com/questions/12310343/always-show-pagination-controls-for-kaminari)
- [Display Pagination With Only One Page](https://github.com/kaminari/kaminari/issues/275)