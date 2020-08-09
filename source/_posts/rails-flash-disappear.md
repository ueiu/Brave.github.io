---
title: Rails 错误提示 Flash 使用
description: Rails 错误提示 Flash 使用总结。
typora-root-url: ..
typora-copy-images-to: ../images
date: 2020-08-09 16:39:28
categories:
tags:
- Ruby On Rails
---

在 Rails 的管理后台中，经常要返回一些错误提示并显示在 `redirect_to` 或者 `render` 的页面中，但当我们点击新的链接，就想要这个错误提示消失掉，不停留在新的页面。

### 解决方案
#### redirect_to

```ruby
controller.rb
# 第一种
redirect_to admin_shops_path, notice: '创建成功'
# 第二种
flash[:noite] = '创建成功'
redirect_to admin_shops_path
# 第三种
flash.discard[:noite] = '创建成功'
redirect_to admin_shops_path
```

#### render

```ruby
controller.rb
flash.now[:noite] = '创建成功'
render :new
```

#### js

其实这种的话，可以搭配上面的一起使用，设置一个定时器自动消失。

```javascript
// 清除 flash alert 消息
$(document).ready(function(){
  setTimeout(function(){
    $('.alert').fadeOut();
  }, 5000);
});
```

------

参考资料

- [Rails中使用flash总结](http://rubyer.me/blog/407/)
- [关于Rails的错误提示 Rails flash error不消失](https://www.iteye.com/blog/hlee-447437)
- [jquery - 让Flash消息消失在Rails的同一页面中](https://www.coder.work/article/6022369)
- [Why flash message won't disappear?](https://stackoverrun.com/cn/q/1100738)