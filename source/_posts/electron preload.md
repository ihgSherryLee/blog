#### electron preload 的用法

在electron中，主进程和渲染进程进行通信，可以通过ipc模块发送和接收信息。

渲染器进程

```js
const ipc = require('electron').ipcRenderer

const asyncMsgBtn = document.getElementById('async-msg')

asyncMsgBtn.addEventListener('click', function () {
  ipc.send('asynchronous-message', 'ping')
})

ipc.on('asynchronous-reply', function (event, arg) {
  const message = `异步消息回复: ${arg}`
  document.getElementById('async-reply').innerHTML = message
})
```

主进程

```js
const ipc = require('electron').ipcMain

ipc.on('asynchronous-message', function (event, arg) {
  event.sender.send('asynchronous-reply', 'pong')
})
```

但是，一旦我们直接load一个网址，渲染的不是一个node运行时的html，而是一个webpack打包后的静态页面，我们不可能从网址的静态页面中往主进程发送消息或接受主进程的消息。这个时候，**preload**就排上用场了。

> Preload:在页面运行其他脚本之前预先加载指定的脚本 无论页面是否集成Node, 此脚本都可以访问所有Node API 脚本路径为文件的绝对路径。 当 node integration 关闭时, 预加载的脚本将从全局范围重新引入node的全局引用标志。

