# 2025-01-06, 添加Notebooks环境
1. 创建env环境: `python -m venv .venv`
1. 激活env环境: `source .venv/bin/activate`
1. 安装依赖： `pip install -r requirements.txt`
1. 检查某个依赖的版本: `pip show sklearn`

# 修改

1. 2019.08.21, 添加jekyll-sitemap, jekyll build时自动生成sitemap.xml
   通过 `last_modified_at:` with a date in the Front Matter 可以修改lastmod值。
   好像不工作, sitemap.xml没有在github上生成。
   1. 可以用[jekyll-seo-gem](https://github.com/pmarsceill/jekyll-seo-gem)来检查文件是否包含正确的tag. `jekyll-seo -k 'anaconda' -p _site/2019/08/14/anaconda-jupyter-notebook正确安装和使用opencv3.html`

# Jekyll-Jacman

Jekyll-Jacman 是为 [Jekyll](http://jekyllrb.com) 设计的一款清新且具有响应式的主题，拥有更丰富的特性并支持了很多的国内服务。Jacman 始于 [Jacman](https://github.com/wuchong/jacman) 移植而来。

[主题演示](http://simpleyyt.github.io/jekyll-jacman/) | [Yitao's Blog](http://simpleyyt.github.io)

[如何使用 Jacman 主题](http://simpleyyt.github.io/jekyll-jacman/jekyll/2015/09/20/how-to-use-jacman/)

# Notes

- ** 延迟加载 **
  <strike> _includes/lazyload.html提供延迟加载功能, 来自于https://jekyllcodex.org/without-plugin/lazy-loading/ </strike>
  https://github.com/verlok/lazyload 提供缩略图和延迟加载功能.
##功能
- **菜单 menu**  
 主导航菜单
- **控件 widget**  
 侧边栏的控件。包括：分类、标签、RSS、友情链接、微博秀。
- **图片相关 Image**  
 设置网站图标、网站logo、作者头像、博客顶部大图等。还提供了多种图片样式`img-logo`,`img-topic`,`img-center`等。
- **首页模式 index**  
 主题提供了两种首页展示模式。
- **作者 author**  
 作者信息，主要用于展示网站右下角的社交网络链接。包括：微博、豆瓣、知乎、邮箱、GitHub、StackOverflow、Twitter、Facebook、Linkedin、Google+。
- **目录 toc**  
 在文章中和侧边栏可以显示目录。
- **评论 comments**  
 支持 [多说](http://duoshuo.com/) & [disqus](https://disqus.com/) 评论。
- **分享 jiathis**  
 启用 内建分享工具 或 [加网](http://www.jiathis.com/) 分享系统。
- **网站统计 Analytiscs**  
 支持 [谷歌统计](http://www.google.com/analytics/) & [百度统计](http://tongji.baidu.com/) & [CNZZ站长统计](http://www.cnzz.com/)。
- **Search**  
 支持 [谷歌自定义搜索](https://www.google.com/cse/ ) & [百度站内搜索](http://zn.baidu.com/)  &[微搜索](http://tinysou.com/)。
- **totop**  
 回到顶部。
- **rss**  
 RSS 订阅链接。
- **fancybox**  
 图片查看的 [Fancybox](http://fancyapps.com/fancybox/) 工具。
- **自定义主题颜色**
 在`_config.yaml`中就可以修改主题的颜色，而不用去找那些奇怪的 stylus 文件。
- **其他**
 你可以设置侧边栏在博文页面中不显示。

##协议
[MIT](/LICENSE)
