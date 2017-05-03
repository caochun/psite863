# 处理无法通过hexo插件deploy到heroku的问题
@2017-5-4

首先需要下载heroku的cli工具，参考[https://devcenter.heroku.com/articles/heroku-cli]。安装后需执行`heroku login`，输入email和密码登录。
克隆本项目（psite863），编辑文章，然后执行`hexo d`，在`psite863/public`目录生成新的静态页，手工将`psite863/public`目录下的文件都拷贝到`psite863/.deploy_heroku`

在`psite863/.deploy_heroku`目录下[执行git命令手动部署](https://dashboard.heroku.com/apps/mcc863/deploy/heroku-git)：
```
git remote add heroku https://git.heroku.com/mcc863.git # 或 git@heroku.com:mcc863.git
git add .
git status 
git commit -m "x"
git push heroku master -f
```

> `psite863/.deploy_heroku/package.json`文件是将静态页伪装成一个nodejs项目，才能触发heroku的build操作。在heroku无法直接更新静态页。

# 设置hexo

## 简介
[hexo](http://hexo.io/)是一个基于[Node.js](https://nodejs.org/en/)的博客生成工具。它把 Markdown 格式的博客文章(post)转换成一个静态的html博客网站，不仅生成post正文页面，还生成了 博客首页的列表，分类，tags等页面。
hexo有多种博客主题可选，这里使用了 jacman 主题。

[heroku](http://heroku.com)是一个流行的PaaS服务商，我们的网站就放在了heroku上。

## 安装程序

下面的设置过程是在Ubuntu 16.04上进行的。
### 安装git
```
$ sudo apt-get install git

# 配置 git 的用户名，Email，git默认使用`~/.ssh`下的 key
$ git config --global user.name "Your Name"
$ git config --global user.email "your@email.com"
```

### 安装node.js
直接使用[https://nodejs.org]提供的二进制包。

### 安装hexo
```
$ sudo npm install hexo-cli -g 
```

## 克隆博客repo
```
$ cd  ~
$ git clone git@github.com:caochun/psite863.git
$ cd psite863
$ 7z x node_modules.7z 
$ 7z x themes.7z
```

## 撰写文章

执行下面的命令，在`source/_posts/`下生成名为 `New-Post.md` 的文件。新文章的模板是`scaffolds/post.md`，模板的格式请见下面的补充说明。
```
$ hexo n "New Post" # new
```

更多可参考[使用hexo写作](http://hexo.io/docs/writing.html)的说明文档 和 [Markdown Cheatsheet](http://www.bagtheweb.com/b/xjmOex)。

## 发布文章

发布文章需要执行*2个*步骤：

1) 发布到 heroku
```
$ hexo d -g
```

2) 同步源文件到github
```
$ git add -A
$ git commit -m "update"

$ git pull origin master
$ git push origin master
```

## 其它

### `scaffold/post.md`模板
增加了catagory，tags，及摘要和正文的分割线`<!--more-->`。
>注意：Markdown对空格和空行敏感。

```
title: {{ title }}
date: {{ date }}
author: 
category: 
tags: [tag1, tag2]
---

摘要部分

<!--more-->
---

正文部分
```

### hexo 常用命令
```
$ hexo g     # generate 生成静态html
$ hexo s     # server   本地预览，默认地址在http://localhost:4000。
             # 编辑了post后，不必重新运行该命令，刷新一下页面就会更新编辑的结果。直到按Ctrl + C才会退出。
$ hexo clean # 删除 public/ 文件夹下所有内容

$ hexo d     # deploy   部署整个网站到heroku

$ hexo s -g  # 依次执行生成和本地预览
$ hexo d -g  # 依次执行生成和部署
```

### psite863目录内容

| 文件/目录       |       说明     |
| ------------- | ------------- |
| `_config.yml`   | 博客的配置文件 |
| `.deploy_git/`  | 用于push到Github、heroku等的git仓库，里面有与`public/`相同的内容及`.git`的记录 |
| `public/`       | 生成的完整的静态html网站，执行 `hexo clean` 会清除此文件夹 |
| `scaffolds/`    | 里面有`post.md`，`page.md`和`draft.md`三个模板文件，`post.md`是博客文章的默认模板 |
| `node_modules/` | 每个blog的目录中安装的hexo插件 |
| `source/`       | 撰写的博客文章要放在`source/_post`下，`source/`文件夹下的.md文件都会被生成为.html文件，其它类型的文件和文件夹则保持原样复制到 `public/`。可以在这里新建一个 `img/` 文件夹用于保存图片，文章中可以用`![图片说明](/img/image.png)`这样的代码来插入图片。 |
| `themes/`       | 存放主题相关文件，各主题可能也有自己的`_config.yml。` |
