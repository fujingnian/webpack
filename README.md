---
layout: '[post]'
title: webpack相关配置
date: 2018-08-24 18:44:23
tags: [webpack]
---
  - webpack 是前端的一个项目构建工具，它是基于 Node.js 开发出来的一个前端工具
  - 运行`npm i webpack -g`全局安装webpack
  - 在项目根目录中运行`npm i webpack --save-dev`安装到项目依赖中

  <!--more-->
## 使用webpack的配置文件简化打包时候的命令

  1. 在项目根目录中创建`webpack.config.js`
  2. 由于运行webpack命令的时候，webpack需要指定入口文件和输出文件的路径，所以，我们需要在`webpack.config.js`中配置这两个路径：
  ```
  // 导入处理路径的模块
    var path = require('path');

  // 导出一个配置对象，webpack在启动的时候，会默认来查找webpack.config.js，并读取这个文件中导出的配置对象，来进行打包处理
  module.exports = {
        entry: path.resolve(__dirname, 'src/js/main.js'), // 项目入口文件
        output: { // 配置输出选项
            path: path.resolve(__dirname, 'dist'), // 配置输出的路径
            filename: 'bundle.js' // 配置输出的文件名
        }
    }
```
## 实现webpack的实时打包构建
  `webpack-dev-server`
  - 运行`cnpm i webpack-dev-server --save-dev`安装到开发依赖
  - webpack-dev-server的相关配置
    1.package.json文件中更改`scripts`节点
    `"dev": "webpack-dev-server --contentBase src --hot --open --port 3000"`
    2.在webpack.config.js文件中配置
      ```
      const htmlWebpackPlugin = require('html-webpack-plugin')

      devServer: { 
          //  --open --port 3000 --contentBase src --hot
        open: true, // 自动打开浏览器
        port: 3000, // 设置启动时候的运行端口
        contentBase: 'src', // 指定托管的根目录
        hot: true // 启用热更新 
      },
      plugins: [ // 配置插件的节点
          new webpack.HotModuleReplacementPlugin(),  // new 一个热更新的 模块对对象
      ]
      ```
## 使用`html-webpack-plugin`插件配置启动页面
  - 由于使用`--contentBase`指令的过程比较繁琐，需要指定启动的目录，同时还需要修改index.html中script标签的src属性，所以推荐使用`html-webpack-plugin`插件配置启动页面.
  - 运行`cnpm i html-webpack-plugin --save-dev`安装到开发依赖
  - 修改`webpack.config.js`配置文件如下：
  ```
    // 导入处理路径的模块
    var path = require('path');
     // 导入自动生成HTMl文件的插件
    var htmlWebpackPlugin = require('html-webpack-plugin');

    module.exports = {
        entry: path.resolve(__dirname, 'src/js/main.js'), // 项目入口文件
        output: { // 配置输出选项
            path: path.resolve(__dirname, 'dist'), // 配置输出的路径
            filename: 'bundle.js' // 配置输出的文件名
        },
        plugins:[ // 添加plugins节点配置插件
            new htmlWebpackPlugin({
                template:path.resolve(__dirname, 'src/index.html'),//模板路径
                filename:'index.html'//自动生成的HTML文件的名称
            })
        ]
    }
  ```

## 使用webpack打包css文件
  - 使用第三方loader style-loader css-loader
  1.运行`cnpm i style-loader css-loader --save-dev`
  2.修改`webpack.config.js`配置文件：
    ```
      module: { // 用来配置第三方loader模块的
        rules: [ // 文件的匹配规则
            { test: /\.css$/, use: ['style-loader', 'css-loader'] }//处理css文件的规则
        ]
      }

    ```

  3.注意：`use`表示使用哪些模块来处理`test`所匹配到的文件；`use`中相关loader模块的调用顺序是从后向前调用的；

## 使用webpack打包less文件

  1.运行`cnpm i less-loader less -D`
  2.修改`webpack.config.js`这个配置文件：
  ```
    { test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader'] },
  ```

## 使用webpack打包sass文件
  1.运行`cnpm i sass-loader node-sass --save-dev`
  2.在`webpack.config.js`中添加处理sass文件的loader模块：  
  ```
    { test: /\.scss$/, use: ['style-loader', 'css-loader', 'sass-loader'] }
  ```

## 使用webpack处理css中的路径
  1.运行`cnpm i url-loader file-loader --save-dev`
  2.在`webpack.config.js`中添加处理url路径的loader模块：
  ```
    { test: /\.(png|jpg|gif)$/, use: 'url-loader' }
  ```
  3.可以通过`limit`指定进行base64编码的图片大小；只有小于指定字节（byte）的图片才会进行base64编码：
  ```
    { test: /\.(png|jpg|gif)$/, use: 'url-loader?limit=43960' },
  ```

## webapck.config.js 整体文件配置
  ```
    const path = require('path')

    const webpack = require('webpack')
    // 导入在内存中生成 HTML 页面的 插件
    // 只要是插件，都一定要 放到 plugins 节点中去
    
    const htmlWebpackPlugin = require('html-webpack-plugin')
      // 这个插件的两个作用：
      //  1. 自动在内存中根据指定页面生成一个内存的页面
      //  2. 自动把打包好的 bundle.js 追加到页面中去
      
      // Node 中的模块操作，向外暴露了一个 配置对象
    module.exports = {
      // 手动指定 入口 和 出口
      entry: path.join(__dirname, './src/main.js'),// 入口，需要 webpack 打包的文件
      output: { // 输出文件相关的配置
        path: path.join(__dirname, './dist'), // 指定 打包好的文件，输出到哪个目录中去
        filename: 'bundle.js' // 指定 输出的文件的名称
      },
      devServer: { // 配置 dev-server 命令参数的第二种形式，
        //  --open --port 3000 --contentBase src --hot
        open: true, // 自动打开浏览器
        port: 3000, // 设置启动时候的运行端口
        contentBase: 'src', // 指定托管的根目录
        hot: true // 启用热更新 的 第1步
      },
      plugins: [ // 配置插件的节点
        new webpack.HotModuleReplacementPlugin(), // new 一个热更新的 模块对象， 这是 启用热更新的第 3 步
        new htmlWebpackPlugin({ // 创建一个 在内存中 生成 HTML  页面的插件
          template: path.join(__dirname, './src/index.html'), // 指定 模板页面，将来会根据指定的页面路径，去生成内存中的 页面
          filename: 'index.html' // 指定生成的页面的名称
        })
      ],
      module: { // 这个节点用于配置 所有第三方模块 => 加载器 
        rules: [ // 所有第三方模块的 匹配规则
          { test: /\.css$/, use: ['style-loader', 'css-loader'] }, //  配置处理 .css 文件的第三方loader 规则
          { test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader'] }, //配置处理 .less 文件的第三方 loader 规则
          { test: /\.scss$/, use: ['style-loader', 'css-loader', 'sass-loader'] }, // 配置处理 .scss 文件的 第三方 loader 规则
        ]
      }
    }
  ```
## 使用babel处理高级JS语法
  1.运行`cnpm i babel-core babel-loader babel-plugin-transform-runtime --save-dev`安装babel的相关loader包
  2.运行`cnpm i babel-preset-es2015 babel-preset-stage-0 --save-dev`安装babel转换的语法
  3.在`webpack.config.js`中添加相关loader模块，其中需要注意的是，一定要把`node_modules`文件夹添加到排除项：
  ```
    { test: /\.js$/, use: 'babel-loader', exclude: /node_modules/ }
  ```
  4.在项目根目录中添加`.babelrc`文件，并修改这个配置文件如下：
  ```
  {
      "presets":["es2015", "stage-0"],
      "plugins":["transform-runtime"]
  }
  ```

