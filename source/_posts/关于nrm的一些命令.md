---
title: 关于nrm的一些命令
categories:
  - nrm
tags:
  - nrm
  - 总结
abbrlink: 9455f24f
---

nrm是什么：

> ```
> nrm` can help you easy and fast switch between different npm registries, now include: `npm`, `cnpm`, `taobao`, `nj(nodejitsu)
> ```

其实就是帮助快速切换npm镜像包，在没有安装之前，最常用的设置镜像的命令是：

```
npm set registry https://registry.npm.taobao.org/

// 备忘 其余命令
// 获取某项配置
npm config get registry 

// 删除registry
npm config delete registry

// 直接编辑config
npm config edit
```

如果需要经常切换镜像，每次都要set，如果有个工具帮你记住各种不同的镜像元，且能快速切换，是不是就很好呢？那nrm就是这样一个工具。

安装：

```
npm install -g nrm
```

查看默认的镜像列表(*表示当前镜像地址)

```
  npm ---- https://registry.npmjs.org/
  cnpm --- http://r.cnpmjs.org/
* taobao - https://registry.npm.taobao.org/
  nj ----- https://registry.nodejitsu.com/
  npmMirror  https://skimdb.npmjs.com/registry/
  edunpm - http://registry.enpmjs.org/
```

直接use即可切换

```
 nrm use cnpm

 Registry has been set to: http://r.cnpmjs.org/
```

增加镜像

```
nrm add name url
```

删除镜像

```
nrm del name
```

