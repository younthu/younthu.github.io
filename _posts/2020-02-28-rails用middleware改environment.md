---
layout: post
title: rails用middleware改environment
category: '技术'
tags:
- rails
- middleware
excerpt: '通过middleware来改变env参数'
---
App里用到`warden-jwt_auth`, 这个gem在用户登录的时候需要取 env['HTTP_JWT_AUD'], 要不然会失败。

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
