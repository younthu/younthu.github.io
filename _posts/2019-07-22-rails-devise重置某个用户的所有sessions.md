---
layout: post
title: Rails devise重置某个用户的所有sessions
excerpt: Rails devise 如果需要重置某个人的session, 可以在console里面修改该用户密码来重置所有session, 因为devise用密码做salt.
---
Rails devise 如果需要重置某个人的session, 可以在console里面修改该用户密码来重置所有session, 因为devise用密码做salt.

~~~ruby
u = User.find xxxx
u.password = "new_password"
u.save
~~~
