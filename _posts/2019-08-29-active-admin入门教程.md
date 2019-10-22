---
layout: post
title: active_admin入门教程
---
# 速查
## 表单

<!-- Begin of tips -->
<div class="card">
  <div class="card-header">
    new form通过参数传递指定表单的值
  </div>
  <div class="card-body">
    <h5 class="card-title">通过parameters的形式给新建表单字段预设值</h5>
    <p class="card-text">
    ~~~ruby
    # 注意参数必须嵌套在namespace package_user里面
    new_admin_package_user_path(package_user: {user_id:user,account_manager_id: current_admin_user}))  
    ~~~
    </p>
    
  </div>
  <div class="card-footer text-muted">
    2 days ago
  </div>
</div>

<!-- Begin of tips -->

<div class="card" id="input_as_select">
  <div class="card-header">
    表单字段的值通过下拉列表输入 
  </div>
  <div class="card-body">
    <h5 class="card-title">和html一样, 只允许选取指定的值</h5>
    <p class="card-text">
    ~~~ruby
    f.input :category, as: :select, collection: Category.all.collect {|product| [product.name, product.id] }
    ~~~
    </p>
    <a href="https://stackoverflow.com/a/23807371" class="btn btn-primary">stackoverflow 参考</a>
  </div>
  <div class="card-footer text-muted">
    2 days ago
  </div>
</div>

<!-- Begin of tips -->



## 自定义按钮


<!-- Begin of tips -->
<div class="card" id="input_as_select">
  <div class="card-header">
    列表右上角按钮 
  </div>
  <div class="card-body">
    <h5 class="card-title">创建一个和新建按钮一样的按钮, 注意，这其实就是一个链接</h5>
    <p class="card-text">
        ~~~ruby
        action_item :create_times_package, only: :index do
            link_to "新建次数套餐", new_admin_package_path(times_package: true), method: :get
        end
        ~~~
    </p>
    
  </div>
  <div class="card-footer text-muted">
    
  </div>
</div>
<div>

</div>

#参考

1. [Arbre](https://github.com/gregbell/arbre)
2. [Customize ActiveAmdin Index filters](https://hashrocket.com/blog/posts/customize-activeadmin-index-filters)