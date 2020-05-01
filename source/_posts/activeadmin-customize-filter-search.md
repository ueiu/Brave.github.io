---
title: ActiveAdmin 自定义 filter 的搜索条件
typora-root-url: ..
typora-copy-images-to: ../images
date: 2019-06-17 10:39:54
tags: 
- Ruby On Rails
- ActiveAdmin
---

### 前言

这几天有一个接到了一个新的需求，需要指定毕业年份搜索相对应的学生。但是因为毕业年份这个字段是存放在经历表的，用ActiveAdmin里filter的*date_range* 过滤器也不是很好看。所以我们来自定义一个filter。

<!-- more -->

### 怎么自定义filter

首先我们在需要有这个搜索条件的model中写一个scope或者是类方法：

```ruby
scope :graduation_on, -> (search) {
  where(id: Experience.select(:student_id).where(end_on: search.to_graduation_day))
}
# 或者是类方法
def self.graduation_on(search)
  where(id: Experience.select(:student_id).where(end_on: search.to_graduation_day))
end
```

我们从页面获取到**search**这个搜索条件，并将它放入我们要执行的代码中。

**to_graduation_day**这个是什么呢？这个是我用于统一生成一个毕业时间的库，生成的时间统一为：传入的年份-06-30。

所以，当传入一个年份时，经过加工，我们的SQL查询语句就是这样的：

```sql
SELECT "students".* FROM "students" WHERE "students"."id" IN (SELECT "resumes"."student_id" FROM "resumes" WHERE "resumes"."end_on" = '2021-06-30')
```
看到这里，你是否兴致冲冲的就跑去ActiveAdmin中调用这个scope或者是类方法呢？很抱歉，这样你会得到这样一个错误，如：

```
undefined method `graduation_on_eq' for Ransack::Search<class: Student, base: Grouping <combinator: and>>:Ransack::Search
```

所以为了能让ActiveAdmin找到这个filter，我们需要在model中进行定义：

点进**ransackable_scopes**方法中可以看到是定义一个白名单，默认情况下是返回一个空数组，所以我们要把我们定义的这个scope或者是类方法放进去覆盖白名单。

```ruby
def self.ransackable_scopes(auth_object = nil)
  [:graduation_on]
end
```

最后就是在ActiveAdmin的中调用这个scope或者是类方法啦：

```ruby
filter :graduation_on, as: :select, label: "毕业年份", collection: -> {
  years = 2000..Time.now.years_since(30).year # 2000年开始到今年往后的30年
  years.map{ |s| [s.to_s + "年", s]}
}
```

### 最后

实现需求有不同的方法以及途径，遇到问题可以多翻翻官方给出的文档或者GitHub上的wiki，说不定会有收获~

------

**<center>over~</center>**