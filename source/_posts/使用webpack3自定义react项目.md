---
title: 使用webpack3自定义react项目
date: 2018-01-29 15:33:41
tags: webpack
categories: Others
---

1. webpakc.config.js

```webpack
var path = require('path')
var webpack = require('webpack')
var HtmlWebpackPlugin = require('html-webpack-plugin');
var OpenBrowserPlugin = require('open-browser-webpack-plugin');

module.exports = {
  entry: path.resolve(__dirname, 'app/index.jsx'),
  output: {
    filename: "bundle.js"
  },

  resolve: {
    extensions: ['.js', '.jsx']
  },

  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: 'babel-loader'
      },
      {
        test: /\.less$/,
        exclude: /node_modules/,
        use: [
          "style-loader",
          "css-loader",
          {
            loader: require.resolve('postcss-loader'),
            options: {
              // Necessary for external CSS imports to work
              // https://github.com/facebookincubator/create-react-app/issues/2677
              ident: 'postcss',
              plugins: () => [
                require('autoprefixer'),
              ],
            },
          },
          "less-loader"
        ]
      },
      {
        test: /\.css$/,
        exclude: /node_modules/,
        use: [
          require.resolve('style-loader'),
          {
            loader: require.resolve('css-loader'),
            options: {
              importLoaders: 1,
            },
          },
          {
            loader: require.resolve('postcss-loader'),
            options: {
              // Necessary for external CSS imports to work
              // https://github.com/facebookincubator/create-react-app/issues/2677
              ident: 'postcss',
              plugins: () => [
                require('autoprefixer'),
              ],
            },
          },
        ]
      },
      {
        test: /\.(png|gif|jpg|jpeg|bmp)$/i,
        use: 'url-loader?limit=5000'// 限制大小5kb
      },
      {
        test: /\.(png|woff|woff2|svg|ttf|eot)($|\?)/i,
        use: 'url-loader?limit=5000'// 限制大小小于5k
      }
    ]
  },

  plugins: [
    // html 模板插件
    new HtmlWebpackPlugin({
      template: __dirname + '/app/index.html'
    }),

    // 热加载插件
    new webpack.HotModuleReplacementPlugin(),

    // 打开浏览器
    new OpenBrowserPlugin({
      url: 'http://localhost:8080'
    }),

    // 可在业务 js 代码中使用 __DEV__ 判断是否是dev模式（dev模式下可以提示错误、测试报告等, production模式不提示）
    new webpack.DefinePlugin({
      __DEV__: JSON.stringify(JSON.parse((process.env.NODE_ENV == 'dev') || 'false'))
    })
  ],

  devServer: {
    // colors: true, //终端中输出结果为彩色
    historyApiFallback: true, //不跳转，在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html
    inline: true, //实时刷新
    hot: true  // 使用热加载插件 HotModuleReplacementPlugin
  }
}
```

2. package.js

```package.js
{
  "name": "test-demo",
  "version": "1.0.0",
  "main": "index.js",
  "author": "handsomehan",
  "license": "MIT",
  "devDependencies": {
    "autoprefixer": "^7.2.5",
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.2",
    "babel-plugin-react-transform": "^3.0.0",
    "babel-preset-env": "^1.6.1",
    "babel-preset-react": "^6.24.1",
    "css-loader": "^0.28.9",
    "file-loader": "^1.1.6",
    "html-webpack-plugin": "^2.30.1",
    "less": "^2.7.3",
    "less-loader": "^4.0.5",
    "open-browser-webpack-plugin": "^0.0.5",
    "postcss-loader": "^2.0.10",
    "react-transform-hmr": "^1.0.4",
    "style-loader": "^0.20.1",
    "url-loader": "^0.6.2",
    "webpack": "^3.10.0",
    "webpack-dev-server": "^2.11.1"
  },
  "dependencies": {
    "react": "^16.2.0",
    "react-dom": "^16.2.0"
  },
  "scripts": {
    "start": "NODE_ENV=dev webpack-dev-server --progress"
  }
}
```

3. .babelrc

```babelrc
{
  "presets": [
    "react",
    "env"
  ],
  "env": {
    "dev": {
      "plugins": [
        [
          "react-transform",
          {
            "transforms": [
              {
                "transform": "react-transform-hmr",
                "imports": [
                  "react"
                ],
                "locals": [
                  "module"
                ]
              }
            ]
          }
        ]
      ]
    }
  }
}
```