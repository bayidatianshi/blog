## WINDOWS 安装Hugo ##
- [Hugo Releases](https://github.com/gohugoio/hugo/releases)页面下载`hugo_extended_0.56.3_Windows-64bit.zip`
文件。
- 将ZIP文件解压移动到您的`D:\Hugo\bin`文件夹中
- 将执行文件目录`D:\Hugo\bin`添加到电脑系统环境变量`path`中
- cmd输入`hugo version`，提示版本信息则Hugo安装成功。


## 初始化

```markup
hugo new site blog
git init

git remote add origin git@github.com:bayidatianshi/blog.git
```

## Hugo教程 ##

### 文档 ###
- [Hugo官方文档](https://www.gohugo.org/doc)
- [TOML文档](https://github.com/LongTengDao/TOML/wiki)
- [Hugo示例中文教程(倒序)](https://hugo.aiaide.com/post/)

### 创建网站骨架和主题目录 ###
- 安装hugo后，到项目目录，输入以下命令，  
    `hugo new site study-hugo  # 创建网站骨架`  
	`cd study-hugo`  
	`hugo new theme study-theme  # 自建主题目录`
- 主要通过修改以下目录进行开发  
	content : 文章内容  
	themes/study-theme/layouts ：页面模板  
	static : 静态资源  
	config.toml : 网站的配置文件

### 编写文章页面 ###
- 页面 = 文章 + 模板，页面模板下index.html是默认首页，_default/single.html文件是默认文章页
	`hugo new post/page1.md  # 添加一篇文章，自动存放到content下`  
- page1.md修改文章内容

```markup
---
title: "文章标题"
date: 2021-06-30T19:03:31+08:00
draft: true
keywords: "页面的关键字"
weight: "列表页的文章排序, 值越小越靠前, 默认是按时间先后排序的, 也就是date中的值"
description: "内容描述, 主要用于SEO优化.包裹在---中的内容叫做Front Matter，可以提供给模板页获取"
---

文章正文：Hello world!
```

- config.toml设置主题`theme = "study-theme"`
- _default/single.html修改页面模板

```html
<!DOCTYPE html>
<html lang="zh">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>{{.Title}}</title>
</head>

<body>
    <div id="post" class="post">
        <article>
            <header>
                <h1 class="post-title">{{.Title}}</h1><!-- 获取Front Matter中title的值 -->
            </header>
            <div>
                <p>{{.Content}}</p> <!-- 获取文章的内容 -->
                <p>{{.Date}}</p> <!-- 获取Front Matter中date的值 -->
                <p>{{.Description}}</p> <!-- 获取Front Matter中description的值 -->
                <p>{{.Keywords}}</p> <!-- 获取Front Matter中keywords的值 -->
                <p>{{.Permalink}}</p> <!-- 获取页面的链接地址(URL) -->
                <p>下一页：{{.Next}}</p> <!-- 下一个页面 -->
                <p>上一页：{{.Prev}}</p> <!-- 上一个页面 -->
                <p>{{.WordCount}}</p> <!-- 内容的字数 -->
                <p>{{.ReadingTime}}</p> <!-- 阅读内容的预估时间 -->
                <p>{{.Pages}}</p> <!-- 当前列表页面下的内容页面的集合, 该变量在内容页模板的上下文中值为nil -->
                <p>{{.Site}}</p> <!-- 站点变量, 该变量下包含很多站点级别的属性和方法. -->
            </div>
            <hr>
            <div>
                <!-- 页面跳转：通过.NextInSection 和 .PrevInSection 变量实现，默认排序按时间倒序，可设置weight自定义顺序 -->
                {{with .NextInSection}}
                <a href="{{.Permalink}}">前一页</a> 
                {{end}}
                {{with .PrevInSection}}
                <a href="{{.Permalink}}">后一页</a>
                {{end}}
            </div>
        </article>
    </div>
</body>

</html>
```
- 预览网站`hugo server -D`,其中-D代表草稿也可以预览，访问`http://localhost:1313/post/page1/`

### 编写列表页面 ###
- 添加_index.md、page2.md和page3.md到post文件夹，修改pageN.md的title为文章标题N
- 此时_index.md就是post目录下面所有博文的列表页，它对应的默认模板文件在: layouts/_default/list.html
- 修改list.html

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>列表页面</title>
</head>
<body>
    {{  range .Pages }}
        <div>
            <a href="{{.Permalink}}">{{.Title}}</a>
        </div>
    {{  end  }}
</body>
</html>
```
- 访问`http://localhost:1313/post/`可以查看文章列表，文章内的页面跳转也可以用了

### 编写首页 ###
- 添加_index.md、news文件夹到content文件夹，添加_index.md、news1.md、news2.md、news3.md到news文件夹，newsN.md的title为新闻标题N，weight为N
- content/_index.md为首页内容，对应模板在layouts/index.html
- 修改index.html

```html
<!DOCTYPE html>
<html lang="zh">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>网站首页</title>
</head>

<body>
    <!-- .Site.RegularPages 表示网站下面的所有节点内容, 具体到本例, 就是news和post目录下的所有文章 -->
    <!-- where .Site.RegularPages "Section" "news" 的含义就是, 查找出section属性的值为news的项目,对应news文件夹下的文章 -->
    <h2> 新闻列表 </h2>
    {{ range where .Site.RegularPages "Section" "news" }}
    <div>
        <a href="{{.Permalink}}">{{.Title}}</a>
    </div>
    {{ end }}

    <h2> 博文列表 </h2>
    {{ range where .Site.RegularPages "Section" "post" }}
    <div>
        <a href="{{.Permalink}}">{{.Title}}</a>
    </div>
    {{ end }}
</body>

</html>
```
- 访问`http://localhost:1313`可以查看首页，展示所有页面的列表

### 设置导航菜单 ###
- config.toml中添加

```markup
# 主导航菜单
name="首页"
url="/"
weight="1"
[[menu.main]]
name="博文"
url="/post"
weight="2"
[[menu.main]]
name="新闻"
url="/news"
weight="3"
[[menu.main]]
name="关于"
url="/about"
weight="4"
[[menu.main]]
name="父菜单"
Identifier="parentID1"
weight="5"
[[menu.main]]
parent="parentID1"
name="子菜单1"
weight="1"
[[menu.main]]
parent="parentID1"
name="子菜单2"
weight="2"

# 页脚导航菜单
[[menu.foot]]
name="网站首页"
url="/"
weight="4"
[[menu.foot]]
name="博文集合"
url="/post"
weight="3"
[[menu.foot]]
name="新闻列表"
url="/news"
weight="2"
[[menu.foot]]
name="关于我们"
url="/about"
weight="1"
```
- 调用导航菜单，例如在首页中加上

```html
    <!-- 导航菜单的调用 -->
    <h2>主导航菜单</h2>
    <ul>
      {{ range .Site.Menus.main }} 
      {{if .HasChildren}}
      <li>{{.Name}}</li>
      <ul>
        {{ range .Children }}
        <li>
          <a href="{{ .URL }}">{{ .Name }}</a>
        </li>
        {{ end }}
      </ul>
      {{ else }}
      <li><a href="{{.URL}}">{{.Name}}</a></li>
      {{end}} {{ end }}
    </ul>

    <h2>页脚导航菜单</h2>
    <ul>
      {{ range .Site.Menus.foot }}
      <li><a href="{{.URL}}">{{.Name}}</a></li>
      {{ end }}
    </ul>
```
- 访问首页可以查看设置的导航菜单


