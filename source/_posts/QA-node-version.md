---
title: QA-node-version
categories:
  - 汇总
tags:
  - 问题
  - 总结
abbrlink: 44e1c702
date: 2019-07-31 11:53:04
---

### Vue cli build 遇到的问题

使用vue-cli 3打包时，出现了问题：

```
vue-cli-service build --mode dev

/node_modules/_open@6.4.0@open/index.js:16
const wslToWindowsPath = async path => {
                               ^^^^

SyntaxError: Unexpected identifier
    at createScript (vm.js:56:10)
    at Object.runInThisContext (vm.js:97:10)
    at Module._compile (module.js:549:28)
    at Object.Module._extensions..js (module.js:586:10)
    at Module.load (module.js:494:32)
    at tryModuleLoad (module.js:453:12)
    at Function.Module._load (module.js:445:3)
    at Module.require (module.js:504:17)
    at require (internal/module.js:20:19)
    at Object.<anonymous> (/root/.jenkins/workspace/dev-privatization-web-edap/node_modules/_@vue_cli-shared-utils@3.9.0@@vue/cli-shared-utils/lib/openBrowser.js:9:14)
npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! privatization-information-search@0.1.0 build:dev: `vue-cli-service build --mode dev`
npm ERR! Exit status 1
npm ERR! 
npm ERR! Failed at the privatization-information-search@0.1.0 build:dev script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     /root/.npm/_logs/2019-07-31T03_09_18_026Z-debug.log
```

可是在本地打包时却没有发现这个问题，在运维脚本打包的服务器上却出现了，然后在github的某个库的issue上找到了[答案](https://github.com/tapio/live-server/issues/305)：

> Nice write up [@pm-radu](https://github.com/pm-radu), also seeing this issue here.
>
> It appears the two options for resolving it are to modify the project's `package.json` on [line 48](https://github.com/tapio/live-server/blob/01cd7f970439d42537bbe13b4885befb816a0446/package.json#L48) to specify a version of Node.js greater than 7.6 when support was added for `async/await`. Additionally the dependency `opn` is now called [`open`](https://www.npmjs.com/package/open) and should be updated.
>
> Alternately if maintaining support for the oldest versions of Node is a priority, [line 24](https://github.com/tapio/live-server/blob/01cd7f970439d42537bbe13b4885befb816a0446/package.json#L24) could be modified to specify the last `opn` version without `async/await` which is 5.4.0.

服务器node版本是6,而async/await语法需node > 7.6，所以报错了。