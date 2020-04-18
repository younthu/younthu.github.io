---
layout: post
title: activerecord [], update, update_attribute, update_column, send
---

~~~ruby
class Board < ApplicationRecord
  belongs_to :user

  belongs_to :p0, class_name: "Dog"
  belongs_to :p1, class_name: "Dog"
  belongs_to :p2, class_name: "Dog"
  belongs_to :p3, class_name: "Dog"
  belongs_to :p4, class_name: "Dog"
  belongs_to :p5, class_name: "Dog"
  belongs_to :p6, class_name: "Dog"
  belongs_to :p7, class_name: "Dog"
  belongs_to :p8, class_name: "Dog"
  belongs_to :p9, class_name: "Dog"
  belongs_to :p10, class_name: "Dog"
  belongs_to :p11, class_name: "Dog"

  def item_at(index) # index = 0
    self["p#{index}"]       # 不工作, 返回nil
    self["p#{index}_id"]    # 工作, 返回p0.id
    self["p#{index}"] = nil # 不工作,  unknown attribute
    self["p#{index}_id"] = 1 # 工作
    self.read_attribute "p#{index}"  # 不工作, 返回nil
    self.read_attribute "p#{index}_id"  # 不工作, 返回id
    self.send "p#{index}"    # 工作, 返回p0
  end
end
~~~