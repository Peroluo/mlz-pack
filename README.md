# mlz-pack | 内置webpack的依赖处理的打包工具

[![CircleCI](https://travis-ci.org/juicecube/mlz-pack.svg?branch=master)](https://travis-ci.org/juicecube/mlz-pack)

## 安装

本地项目安装

由于内含mozjpeg-bin，optipng-bin两个binary包，需要.npmrc中添加以下配置，直接安装很大几率安装不成功。

```
mozjpeg_binary_site=https://npm.taobao.org/mirrors/mozjpeg-bin
optipng_binary_site=https://npm.taobao.org/mirrors/optipng-bin
```
```
npm i @mlz/pack -D
```

## 使用

支持serve和build两种命令，server用于本地开发，build用于代码构建。

package.json
```js
{
  "scripts": {
    "start": "mlz-pack serve",
    "build": "mlz-pack build"
    ...
  }
}
```
serve命令的使用

```
Usage: mlz-pack serve [options] [entry]

serve your project in development mode

Options:
  -p, --port <port>  port used by the server (default: 8080)
  -h, --help         output usage information
```

build命令的使用

```
Usage: mlz-pack build [options] [entry]

build your project from a entry file(.js||.ts||.tsx), default: src/index.tsx

Options:
  -e, --env <environment>  dev or prod（default: prod）
  -d, --dest <dest>        output directory (default: build)
  -h, --help               output usage information
```

eject命令使用

```
Usage: mlz-pack eject [options]

eject webpack config.

Options:
  -h, --help  output usage information
```

## 自定义配置

读取项目根目录下的**mlz-pack.js**

**Tips:**

开启热加载需要在入口文件加入以下代码：

```
if(module.hot){
  module.hot.accept()
}
```

### webpack配置

| Name 	| Type 	| Default 	| Description 	|
|:-------------------------------------:	|:--------------------------------------------:	|:---------------------------------------------------------------------------------:	|:---------------------------------------------------------------------------------------------------:	|
| **[`rootPath`](#rootPath)** 	| `string` 	| `process.cwd()` 	| 项目的根目录 	|
| **[`entryPath`](#entryPath)** 	| `string \| string[] \| Entry \| EntryFunc` 	| `{ index: path.resolve(process.cwd(), 'src/index.tsx') }` 	| 入口文件（webpack的entry） 	|
| **[`buildPath`](#buildPath)** 	| `string` 	| `build` 	| build文件 	|
| **[`publicPath`](#publicPath)** 	| `string` 	| `/` 	| js,css,图片等资源前缀 	|
| **[`tsconfig`](#tsconfig)** 	| `string` 	| `undefined` 	| tsconfig路径（可选） 	|
| **[`devServer`](#devServer)** 	| `{port:string;open:boolean;}` 	| `{port: 8080, open: true}` 	| dev-server相关配置 	|
| **[`pxtorem`](#pxtorem)** 	| `Object` 	| `undefined` 	| 是否开启px转rem，还有相关配置 	|
| **[`cssScopeName`](#cssScopeName)** 	| `string` 	| `[local]__[hash:base64:5] \| [path][name]__[local]` 	| css的className编译规则，默认：dev环境是`[path][name]__[local]`，正式环境是`[name]__[hash:base64:5]` 	|
| **[`alias`](#alias)** 	| `{[key:string]:string}` 	| `undefined` 	| 别名，可不填，默认会读tsconfig里的paths 	|
| **[`definePlugin`](#definePlugin)** 	| `{[key:string]:any}` 	| `undefined` 	| 全局变量 	|
| **[`analyzePlugin`](#analyzePlugin)** 	| `boolean` 	| `false` 	| 是否开启bundle分析 	|
| **[`htmlPlugin`](#htmlPlugin)** 	| `Object` 	| `{filename: 'index.html',template: path.resolve(process.cwd(), 'src/index.ejs')}` 	| htmlplugin的参数设置 	|
| **[`libs`](#libs)** 	| {[key:string]:string[]} 	| `undefined` 	| 用于单独切分第三方依赖的bundle的配置 	|
| **[`loaderOptions`](#loaderOptions)** 	| `RuleSetRule[]` 	| `undefined` 	| loader扩展 	|
| **[`pluginOptions`](#pluginOptions)** 	| `any[]` 	| `undefined` 	| plugin扩展 	|
| **[`babel`](#babel)** 	| `any` 	| `undefined` 	| babel扩展 	|

### `rootPath`

Type: `string` Default: `process.cwd()`

设置项目的根目录，一般不用设置。

### `entryPath`

Type: `string` Default: `{ index: path.resolve(process.cwd(), 'src/index.tsx') }`

设置项目的入口文件，即webpack的entry配置(只针对单页应用进行设置,暂时没有测试过多页应用)。

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    entryPath: [path.join(__dirname, './src/index.tsx')],
  }
}
```

### `buildPath`

Type: `string` Default: `build`

build文件夹(绝对地址)，默认是`path.resolve(process.cwd(), 'build')`

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    buildPath: path.resolve(process.cwd(), 'build'),
  }
}
```

### `publicPath`

Type: `string` Default: `/`

js,css,图片等资源前缀，默认是`/`，正式环境请设置为cdn的地址

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    publicPath: '/',
  }
}
```

### `tsconfig`

Type: `string` Default: `undefined`

由于使用了 (tsconfig-paths-webpack-plugin)[https://github.com/dividab/tsconfig-paths-webpack-plugin]，默认会读取`cwd`的tsconfig.json中的path来设置webpack的alias。`tsconfig`此配置会设置该plugin的tsconfig的地址。

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    tsconfig: path.resolve(process.cwd(), 'tsconfig.json'),
  }
}
```

### `devServer`

Type: `{ port:string, open:boolean, }` Default: `{ port: '8080', open: true, }`

开发环境的server的配置，端口号和是否自动打开浏览器。

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    devServer: {
      port: '8080',
      open: true,
    },
  },
}
```

### `pxtorem`

Type: 
```
{
  rootValue?:number;
  propList?:string[];
  selectorBlackList?:string[];
  replace?:boolean;
  minPixelValue?:number;
};
```
Default: `undefined`

是否开启px转rem的功能，并且设置相关配置，有关于移动端适配的方案详细参考文档(https://shimo.im/docs/69JqRr9yRCGJXHDv)。
```
{
  rootValue: 100, // 标准设计稿的根元素的font-size
  unitPrecision: 5, // 小数点位数
  propList: ['*', '!no-rem'], // 需要转换的属性
  selectorBlackList: [], // 不需要转换的属性
  replace: true,
  mediaQuery: false,
  minPixelValue: 0, // 最小值
}
```

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    pxtorem: {
      rootValue: 100,
      propList: [
        '*',
        '!min-width',
        '!border',
        '!border-left',
        '!border-right',
        '!border-top',
        '!border-bottom',
      ],
      selectorBlackList: [
        'no_rem'
      ],
    },
  },
}
```

### `cssScopeName`

Type: `string` Default: `[local]__[hash:base64:5] (prod) \| [path][name]__[local] (dev)	`

className的编译规则，默认：dev环境是[path][name]__[local]，正式环境是[name]__[hash:base64:5]

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    cssScopeName: '[local]__[hash:base64:5]',
  },
}
```

### `alias`

Type: `{[key:string]:string}` Default: `undefined`

别名即webpack配置中的alias，可不填，默认会读tsconfig里的paths，别名建议在tsconfig中配置。

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    alias: {src: path.resolve(__dirname, 'src')},
  },
}
```

### `definePlugin`

Type: `{[key:string]:any}` Default: `undefined`

全局变量，即webpack中`webpack.DefinePlugin`的配置

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    definePlugin: {DEBUG: false},
  },
}
```

### `analyzePlugin`

Type: `boolean` Default: `false`

是否开启bundle的分析，对于build和资源优化有很大帮助。

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    analyzePlugin: false,
  },
}
```

### `htmlPlugin	`

Type: 
```
{
  template?:string;
  favicon?:string;
  filename?:string;
  [key:string]:any;
}
```
Default:
```
{
  filename: 'index.html',
  template: path.resolve(process.cwd(), 'src/index.ejs'),
}
```
html-plugin配置，用于设置HTML的编译属性

`template`：指html的模板可以是.html|.ejs等文件

`favicon`：favicon小图标的地址

`filename`: 编译后的HTML名称

`[key:string]:any;`: 指的是可写入HTML的变量

默认给了loading变量用于设置HTML的loading减少白屏时间，在HTML中可以直接使用，例如：
```html
<!DOCTYPE html>
<html>
<head lang="zh-CN">
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0, user-scalable=no">
  <meta name="format-detection" content="telephone=no" />
  <meta name=”renderer” content=”webkit”>
  <title>Juice Cube</title>
  <%= htmlWebpackPlugin.options.loading.css %>
</head>
<body>
  <div id="root">
    <%= htmlWebpackPlugin.options.loading.html %>
  </div>
  </body>
</html>
```

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    {
      filename: 'index.html',
      template: path.resolve(process.cwd(), 'src/index.ejs'),
      loading: {
        css: '',
        html: '',
      };
    }
  },
}
```

### `libs`

Type: `{[key:string]:string[]}	` Default: `undefined`

将node_modules里的包进行分类打包，根据不同包的更新频率不同有利于缓存，默认会为第三方包创建名为`venderLibs`的bundle。

例如下边代码会分别生成，名为vender的bundle包含`react`和`react-dom`，名为juice的bundle包含作`@mlz/pack`和`@mlz/lint`，其他的第三方依赖会在名为venderLibs的bundle

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    libs: {
      vender: ['react', 'react-dom'],
      juice: ['@mlz/pack', '@mlz/lint'],
    }
  },
}
```

### `loaderOptions`

Type: `RuleSetRule[]` Default: `undefined`

webpack的loader扩展

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    loaderOptions: [
      {
        test: /\.svg$/,
        use: [
          '@svgr/webpack',
        ],
      },
    ]
  },
}
```

### `pluginOptions`

Type: `any[]` Default: `undefined`

webpack的plugin扩展

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    pluginOptions: [
      new ProgressBarPlugin(),
    ]
  },
}
```

### `babel`

Type: `any` Default: `undefined`

babel扩展

**mlz-pack.js**

```js
module.exports = {
  webpack: {
    babel: {
      'presets': [
        '@babel/preset-react',
      ],
    }
  },
}
```

### TODO
1.定制化配置: 可以通过 mlz-pack --init 初始化配置文件