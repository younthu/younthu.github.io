---
layout: post
title: rails用middleware改environment
category: '技术'
tags:
- rails
- middleware
excerpt: '通过middleware来改变env参数'
---
App里用到`warden-jwt_auth`, 这个gem在用户登录的时候需要取 env['HTTP_JWT_AUD'],用来生成jwt whitelist, 要不然会失败。

用middleware硬塞了一个变量进去，暂时解决这个问题。


middleware添加方法如下:

~~~ruby
#config/application.rb
require_relative 'boot'

require 'rails/all'

# Require the gems listed in Gemfile, including any gems
# you've limited to :test, :development, or :production.
Bundler.require(*Rails.groups)

module Myapp
  class ConditionalTracker
    def initialize(app)
      @app = app
    end

    def call(env)

      env['HTTP_JWT_AUD'] = "my_jwt_aud" # 添加变量
      @app.call(env)
    end
  end

  class Application < Rails::Application
    # Initialize configuration defaults for originally generated Rails version.
    config.load_defaults 6.0


    config.middleware.insert_before 1, Myapp::ConditionalTracker #  注册middleware

  end
end

~~~

可以通过`rails middleware`查看注册的middleware, 顶部的index为0， 刚注册的ConditionalTracker在列表的顶部.


# 解法二
可以在config/application.rb里给Application加一个`call`实例方法，改方法会overwrite `Engine.call`.

~~~ruby
#config/application.rb
require_relative 'boot'

require 'rails/all'

# Require the gems listed in Gemfile, including any gems
# you've limited to :test, :development, or :production.
Bundler.require(*Rails.groups)

module Myapp
  
  class Application < Rails::Application
    # Initialize configuration defaults for originally generated Rails version.
    config.load_defaults 6.0

    # Implements call according to the Rack API. It simply
    # dispatches the request to the underlying middleware stack.
    def call(env)
      env['HTTP_JWT_AUD'] = "my_jwt_aud"  # 添加变量
      super(env) # Engine.call
    end
  end
end

~~~

解法二比较简单,不加而外的middleware层。

Application在所有middleware的后面，是一个rack app, 响应`call`。

Middleware的创建是在`rais/engine.app`方法里面。

Routes是在`rails/engine.routes`方法里面创建的。

# 参考
1. [Undestanding rack apps and middleware](https://blog.engineyard.com/understanding-rack-apps-and-middleware), 有对ActionDispatch, ActionPack的详解, 这是Rails的核心, 非常棒的一篇文章，值得一看。
2. [Diving in Rails - The request handling](http://blog.siami.fr/diving-in-rails-the-request-handling)
3. [Rails 对请求的处理过程](https://ruby-china.org/topics/23588)
4. [内置middleware解释](https://guides.rubyonrails.org/rails_on_rack.html#internal-middleware-stack)
5. [ActionDispatch middleware components](https://github.com/rails/rails/tree/master/actionpack/lib/action_dispatch/middleware)
6. [Writing middleware](https://ieftimov.com/post/writing-rack-middleware/)
7. [Rails on rack](https://guides.rubyonrails.org/v3.2/rails_on_rack.html), 可以通过`ActionController::Dispatcher.middleware=.`来替换整个middleware stack.
8. [A Deep Dive into Routing and Controller Dispatch in Rails](https://medium.com/rubyinside/a-deep-dive-into-routing-and-controller-dispatch-in-rails-8bf58c2cf3b5)