title: electron采坑(二)
---

在后续维护electron项目中想到：每次客户端有更新(即使改动不大),都要重新打包后把客户端发给供应商用户，虽随着用户越来越多，更新也越来越麻烦，花在沟通上的成本更大，所以考虑使用自动更新，减少沟通成本，让用户在没有察觉的情况下就能更新到最新版。

> 官方支持的方法是利用内置的[Squirrel](https://github.com/Squirrel)框架和Electron的[autoUpdater](https://electronjs.org/docs/api/auto-updater)模块。

但因为之前适用electron-builder工具打包，且，其自带有更新功能，所以还是使用electron-builder的自动更新。

1、引入自动更新插件，在主进程中使用

```js
const { autoUpdater } = require('electron-updater')

autoUpdater.checkForUpdates()
```

这将使用electron-updater的默认配置，自动下载并弹出提醒，但提示语固定且为英语，所以在查看了AppUpdater.js后，修改了更新的回调。

```js
autoUpdater.checkForUpdates().then(it => {
    const downloadPromise = it.downloadPromise
    if (downloadPromise == null) {
      return
    }

    downloadPromise.then(() => {
      new Notification({
        title: '新版本提醒',
        body: `${autoUpdater.app.name} '新版本' ${
          it.updateInfo.version
        } '将会在系统关闭后自动更新'`,
      }).show()
    })
  })
```

checkForUpdates是electron-updater的内置方法，是一个promise，可以在成功回调里做特殊处理。参考checkForUpdatesAndNotify方法，在downloadPromise完成后弹窗提示：

```js
// AppUpdater.js
  checkForUpdatesAndNotify() {
    if (_electronIsDev().default) {
      return Promise.resolve(null);
    }

    const checkForUpdatesPromise = this.checkForUpdates();
    checkForUpdatesPromise.then(it => {
      const downloadPromise = it.downloadPromise;

      if (downloadPromise == null) {
        const debug = this._logger.debug;

        if (debug != null) {
          debug("checkForUpdatesAndNotify called, downloadPromise is null");
        }

        return;
      }

      downloadPromise.then(() => {
        new (_electron().Notification)({
          title: "A new update is ready to install",
          body: `${this.app.getName()} version ${it.updateInfo.version} has been downloaded and will be automatically installed on exit`
        }).show();
      });
    });
    return checkForUpdatesPromise;
  }
```

如果需要更多定制，可以使用以下事件：

```js
autoUpdater.on('error', (ev, err) => {
  // 更新出错，其中一步错误都会emit
})
autoUpdater.on('checking-for-update', () => {
  
})
autoUpdater.on('update-available', (ev, info) => {

})
autoUpdater.on('update-not-available', (ev, info) => {
  
})
autoUpdater.on('download-progress', (ev, progressObj) => {
  // sendStatusToWindow('Download progress...')
})
autoUpdater.on('update-downlo aded', (ev, info) => {
  // sendStatusToWindow('Update downloaded; will install in 5 seconds')
})
```

2、[发布方式](https://www.electron.build/configuration/publish)：

> The [publish](https://www.electron.build/configuration/configuration#Configuration-publish) key contains a set of options instructing electron-builder on how it should publish artifacts and build update info files for [auto update](https://www.electron.build/auto-update).
>
> `String | Object | Array<Object | String>` where `Object` it is [BintrayOptions](https://www.electron.build/configuration/publish#bintrayoptions), [GenericServerOptions](https://www.electron.build/configuration/publish#genericserveroptions), [GitHub](https://www.electron.build/configuration/publish#githuboptions), [S3Options](https://www.electron.build/configuration/publish#s3options) or [SpacesOptions](https://www.electron.build/configuration/publish#spacesoptions). Order is important — first item will be used as a default auto-update server. Can be specified in the [top-level configuration](https://www.electron.build/configuration/configuration#configuration) or any platform- ([mac](https://www.electron.build/configuration/mac), [linux](https://www.electron.build/configuration/linux), [win](https://www.electron.build/configuration/win)) or target- (e.g. [nsis](https://www.electron.build/configuration/nsis)) specific configuration.
>
> If `GH_TOKEN` is defined — defaults to `[{provider: "github"}]`.
>
> If `BT_TOKEN` is defined and `GH_TOKEN` is not — defaults to `[{provider: "bintray"}]`

我们使用的是

```json
"publish": [
      {
        "provider": "generic",
        "url": "http://localhost:8080/installer"
      }
    ]
```

此时，重新打包会生成latest.yml:

```yml
version: 1.0.2
files:
  - url: erp Setup 1.0.2.exe
    sha512: b5ck9cBaZKgKAQhJ4hfh6xOtMzqZoWvxbudlK3dBKYh7qImOcgPg19zfwnAoZ1/+3zKFHktDCzBY4pSbptc1Hw==
    size: 75291392
path: erp Setup 1.0.2.exe
sha512: b5ck9cBaZKgKAQhJ4hfh6xOtMzqZoWvxbudlK3dBKYh7qImOcgPg19zfwnAoZ1/+3zKFHktDCzBY4pSbptc1Hw==
releaseDate: '2019-02-25T10:26:05.909Z'
```

这是程序判断有无更新的依据，url(http://localhost:8080/installer，假设起在本地，也可以换成线上的地址)上存放安装包和latest.yml文件，运行客户端时会访问url的latest.yml查询是否有版本更新。

遇到的坑：

- electron provider url 测试为本地时，漏了个/,导致ERR_INVALID_URL

- electron provider url 测试为线上时，当时线上安装包地址使用软连接，不是带版本号的文件名，而是直接erp.exe，但在测试自动更新时发现，无论如何更新安装包和latest.yml文件，都没有改变sha512，即使检测到了新版本，可是安装包的地址指向不对，一直是第一次上传的版本，因为这个url使用了cdn缓存，导致软连接一直指向了最开始上传的版本，即使刷新了cdn地址和预加载大文件都不可以，所以最后没有使用软连接，而是直接带上了版本号，这样就能正常更新。

  ![image-20190304164623128](/Users/sherry/Library/Application Support/typora-user-images/image-20190304164623128.png)

- 在这次更新electron时，需要做到打开多个客户端，在修改代码后测试发现，第一个客户端中能正常获取localStorage，但之后的客户端都无法获取，查看[issue](https://github.com/electron/electron/issues/2493)后发现，这是因为两个浏览器进程使用的是相同路径的数据，一旦文件被一个进程读取(localStorage是以本地文件存储的方式)，即被锁住，其他进程无法读取，除非把这些数据拷贝到另一个路径下。所以electron[推荐使用单实例窗口](https://github.com/electron/electron/issues/12763)，否则可能会出现无法获取到部分数据。

  > Running multiple instances of Electron is generally a bad idea and [not really supported](https://github.com/electron/electron/issues/4727) with the default configuration. Some renderer features don't work in this scenario ([localStorage](https://github.com/electron/electron/issues/2493), [IndexedDb](https://github.com/electron/electron/issues/10792), persisted sessions?)

  [打开一个窗口会创建3个进程](https://github.com/electron/electron/issues/2437)，这是由于Chromium的特征导致的：

  > Yes. When you run Electron it will run 3 processes. That's because of Chromium's multi-process architecture