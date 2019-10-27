---
layout: post
title: ActiveRecord中的多态与继承
categories: Rails
excerpt: "ActiveRecord中应该怎样实现多态与继承, 接口怎样定义？用scope和单表继承. 该方法只需要一个model，可以无限扩展子类(一级).思想是通过在子类中指定default_scope参数来限制子类模型query出来的范围.有另外一种比较简单的实现继承的方法: 单表继承
该方法就是给模型加一个`type`字段，注意`type`在AR中是有特殊用途的，用来实现单表继承."

---

# 继承用 scope
用scope和单表继承. 该方法只需要一个model，可以无限扩展子类(一级).

思想是通过在子类中指定`default_scope`参数来限制子类模型`query`出来的范围.

该方法简单易懂

~~~
class Child < Parent
  default_scope {where(type:true)}
end
~~~

# 单表继承
有另外一种比较简单的实现继承的方法: 单表继承
该方法就是给模型加一个`type`字段，注意`type`在AR中是有特殊用途的，用来实现单表继承。

然后在生成子模型的时候加上`--parent=ParentModel`参数

~~~
rails generate model vehicle type:string color:string price:decimal{10.2} # type字段是重点
rails generate model car --parent=Vehicle
~~~

**子模型路径的问题**

注意，按上面的方法生产子模型以后，子模型可以正常query属于它的类型，但是在路径寻找的时候会出现子模型的路径无法匹配的情况。模型的路径与`ApplicationRecord.model_name`相关联。

如果你想要子模型的路径也指向基类的路径，可以在基类的中加入下面的代码:
~~~
class Vehicle << ApplicationRecord
  def self.inherited(child)
    child.instance_eval do
      def model_name
        Vehicle.model_name
      end
    end
    super
  end
end
~~~

加了上面的代码，所有vehicle和car的寻找路径都会指向vehicle.
否则，你得手动为car生成一整套的view 和view controller.

# 多态关联
在多态关联中，在同一个关联中，一个模型可以属于多个模型。

注意：
~~~
多态关联与传统软件工程UML里面的多态没有任何关系！别钻牛角尖！`多态关联`四个字重点是关联。普通的关联只能实现一个模型对一个模型，如果你想让多个模型可以引用同一个模型，就得用多态关联。

说白了，rails的设计限制了一遍情况下只能实现一个模型对一个模型的关联，框架本身的缺陷。多态关联就是为了解决框架本身的缺陷而造出来的一个名词。
~~~

多态关联通过在`Model`中插入`belongs_to :xxxx, polymorphic: true`来把模型定义为符合接口`xxxx`规范, 也就是我可以被`xxxx`引用。

然后，在引用的地方通过`has_many :model, as: :xxxx`来申明我可以作为`xxxx`来引用`model`

以评论功能为例子, `Post`和`Image`都可以评论.

~~~ruby
class Comment < ActiveRecord::Base
  belongs_to :commentable, polymorphic: true
end

class Post < ActiveRecord::Base
  has_many :comments, as: :commentable
end

class Image < ActiveRecord::Base
  has_many :comments, as: :commentable
end
~~~

我们需要一张 comments 表能关联 posts 表或 images 表, 注意 `commentable_id`和`commentable_type`, 这可以用`t.belongs_to`替换

~~~ruby
class CreateComments < ActiveRecord::Migration
  def change
    create_table :products do |t|
      t.integer :user_id
      t.text :content

      # 下面等同于 t.belongs_to :commentable, polymorphic: true
      t.integer :commentable_id
      t.string  :commentable_type

      t.timestamps null: false
    end

    add_index :comments, :commentable_id
  end
end
~~~
# 多态关联替换Single Table Inheritance
把通用的部分用一个表来表示，多态的部分通过delegate给多态引用。注意，这也不是我们传统面向对象上的多态，这个只实现了部分数据和方法的多态，类型是没有继承关系的。

~~~ruby
class User < ActiveRecord::Base
  has_many :tasks, dependent: :destroy
  belongs_to :profile, polymorphic: true

  delegate :guest?, :can_share_task?

end


class GuestProfile < ActiveRecord::Base
  has_one :user, as: :profile, dependent: :destroy

  def guest?
    true
  end

  def can_share_taks?(task)
    false
  end
end


class MemberProfile < ActiveRecord::Base
  has_one :user, as: :profile, dependent: :destroy

  def guest?
    false
  end

  def can_share_taks?(task)
    task.user_id == user.id
  end
end
~~~

# 参考
1. [多态表关联详解](http://tailang.github.io/2013/10/22/rails%E4%B8%AD%E7%9A%84%E5%A4%9A%E6%80%81%E8%A1%A8%E5%85%B3%E8%81%94/)
2. [ActiveRecord中文文档，多态关联](https://ruby-china.github.io/rails-guides/association_basics.html#polymorphic-associations)
3. [ActiveRecord中文文档，自关联](https://ruby-china.github.io/rails-guides/association_basics.html#self-joins)
1. [单表继承](https://ruby-china.github.io/rails-guides/association_basics.html#single-table-inheritance)， 可以通过`rails g model Child --parent=Parent`来实现单表继承
1. [Rails多态的两种使用场景](https://mednoter.com/polymorphic.html)
2. [Railscast: STI and Polymorphic Associations](http://railscasts.com/episodes/394-sti-and-polymorphic-associations)
3. [Rails的多态关联](https://www.jianshu.com/p/fa7bfb81cf6a)