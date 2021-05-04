---
title: hexo
date: 2020-05-05 20:00:48
categories: 
- Nodejs
tags:
- hexo
- CentOS
- Nodejs
---

记录在CentOS下搭建Hexo静态博客与使用next主题以及其配置的步骤
<!-- more -->

中文网 [https://hexo.io/zh-cn/][1]
## 依赖
- node.js   `安装参照nodejs`
- nginx  ` 安装参照hexo`
- git   `yum install git`

## 安装hexo
``` stata
npm install hexo-cli -g
npm uninstall hexo-cli -g
```

## 初始化 hexo博客
初始化报错，是因为未安装git
1. 选定一个位置，初始化hexo
``` stylus
cd /home/
hexo init mblog
cd mblog
npm install
hexo g
```

2. 修改站点配置
	``` avrasm
	# Site
	title: 宁静致远
	subtitle: 一沙一世界,一叶一菩提
	description: 成功在优点的发挥，失败是缺点的累积。
	keywords:
	author: my
	language: zh-CN
	timezone: Asia/Shanghai

	url: http://myinsky.com

	highlight:
	 auto_detect: true
	```

3. 修改nginx配置location ->root，指向生成的public目录`/home/mblog/public`,然后重启nginx
``` fortran
 location / {
            root   /home/mblog/public;
            index  index.html index.htm;
        }
```
4. 在浏览器输入服务器ip，即可访问blog

## 切换next主题
1. 安装next主题
``` vim
git clone https://github.com/theme-next/hexo-theme-next themes/next
```
2. 站点配置文件切换主题 `theme: next`
3. 主题配置Schemes改为 `scheme: Gemini`
4. 添加分类和标签，
- 启用about，tags，categories
``` less
menu:
  home: / || fa fa-home
  about: /about/ || fa fa-user
  tags: /tags/ || fa fa-tags
  categories: /categories/ || fa fa-th
  archives: /archives/ || fa fa-archive
```
- 添加categories `hexo new page "categories"`
``` asciidoc
title: 分类
date: 2020-07-04 15:52:58
type: "categories"
layout: "categories"
```
- 添加tags `hexo new page "tags"`
``` asciidoc
title: 标签
date: 2020-07-04 15:49:26
type: "tags"
layout: "tags"
```
- 添加about `hexo new page "about"`

## 站内搜索
1. 安装依赖库
``` sql
npm install hexo-generator-searchdb --save
```

2. 站点配置添加
``` less
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```

3. 主题配置设置true
``` stata
# Dependencies: https://github.com/theme-next/hexo-generator-searchdb
local_search:
  enable: true
```

## 字数统计
1. 安装依赖库
``` sql
npm install hexo-symbols-count-time --save
```

2. 站点配置文件添加
``` less
symbols_count_time:
  symbols: true
  time: false
  total_symbols: true
  total_time: false
  exculd_codeblock: false
  awl: 4
  wpm: 275
  suffix: "mins."
```
3. 主题配置文件
``` groovy
symbols_count_time:
  separated_meta: true
  item_text_post: true
  item_text_total: true
```

## 添加网络背景

1. 在next主题下安装以来库
``` vim
git clone https://github.com/theme-next/theme-next-canvas-nest themes/next/source/lib/canvas-nest
```

2. 主题配置
``` bash
canvas_nest: # 网络背景
  enable: true
  onmobile: true # display on mobile or not
  color: '0,0,0' # RGB values, use ',' to separate
  opacity: 0.5 # the opacity of line: 0~1
  zIndex: -1 # z-index property of the background
  count: 150 # the number of lines
```

## github_banner
``` less
github_banner:
  enable: true
  permalink: https://github.com/speedsky
  title: Follow me on GitHub
```
## 代码块添加复制按钮
``` less
codeblock:
  highlight_theme: night
  copy_button:
    enable: true
```
## 开启图像显示
``` sql
avatar:
  # Replace the default image and set the url here.
  url: /images/avatar.gif
  # If true, the avatar will be dispalyed in circle.
  rounded: true
```
## 社交
``` less
social:
  GitHub: https://github.com/speedsky || fab fa-github
  E-Mail: mailto:yeal136@163.com || fa fa-envelope
```

## 打赏
``` stylus
reward:
  wechatpay: /images/wechatpay.png
  alipay: /images/alipay.png
  #paypal: /images/paypal.png
  #bitcoin: /images/bitcoin.png
```
## RSS
- `npm install --save hexo-generator-feed`
- 战点添加 `plugins: hexo-generate-feed`
- 启用配置
``` less
follow_me:
  RSS: /atom.xml || fa fa-rss
```

## 去掉版权  
- `powered: false`

## 添加鼠标点击小红心
1. themes/next/source/js/下新建clicklove.js文件，内容如下：
``` javascript
!function(e,t,a){function n(){c(".heart{width: 10px;height: 10px;position: fixed;background: #f00;transform: rotate(45deg);-webkit-transform: rotate(45deg);-moz-transform: rotate(45deg);}.heart:after,.heart:before{content: '';width: inherit;height: inherit;background: inherit;border-radius: 50%;-webkit-border-radius: 50%;-moz-border-radius: 50%;position: fixed;}.heart:after{top: -5px;}.heart:before{left: -5px;}"),o(),r()}function r(){for(var e=0;e<d.length;e++)d[e].alpha<=0?(t.body.removeChild(d[e].el),d.splice(e,1)):(d[e].y--,d[e].scale+=.004,d[e].alpha-=.013,d[e].el.style.cssText="left:"+d[e].x+"px;top:"+d[e].y+"px;opacity:"+d[e].alpha+";transform:scale("+d[e].scale+","+d[e].scale+") rotate(45deg);background:"+d[e].color+";z-index:99999");requestAnimationFrame(r)}function o(){var t="function"==typeof e.onclick&&e.onclick;e.onclick=function(e){t&&t(),i(e)}}function i(e){var a=t.createElement("div");a.className="heart",d.push({el:a,x:e.clientX-5,y:e.clientY-5,scale:1,alpha:1,color:s()}),t.body.appendChild(a)}function c(e){var a=t.createElement("style");a.type="text/css";try{a.appendChild(t.createTextNode(e))}catch(t){a.styleSheet.cssText=e}t.getElementsByTagName("head")[0].appendChild(a)}function s(){return"rgb("+~~(255*Math.random())+","+~~(255*Math.random())+","+~~(255*Math.random())+")"}var d=[];e.requestAnimationFrame=function(){return e.requestAnimationFrame||e.webkitRequestAnimationFrame||e.mozRequestAnimationFrame||e.oRequestAnimationFrame||e.msRequestAnimationFrame||function(e){setTimeout(e,1e3/60)}}(),n()}(window,document);
```
2. 打开themes/next/layout/_layout.swig,在上面写下如下代码：
``` stylus
{% if theme.fireworks %}
   <canvas class="fireworks" style="position: fixed;left: 0;top: 0;z-index: 1; pointer-events: none;" ></canvas> 
   <script type="text/javascript" src="//cdn.bootcss.com/animejs/2.2.0/anime.min.js"></script> 
   <script type="text/javascript" src="/js/src/fireworks.js"></script>
{% endif %}
```

## 新增自定义菜单
1. 在主题配置文件中添加菜单项
``` stylus
  tools: /tools/ || fa fa-gavel
  books: /books/ || fa fa-book
```
2. 添加tools `hexo new page "tools"`
``` asciidoc
title: 工具
date: 2020-07-04 15:49:26
type: "tools"
layout: "tools"
```

3.  添加books `hexo new page "books"`
``` asciidoc
title: 阅读
date: 2020-07-04 15:49:26
type: "books"
layout: "books"
```
4. 在主题的language目录下对应的语言文件的菜单项添加tools与books对应的中文显示

## next主题样式设置
1. 在`next/source/css/_common/componments/pages/categories.styl`添加`display: inline-block;`
``` js
  .category-list {
    display: inline-block;
    margin: 0;
    padding: 0;
  }

  .category-list-item {
    display: inline-block;
    margin: 5px 10px;
  }
```
2. 在`next/source/css/_common/componments/pages/tag-cloud.styl`添加`display: inline-block;`
``` js
 .tag-cloud {
  text-align: center;

  a {
    display: inline-block;
    margin: 10px;

    &:hover {
      color: var(--link-hover-color) !important;
    }
  }
}
```


  [1]: https://hexo.io/zh-cn/


