---
title: electron踩坑(一)
categories:
  - electron
tags:
  - electron
  - 踩坑
abbrlink: 4d2accbc
---

公司项目使用electron开发，

> [Electron](https://electronjs.org/)是由Github开发，用HTML，CSS和JavaScript来构建跨平台桌面应用程序的一个开源库。 Electron通过将[Chromium](https://www.chromium.org/Home)和[Node.js](https://nodejs.org/)合并到同一个运行时环境中，并将其打包为Mac，Windows和Linux系统下的应用。

使用electron将公司网站打包成应用，伪装成专业的bs客户端。

安装和入门在[GitHub](https://github.com/electron/electron)上都有

```
git clone https://github.com/electron/electron-quick-start
cd electron-quick-start
npm install
npm start
```

clone之后修修改改就形成了现在的项目。

#### 使用electron-builder打包成应用程序exe/dmg

下载yarn

```
yarn add electron-builder --dev
```

修改package.json，设置appId，使windows

```
"build": {
  "appId": "your.id",
  "mac": {
    "category": "your.app.category.type"
  }
}
"scripts": {
  "pack": "electron-builder --dir",
  "dist": "electron-builder"
}
```

然后打包就会在dist目录生成.exe(在windows打包)

```
yarn dist
```

具体的dist 参数可以参考 https://www.electron.build/cli

```
electron-builder // 不指定平台和位数，默认会打当前打包程序所在平台的相应位数版本
electron-builder --win --ia32 --x64 // 指定平台和位数，将32位和64位打成同一个安装包
```

若要分开打成两个包，需分别执行--ia和x64两次得到2个包:[Issue](https://github.com/electron-userland/electron-builder/issues/3293)，但因为两个安装包生成同一名字的latest.yml，会被覆盖，自动更新会替换后生成的安装包，不然就要将两个安装包和latest.yml放在不同文件夹下，保证自动更新正常。

注!!!

不要指望一个平台能打包全平台的安装包。

> Don’t expect that you can build app for all platforms on one platform.
>
> - If your app has native dependency, it can be compiled only on the target platform unless [prebuild](https://www.npmjs.com/package/prebuild) is not used.
>
>   [prebuild](https://www.npmjs.com/package/prebuild) is a solution, but most node modules [don’t provide](https://github.com/atom/node-keytar/issues/27) prebuilt binaries.
>
> - macOS Code Signing works only on macOS. [Cannot be fixed](http://stackoverflow.com/a/12156576).



注：

- Win 10 notification bug：这个项目一开始并不是打包成安装包后安装成桌面应用，而是打包成asar归档规格式，但因为后来网页需要使用通知notification，发现在win10的某一个版本16299.19里无法显示通知，但是有通知的声音播放，查阅相关文档后发现，这是win10的feature，必须使用有appId的安装包安装后才能显示通知(相关[issue](https://github.com/electron/electron/issues/10864))。所以使用[electron-builder](https://github.com/electron-userland/electron-builder)打包成安装包.
- win 7 notification bug：在win7电脑上使用通知，若通知tag相同，则只会弹第一次，即使刷新了页面，也不好弹第二次，只有关闭应用程序后重开才会再次弹窗。[issue](https://github.com/electron/electron/issues/11189)。所有要将tag加个random，避免相同。

