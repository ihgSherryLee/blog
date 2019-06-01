title: 入坑 hexo 写 blog
categories: - 前端

---

#### 入坑 hexo 写 blog

​ 很久很久之前，有玩过 github page，那时用的是 jekyll，折腾了下，苦于样式并且懒，就放弃了。emmmm，现在又想玩起来了，就直接试下 hexo 咯。

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
