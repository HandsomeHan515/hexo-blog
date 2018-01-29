---
title: 在create-react-app中使用antd的webpack配置
date: 2017-11-17 17:10:33
tags: React
categories: JavaScript
---
```
    webpack.config.dev.js(开发环境)中添加plugins: 
{
  test: /\.(js|jsx|mjs)$/,
  include: paths.appSrc,
  loader: require.resolve('babel-loader'),
  options: {
    plugins: [
			['import', { libraryName: 'antd', style: 'css' }],
		],
    // This is a feature of `babel-loader` for webpack (not Babel itself).
    // It enables caching results in ./node_modules/.cache/babel-loader/
    // directory for faster rebuilds.
    cacheDirectory: true,
  },
}
```

    webpack.config.prod.js(线上环境)中添加plugins

```
// Process JS with Babel.
{
  test: /\.(js|jsx|mjs)$/,
  include: paths.appSrc,
  loader: require.resolve('babel-loader'),
  options: {
    plugins: [
      ['import', { libraryName: 'antd', style: 'css' }],
    ],

    compact: true,
  },
},
```