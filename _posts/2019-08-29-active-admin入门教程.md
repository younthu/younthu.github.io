---
layout: post
title: active_admin入门教程
excerpt: 'active_admin是一个简单好用的后台框架,能满足大部分后台需求.这里记录常见的定制方法.'
---
# 速查
## 表单

###    new form通过参数传递指定表单的值
  

~~~ruby
    # 注意参数必须嵌套在namespace package_user里面
    new_admin_package_user_path(package_user: {user_id:user,account_manager_id: current_admin_user}))  
~~~

   


###    表单字段的值通过下拉列表输入 
  
~~~ruby
    f.input :category, as: :select, collection: Category.all.collect {|product| [product.name, product.id] }
~~~


## scope

~~~ruby
        scope :all, default: true

        # assumes the model has a scope called ':active'
        scope :active

        # renames model scope ':leaves' to ':subcategories'
        scope "Subcategories", :leaves

        # Dynamic scope name
        scope ->{ Date.today.strftime '%A' }, :published_today

        # custom scope not defined on the model
        scope("Inactive") { |scope| scope.where(active: false) }

        # conditionally show a custom controller scope
        scope "Published", if: -> { current_admin_user.can? :manage, Posts } do |posts|
            posts.published
        end

~~~

scope 分组

~~~ruby
      # a scope in the default group
        scope :all

        # two scopes used to filter by status
        scope :active, group: :status
        scope :inactive, group: :status

        # two scopes used to filter by date
        scope :today, group: :date
        scope :tomorrow, group: :date

~~~

## 自定义按钮

列表右上角按钮.

~~~ruby
        action_item :create_times_package, only: :index do
            link_to "新建次数套餐", new_admin_package_path(times_package: true), method: :get
        end
~~~

    

## 搜索&过滤

#参考

1. [Arbre](https://github.com/gregbell/arbre)
2. [Customize ActiveAmdin Index filters](https://hashrocket.com/blog/posts/customize-activeadmin-index-filters)