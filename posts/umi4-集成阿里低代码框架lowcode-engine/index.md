# Umi4 集成阿里低代码框架lowcode-engine


<!--more-->

# Umi4 集成阿里低代码框架lowcode-engine

![Umi4 集成阿里低代码框架lowcode-engine](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cfb2462f6c6e430fb769c7da8479ef2e~tplv-k3u1fbpfcp-zoom-crop-mark:3024:3024:3024:1702.awebp?)

携手创作，共同成长！这是我参与「掘金日新计划 · 8 月更文挑战」的第2天，[点击查看活动详情](https://juejin.cn/post/7123120819437322247)

## 前言

最近准备研究下阿里低代码框架lowcode-engine, 官方Demo是提供好的脚手架，由于我们的框架使用的是umi，官方文档提供了一些教程，在此记录下在umi4集成lowcode-engine.

## 实现

#### 搭建umi4项目

1.通过官方文档的[快速开始](https://link.juejin.cn/?target=https%3A%2F%2Fumijs.org%2Fdocs%2Ftutorials%2Fgetting-started)，我们可以快速创建出项目

先找个地方建个空目录

```bash
mkdir myapp && cd myapp
复制代码
```

通过官方工具创建项目, 这里我们采用pnpm包管理工具

```css
$ pnpm dlx create-umi@latest
✔ Install the following package: create-umi? (Y/n) · true
✔ Pick Npm Client › pnpm
✔ Pick Npm Registry › taobao
Write: .gitignore
Write: .npmrc
Write: .umirc.ts
Copy:  layouts/index.tsx
Write: package.json
Copy:  pages/index.tsx
Copy:  pages/users.tsx
Copy:  pages/users/foo.tsx
> @ postinstall /private/tmp/sorrycc-vylwuW
> umi setup
info  - generate files
复制代码
```

创建完成后，安装依赖, 执行 `pnpm dev`就可以看到我们的项目启动起来了。

#### 集成lowcode-engine

1. 引入UMD包, 我们在 `.umirc.ts`中配置externals,styles和scripts

```css
 externals: {
    'react': 'var window.React',
    'react-dom': 'var window.ReactDOM',
    'prop-types': 'var window.PropTypes',
    '@alifd/next': 'var window.Next',
    '@alilc/lowcode-engine': 'var window.AliLowCodeEngine',
    '@alilc/lowcode-editor-core': 'var window.AliLowCodeEngine.common.editorCabin',
    '@alilc/lowcode-editor-skeleton': 'var window.AliLowCodeEngine.common.skeletonCabin',
    '@alilc/lowcode-designer': 'var window.AliLowCodeEngine.common.designerCabin',
    '@alilc/lowcode-engine-ext': 'var window.AliLowCodeEngineExt',
    '@ali/lowcode-engine': 'var window.AliLowCodeEngine',
    'moment': 'var window.moment',
    'lodash': 'var window._',
  },
  styles: [
    'https://alifd.alicdn.com/npm/@alilc/lowcode-engine@latest/dist/css/engine-core.css',
    'https://g.alicdn.com/code/lib/alifd__next/1.23.24/next.min.css',
    'https://alifd.alicdn.com/npm/@alifd/theme-lowcode-light/0.2.0/next.min.css',
    'https://alifd.alicdn.com/npm/@alilc/lowcode-engine-ext@latest/dist/css/engine-ext.css'
  ],
  scripts: [
    {
      src: 'https://g.alicdn.com/code/lib/react/18.0.0/umd/react.development.js',
      defer: false,
    },
    {
      src: 'https://g.alicdn.com/code/lib/react-dom/18.0.0/umd/react-dom.development.js',
      defer: false,
    },
    {
      src: 'https://g.alicdn.com/code/lib/prop-types/15.7.2/prop-types.js',
      defer: false,
    },
    {
      src: 'https://g.alicdn.com/platform/c/react15-polyfill/0.0.1/dist/index.js',
      defer: false,
    },
    {
      src: 'https://g.alicdn.com/platform/c/lodash/4.6.1/lodash.min.js',
      defer: false,
    },
    {
      src: 'https://g.alicdn.com/code/lib/moment.js/2.29.1/moment-with-locales.min.js',
      defer: false,
    },
    {
      src: 'https://g.alicdn.com/code/lib/alifd__next/1.23.24/next.min.js',
      defer: false,
    },
    {
      src: 'https://alifd.alicdn.com/npm/@alilc/lowcode-engine@latest/dist/js/engine-core.js',
      defer: false,
    },
    {
      src: 'https://alifd.alicdn.com/npm/@alilc/lowcode-engine-ext@latest/dist/js/engine-ext.js',
      defer: false,
    }
```

1. 我们使用lowcode-enginey引擎提供的init进行初始化，`init()` 内部会调用 `ReactDOM.render()` 函数，这样初始化有些弊端。一些内容没有办法与外部的 React 组件进行通信，也就没有办法在一些自定义的 plugin 中获取 redux 上的全局数据等内容，但也有好处，就是低代码引擎比较独立，后期可以拆出供多个项目使用。

我们参考官方提供的[Demo](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Falibaba%2Flowcode-demo)的入口文件。在我们这里封装成一个`LowcodeEditor`组件。

```javascript
import { init, plugins } from '@alilc/lowcode-engine';
import { useEffect } from 'react';
import registerPlugins from './plugin';

import './global.less';

export default function LowcodeEditor() {
  useEffect(() => {
    const preference = new Map();
    preference.set('DataSourcePane', {
      importPlugins: [],
      dataSourceTypes: [
        {
          type: 'fetch',
        },
        {
          type: 'jsonp',
        },
      ],
    });

    (async function main() {
      await registerPlugins();
      console.log('first-2')

      init(
        document.getElementById('engine-container')!, {
          // designMode: 'live',
          // locale: 'zh-CN',
          enableCondition: true,
          enableCanvasLock: true,
          // 默认绑定变量
          supportVariableGlobally: true,
          // simulatorUrl 在当 engine-core.js 同一个父路径下时是不需要配置的！！！
          // 这里因为用的是 alifd cdn，在不同 npm 包，engine-core.js 和 react-simulator-renderer.js 是不同路径
          simulatorUrl: [
            'https://alifd.alicdn.com/npm/@alilc/lowcode-react-simulator-renderer@latest/dist/css/react-simulator-renderer.css',
            'https://alifd.alicdn.com/npm/@alilc/lowcode-react-simulator-renderer@latest/dist/js/react-simulator-renderer.js',
          ],
          requestHandlersMap: {
            fetch: async () => {}
          }
        },
        preference,
      );
    })();
  }, []);
  return <div id='engine-container' style={{backgroundColor: 'red'}}></div>;
}

复制代码
```

这里只是入口组件的初始化，组件内部还有很多内容，我们重Deom中copy出来。比如`plugin`，`assets.josn`,`schem.json`等，这里不做过多的介绍，里面的内容好多，后续我们一边学习，一边慢慢拆解，分享。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/34d2230ea2cc4c2e922c636e5473130f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

1. 使用`LowcodeEditor`组件，在我们需要的地方直接导入，使用就行

```javascript
import yayJpg from '../assets/yay.jpg';
import LowcodeEditor from '@/components/lowcode-editor2';

export default function HomePage() {
  return (
    <div style={{height: '100%'}}>
      <LowcodeEditor />
    </div>
  );
}

复制代码
```

4.访问页面

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f22b94432d4349b08cc3f7939e25e6ab~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

## 结束语

代码已上传到github,请查看[mi4-lowcode-app](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fjiechud%2Fuim4-lowcode-app)。
