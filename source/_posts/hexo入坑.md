---
title: 入坑 hexo 写 blog
abbrlink: 1b39d25c
---

#### 入坑 hexo 写 blog

 很久很久之前，有玩过 github page，那时用的是 jekyll，折腾了下，苦于样式并且懒，就放弃了。emmmm，现在又想玩起来了，就直接试下 hexo 咯。

hexo 有[中文文档地址](https://hexo.io/zh-cn/docs/index.html)，还是很方便的。

##### 第一步 当然是安装啦

先是全局安装 hexo

```
sudo npm install -g hexo-cli
```

然后就是初始化

```javascript
hexo init blog // blog就是文件夹名字，可随意，与github page 项目无关
cd blog
npm install
```

此时，blog 的初始主题已经安装好了，运行

```
hexo s
```

本地服务就启动了

```
 Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.
```

打开网址可以看到 blog 已经建好了，然后就可以开始改主题啦，[知乎](https://www.zhihu.com/question/24422335)的一个回答里已经列了多 star 的主题，我选择的是[next](https://github.com/iissnan/hexo-theme-next/blob/master/README.cn.md)。在 blog 目录下：

```
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

然后在\_config.yml 文件里找到主题字段，改为

```
theme: next
```

##### 2、同步当前博客到 github pages

安装同步必要的插件

```
npm install hexo-deployer-git
```

在\_config.yml 文件里找到 deploy 字段，添加下列字段(注意：':'后面要带个空格，没有空格则 deploy 不了)

```
deploy:
  type: git
  repo: https://github.com/ihgSherryLee/ihgSherryLee.github.io.git // 我的github pages 的仓库地址
  branch: master
```

然后分步执行，即可在你的 github pages 看到了。

```
hexo clean // 清除缓存
hexo g     // 生成文件夹
hexo d     // 发布
```

##### 3、为你的blog添加分类和标签

```
hexo new page tags
```

这会在source文件夹内生成一个tags文件夹，里面有index.md

```
---
title: tags
date: 2019-07-12 00:10:57
type: "tags"
---

```

然后在blog的头部，加入tags

```
tags:
- electron // some tags
- preload
---
```

这样，文章就有了标签，然后，就在对应themes里修改_config.yml，找到menu

```yml
menu:
  home: / || home
  #about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```

把tags的目录加上，这样首页的菜单下就会出现标签的分类链接。

##### 3、当blog中要显示图片时

1️⃣可以在source文件夹中新建一个images目录，这样，在首页和文章页都能访问到

在mardown里用绝对路径访问到

2️⃣相对路径的做法：在_config.yml中设置

```
post_asset_folder: true
```

这样hexo new sth 时，会生成一个相同名字的文件夹，可以存放图片，使用相对路径引入图片，这样只能在文章中访问到，首页无法访问，如果要首页也能访问，可以使用标签插件语法

```
{% asset_img image.jpg This is an image %}
```

但这种做法，会使每个文章都生成一个文件夹，即使只是要存放图片的地方需要，但感觉还是会影响项目文件结构，见仁见智吧，我还是选择第1️⃣种做法

3️⃣还可以使用cdn引用图片