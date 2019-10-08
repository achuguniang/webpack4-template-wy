# webpack4-template-wy

本项目基于 webpack4 搭建 vue 项目。
你可以用此项目搭建属于你的项目！！
欢迎 star!

## webpack介绍
1. Webpack 是一个前端资源加载和打包工具;在 Webpack 里一切文件皆模块，通过 Loader 转换文件，通过 Plugin 注]
入钩子，最后输出由多个模块组合成的文件,打包成浏览器可以识别并正常运行的代码。

## webpack构建

> 构建就是把源代码转换成发布到线上的可执行 JavaScrip、CSS、HTML 代码，包括如下内容。

1.代码转换：TypeScript 编译成 JavaScript、SCSS或Less 编译成 CSS 等。

2.文件优化：压缩 JavaScript、CSS、HTML 代码，压缩合并图片等。

3.代码分割：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载。

4.模块合并：在采用模块化的项目里会有很多个模块和文件，需要构建功能把模块分类合并成一个文件。

5.自动刷新：监听本地源代码的变化，自动重新构建、刷新浏览器,nodemon。

6.代码校验：在代码被提交到仓库前需要校验代码是否符合规范，以及单元测试是否通过。

7.自动发布：更新完代码后，自动构建出线上发布代码并传输给发布系统。

构建其实是工程化、自动化思想在前端开发中的体现，把一系列流程用代码去实现，让代码自动化地执行这一系列复杂的流程。 构建给前端开发注入了更大的活力，解放了我们的生产力,更加方便了我们的开发。

## webpack.config.js配置项简介
1. 入口起点(entry point)指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。
2. Output：输出结果，在`Webpack`经过一系列处理并得出最终想要的代码后输出结果，配置项用于指定输出文件夹，默认是`./dist`。
3. Module：模块，在`Webpack`里一切皆模块，`Webpack`会从配置的`Entry`开始递归找出所有依赖的模块，最常用的是`rules`配置项，功能是匹配对应的后缀，从而针对代码文件完成格式转换和压缩合并等指定的操作。
4. Loader：模块转换器，用于把模块原内容按照需求转换成新内容，这个是配合`Module`模块中的`rules`中的配置项来使用。
5. Plugins：扩展插件，在`Webpack`构建流程中的特定时机注入扩展逻辑来改变构建结果或做你想要的事情。(插件`API`)
6. DevServer：用于配置开发过程中使用的本机服务器配置，属于`webpack-dev-server`这个插件的配置项。

## Webpack4 搭建 Vue 项目
### 1. 项目搭建
>* 创建 webpack4-template 文件夹，进入该文件夹， npm init 初始化项目<br/>
`npm init webpack4-template`

>* 安装 webpack 四件套<br/>
`
npm i webpack webpack-cli webpack-dev-server webpack-merge --save-dev
`

>* 创建相应文件<br/>
* build
   * |--webpack.prod.js
   * |--webpack.dev.js
   * |--webpack.base.js
* dist
* src
   * |--index.js
   * |--app.vue
* index.html
```
// webpack.base.js
// 存放 dev 和 prod 通用配置
const webpack = require('webpack');
const path = require("path");
module.exports = {
  entry: './src/main.js', //入口
  module: {
    rules: []
  },
  plugins: [
    // 解决vender后面的hash每次都改变
    new webpack.HashedModuleIdsPlugin(),
  ],// 插件
};

// webpack.dev.js
// 存放 dev 配置
const merge = require('webpack-merge');
const common = require('./webpack.base.js');
const path = require('path');

module.exports = merge(common, {
  devtool: 'inline-source-map',
  devServer: { // 开发服务器
    contentBase: '../dist'
  },
  output: { // 输出
    filename: 'js/[name].[hash].js', // 每次保存 hash 都变化
    path: path.resolve(__dirname, '../dist')
  },
  module: {},
  mode: 'development',
});


// webpack.prod.js
// 存放 prod 配置
const path = require('path');
// 合并配置文件
const merge = require('webpack-merge');
const common = require('./webpack.base.js');

module.exports = merge(common, {
  module: {},
  plugins: [],
  mode: 'production',
  output: {
    filename: 'js/[name].[contenthash].js', //contenthash 若文件内容无变化，则contenthash 名称不变
    path: path.resolve(__dirname, '../dist')
  },
});
```



>* 安装vue<br/>
`npm i vue --save`<br/>
在src目录中创建入口文件main.js   app.vue
* src
   * main.js
   * app.vue

>* 安装 vue 核心解析插件<br/>
`npm i vue-loader vue-template-compiler --save-dev`
```
webpack.base.js
// vue-loader 插件
const VueLoaderPlugin = require('vue-loader/lib/plugin');
module.exports = {
  //...省略号
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader'
      }
    ]
  },
  plugins: [
    // 请确保引入这个插件来施展魔法
    new VueLoaderPlugin(),
  ]
};
```
>* 安装 html 模板解析插件<br/>
`npm i html-webpack-plugin --save-dev`
```
// webpack.base.js

// ...省略号
// html插件
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  //...省略号
  plugins: [
    //...省略号
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, '../index.html'),
    }),
  ]
};

  1. 创建 npm 命令
"scripts": {
  "start": "webpack-dev-server --hot --open --config build/webpack.dev.js",
  "build": "webpack --config build/webpack.prod.js"
},

--hot 模块热替换
--open 开启本地服务器
此时 npm start，项目可正常运行
```

### 2. 功能拓展

>* CSS loader<br/>
   *  CSS 基础 loader<br/>
    `npm i css-loader style-loader --save-dev`
   * CSS 前处理 sass 两件套
    `npm i node-sass sass-loader --save-dev`
   * CSS 后处理 postcss 两件套
    `npm i postcss-loader autoprefixer --save-dev`
    并在根文件夹创建 postcss.config.js 文件
    ```
    // postcss.config.js
        // 自动添加css兼容属性
        module.exports = {
        plugins: [
            require('autoprefixer')
        ]
        }
    ```

    ```
    // webpack.base.js
    // ...省略号
    rules: [
    {
        test: /\.(sa|sc|c)ss$/,
        use: [
        'style-loader',
        'css-loader',
        'postcss-loader',
        'sass-loader',
        ],
    },
    ]
    ```

>* 图片 loader<br />
`npm i file-loader --save-dev`
解析图片，字体等都是用 file-loader
```
// webpack.base.js

// ...省略号
rules: [
  // ...省略号
  {
    test: /\.(png|svg|jpg|gif)$/,
    use: [
      {
        loader: 'file-loader',
        options: {
          limit: 5000,
          // 分离图片至imgs文件夹
          name: "imgs/[name].[ext]",
        }
      },
    ]
  },
]
```


### 打包优化
>* 安装 clean-webpack-plugin 插件,清理dist文件目录<br/>
`npm i clean-webpack-plugin --save-dev`
```
// webpack.prod.js

// 打包之前清除文件
const CleanWebpackPlugin = require('clean-webpack-plugin');
// ...省略号
plugins: [
  new CleanWebpackPlugin(['dist/*'], {
    root: path.resolve(__dirname, '../')
  }),
]
```

>* 分离 CSS
`npm i  mini-css-extract-plugin --save-dev`
```
/ webpack.prod.js

// 分离CSS插件
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
// ...省略号
plugins: [
  new MiniCssExtractPlugin({
    filename: "css/[name].[hash].css",
    chunkFilename: 'css/[id].[hash].css'
  }),
]

另外，还需将各个 css loader中的style-loader 替换为 MiniCssExtractPlugin
图片压缩使用 image-webpack-loader, 安装后 代码如下：
// webpack.prod.js
// ...省略号
rules: [
  {
    test: /\.(sa|sc|c)ss$/,
    use: [
      {
        loader: MiniCssExtractPlugin.loader,
        options: {
          // you can specify a publicPath here
          // by default it use publicPath in webpackOptions.output
          publicPath: '../'
        }
      },
      'css-loader',
      'postcss-loader',
      'sass-loader',
    ],
  },
  {
    test: /\.less$/,
    use: [
      {
        loader: MiniCssExtractPlugin.loader,
        options: {
          // you can specify a publicPath here
          // by default it use publicPath in webpackOptions.output
          publicPath: '../'
        }
      },
      'css-loader',
      'postcss-loader',
      'less-loader',
    ],
  },
  {
    test: /\.(png|svg|jpg|gif)$/,
    use: [
      {
        loader: 'file-loader',
        options: {
          limit: 5000,
          name: "imgs/[hash].[ext]",
        }
      },
      // 图片压缩
      {
        loader: 'image-webpack-loader',
        options: {
          //   bypassOnDebug: true,
          mozjpeg: {
            progressive: true,
            quality: 65
          },
          optipng: {
            enabled: false,
          },
          pngquant: {
            quality: '65-90',
            speed: 4
          },
          gifsicle: {
            interlaced: false,
          }
        },
      },
    ]
  },
]
```

>* 压缩CSS和JS代码<br/>
`npm i  optimize-css-assets-webpack-plugin uglifyjs-webpack-plugin --save-dev`

```
// webpack.prod.js
// 压缩CSS和JS代码
// ...省略号
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");
const UglifyJsPlugin = require("uglifyjs-webpack-plugin");
module.exports = merge(common, {
  // ...省略号
  optimization: {
    // ...省略号
    minimizer: [
      // 压缩JS
      new UglifyJsPlugin({
        uglifyOptions: {
          compress: {
            warnings: false, // 去除警告
            drop_debugger: true, // 去除debugger
            drop_console: true // 去除console.log
          },
        },
        cache: true, // 开启缓存
        parallel: true, // 平行压缩
        sourceMap: false // set to true if you want JS source maps
      }),
      // 压缩css
      new OptimizeCSSAssetsPlugin({})
    ]
  },
})
```
>* 处理静态文件夹 static 复制到打包的 static 文件夹
`npm i copy-webpack-plugin --save-dev`
```
// webpack.base.js
// 压缩CSS和JS代码
// ...省略号


const CopyWebpackPlugin = require('copy-webpack-plugin');
// ...省略号
plugins: [
  // 处理静态文件夹 static 复制到打包的 static 文件夹
        new CopyWebpackPlugin([
            {
                from: path.resolve(__dirname, '../static'),
                to: 'static',
                ignore: ['.*'],
            },
        ]),
]
```
