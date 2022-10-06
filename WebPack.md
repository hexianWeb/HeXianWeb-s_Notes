# webpack入门

## webpack安装

1. 不能编译打包 css、img 等文件。
2. 不能将 js 的 es6 基本语法转化为 es5 以下语法。

```bash
npm install webpack webpack-cli -g
npm install webpack webpack-cli -D
```



## Webpack 开发环境的基本配置

```js
// Node 版本过高需要在其中加入設定 hash 的程式碼，
const path = require("path");
const crypto = require("crypto");
const crypto_orig_createHash = crypto.createHash;
crypto.createHash = (algorithm) =>
  crypto_orig_createHash(algorithm == "md4" ? "sha256" : algorithm);

// 引入 HTML打包工具 html-webpack-plugin
const HtmlWebpackPlugin = require("html-webpack-plugin");

// 引入文件复制打包工具
const CopyPlugin = require("copy-webpack-plugin");


module.exports = {
  // 设定开发环境
  mode: "development",
  // 配置入口文件
  entry: {
    app: "./src/app.js",
  },
  // 配置出口文件
  output: {
    path: path.join(__dirname, "./dist"),
    filename: "app.js",
  },
  // loader的配置
  module: {
    rules: [
      // 详细 loader 配置
      // 不同文件必须配置不同 loader 处理
      {
        // 匹配哪些文件
        test: /\.css$/,
        // 使用哪些 loader 进行处理
        use: [
          // use 数组中 loader 执行顺序：从右到左，从下到上 依次执行
          // 创建 style 标签，将 js 中的样式资源插入进行，添加到 head 中生效
          "style-loader
          // 将 css 文件变成 commonjs 模块加载 js 中，里面内容是样式字符串
          "css-loader",
        ],
      },
      {
        test: /\.less$/,
        use: [
          "style-loader",
          "css-loader",
          // 将 less 文件编译成 css 文件
          // 需要下载 less-loader 和 less
          "less-loader",
        ],
      },
      {
        test: /\.(jpg|png|gif)$/,
        // 使用一个 loader
        // 下载 url-loader file-loader
        loader: "url-loader",
        options: {
          // 图片大小小于 8kb，就会被 base64 处理
          // 优点: 减少请求数量（减轻服务器压力）
          // 缺点：图片体积会更大（文件请求速度更慢）
          limit: 8 * 1024,
          // 问题：
          // 因为 url-loader 默认使用 es6 模块化解析，
          // 而 html-loader 引入图片是 commonjs
          // 解析时会出问题：[object Module]
          // 解决：关闭 url-loader 的 es6 模块化，使用 commonjs 解析
          esModule: false,
          // 给图片进行重命名
          // [hash:10]取图片的 hash 的前 10 位
          // [ext]取文件原来扩展名
          name: "[hash:10].[ext]",
        },
      },
      {
        test: /\.html$/,
        // 处理 html 文件的 img 图片（负责引入 img，从而能被 url-loader 进行处理）
        loader: "html-loader",
      },
      // 打包其他资源(除了 html/js/css 资源以外的资源)
      {
        // 排除 css/js/html 资源
        exclude: /\.(css|js|html|less)$/,
        loader: "file-loader",
        options: {
          name: "[hash:10].[ext]",
        },
      },
    ],
  },
  // 配置插件
  plugins: [
    // HTML 打包插件
    // html-webpack-plugin
    // 功能：默认会创建一个空的 HTML，自动引入打包输出的所有资源（JS/CSS）
    // 需求：需要有结构的 HTML 文件
    new HtmlWebpackPlugin({ template: "./src/index.html" }),
    // 文件资源打包插件
    new CopyPlugin({
      patterns: [
        {
          from: path.join(__dirname, "./public/Bt.png"),
          to: "./img",
        },
      ],
    }),
  ],

  devServer: {
    contentBase: path.join(__dirname, "./dist"),
    compress: true,
    port: 8080,
  },
};

```

> Do not use `style-loader` and `file-loader` together, use `style-loader` for browsers target and `file-loader` for node target!!!

**这是我的可用的Webpack版本**

```json
 "devDependencies": {
    "copy-webpack-plugin": "^6.4.1",
    "css-loader": "^5.2.7",
    "file-loader": "^6.2.0",
    "html-loader": "^1.3.0",
    "html-webpack-plugin": "^5.5.0",
    "less-loader": "^10.2.0",
    "style-loader": "^2.0.0",
    "url-loader": "^4.1.1",
    "webpack": "^5.70.0",
    "webpack-cli": "^4.9.2",
    "webpack-dev-server": "^4.7.4"
  },
```



## 打包样式资源



