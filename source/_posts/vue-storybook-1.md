---
title: vue-storybook-1
categories:
  - vue
tags:
  - vue
  - storybook
abbrlink: 8cabe836
date: 2019-08-13 17:30:50
---

# 在vue项目中引入storybook

之前使用了vue-cli生成项目，并且选择了typescript，那么，要怎么引入[storybook](https://storybook.js.org/docs/guides/guide-vue/)呢？

#### 手动安装storybook

官网上有storybook的cli，但因为我已经存在一个vue项目，所有使用手动安装：

```
npm install @storybook/vue --save-dev
```

然后还需要一些loader,因为我们用是ts,而且有一些loader在vue项目生成时已经安装，所有只需安装没有的部分（可以在使用下一步命令启动storybook后看漏了什么loader）

```
npm i babel-preset-vue awesome-typescript-loader -D
```

#### npm script

在package.json中添加npm命令

```
  "scripts": {
    "storybook": "start-storybook"
  },
```

#### 添加config file

在项目根目录添加文件.storybook/config.js

```js
import { configure } from '@storybook/vue'

function loadStories() {
  require('../src/stories/index.ts')
}

configure(loadStories, module)
```

除此之外，还必须添加webpack.config.js，[参考](https://github.com/storybookjs/storybook/issues/5294)，来处理对ts的转化，不然像.vue里的@Component等，都会报Unexpected character '@'

```js
const vueConfig = require('@vue/cli-service/webpack.config.js');

module.exports = async ({ config }) => {
  return {
    ...config,
    resolve: {
      ...vueConfig.resolve,
      alias: {
        ...vueConfig.resolve.alias,
        'vue$': 'vue/dist/vue.esm.js' // if you need it
      },
    },
    module: {
      ...vueConfig.module,
      rules: vueConfig.module.rules,
    },
  }
}
```

#### 编写stories

在src文件夹下新建/stories/index.ts（根目录也可以）

```typescript
import { storiesOf } from '@storybook/vue';
import MyButton from '../components/MyButton.vue';

storiesOf('Button', module)
  .add('with text', () => '<my-button>with text</my-button>')
  .add('with emoji', () => '<my-button>😀 😎 👍 💯</my-button>')
  .add('as a component', () => ({
    components: { MyButton },
    template: '<my-button :rounded="true">rounded</my-button>',
  }));
```

MyButton组件就自己随便写写就好。

然后发现tslint有提示@storybook/vue没有定义的声明types，所以要安装

```
 npm install @types/storybook__vue -D
```

这样storybook就能跑起来了。