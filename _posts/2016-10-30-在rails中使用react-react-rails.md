---
layout: post
title: 在Rails中使用React - react_rails
tags:
- ReactJS
- Rails

category:
- 技术
- Ruby on Rails

excerpt: ReactJS是非常棒的前端框架，在rails中使用ReactJS非常方便，4步快速上手reactJS. 1. 添加Gem包，2.安装react_rails, 3. 配置react_rails, 4. 添加reactjs页面并渲染。
---

[react_rails](https://github.com/reactjs/react-rails)是facebook推荐的一个react gem, 安装使用都非常方便。

步骤比较简单:

1. 在Gemfile中加入下面的语句

```ruby
gem 'react-rails'
```

1. bundle install

```
bundle install
```

1. 运行react_rails安装脚本

```
rails g react:install
```

1. 配置react

```ruby
# config/environments/development.rb
MyApp::Application.configure do
  config.react.variant = :development
end

# config/environments/production.rb
MyApp::Application.configure do
  config.react.variant = :production
end
```

1. 在app/assets/javascripts/components/下面创建自己的components

```javascripts
class MyComponent extends React.Component {
    render(){
        return (<div>Hello React</div>);
    }
}
```

1. 在Rails view中添加下面的代码，渲染组件

```
<%= react_component 'MyComponent', { data: @records } %>
```
