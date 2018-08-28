---
layout: post
title: "Webpack DllPlugin 使用配置"
author: "Jensen"
date: 2018-08-28 14:40:00 +0800
comments: true
tags: 
    - webpack
---

说来惭愧，更新上一篇博客已经是一个月之前了，说好的每个月至少要写两篇博客。。。废话不多说，今天记录一下webpack的 DllPlugin 配置。

前不久在公司写的新后台项目里，引入了公司内部的ui库（跟element ui差不多），体积比较大，再加上其他的依赖，打包后app.js和common.js的体积都比较大，于是就思考如何再优化一下打包体积。

#### 为什么要使用DllPlugin

在使用webpack构建项目的时候，会希望自己写的代码和第三方库的代码分离开，webpack官方也推荐使用`CommonsChunkPlugin`*（从webpack 4 开始，`CommonsChunkPlugin` 被移除，取而代之的是`SplitChunksPlugin`）*来分离第三方库，但是使用`CommonsChunkPlugin`会有一个问题： 

**每次构建的时候都要重新打包！！ 而一般来说，第三方库的代码是很少变动的，除了更新版本。** 

这无疑也增加了webpack每次的编译时间，这时候就需要DllPlugin了。`DLLPlugin` 需要配合 `DLLReferencePlugin`使用，先说`DLLPlugin`

#### DllPlugin 配置

> 这个插件是在一个额外的独立的 webpack 设置中创建一个只有 dll 的 bundle(dll-only-bundle)。 这个插件会生成一个名为 manifest.json 的文件，这个文件是用来让 DLLReferencePlugin 映射到相关的依赖上去的

新建一个`webpack.dll.conf.js`配置文件:

```js
const webpack = require('webpack')
const path = require('path')

// 需要独立打包的第三方库，自行添加
let vendors = [
    'vue/dist/vue.esm.js', 
    'vue-router/dist/vue-router.esm.js', 
    'vuex/dist/vuex.esm.js'
    ]

module.exports = {
  entry:{
    vendor: vendors
  },

  output:{
    path: path.resolve(__dirname, '../static/vendor'),      // 路径
    filename: '[name].[hash].js',   // 文件名
    library: '[name]'        //  与打包后的文件的返回值绑定
  },

  plugins:[
    new webpack.DllPlugin({
      context: __dirname,       // 可选参数，manifest 文件中请求的上下文(默认值为 webpack 的上下文)
      path: path.join(__dirname, '../static/vendor', '[name]-manifest.json'),   // manifest.json 文件的路径
      name: '[name]'     // 暴露出来的Dll函数名，必须跟output.library保持一致
    }),
  ]
}

```
然后用webpack执行这个文件，就可以生成一个包含第三库代码的vendor.(hash).js。为了方便，在`package.json`添加一行script：
```json5
{
    "script": {
      "dll": "webpack --config ./build/dll.conf.js"
    }
}
```
执行`npm run dll`

到这里，没什么报错信息的话，就会顺利生成`vendor.js`和`manifest.json`文件。 当然为了减小`vendor.js`的体积，可以使用`UglifyJSPlugin`插件来压缩一下代码。

#### DllReferencePlugin 配置

> 这个插件是在 webpack 主配置文件中设置的， 这个插件把只有 dll 的 bundle(们)(dll-only-bundle(s)) 引用到需要的预编译的依赖。

如果是使用vue-cli生成的项目，在`webpack.prod.conf.js`里配置就好了。在plugins下加上该插件

```js
new webpack.DllReferencePlugin({
  context:__dirname,    // manifest中请求的上下文（即依赖的相对路径）
  manifest: require('../static/vendor/vendor-manifest.json')
})
```

其他参数一般都不需要配置，有特殊情况的就根据官方文档配置。

**最后的最后，别忘了在`index.html`将`vendor.js`作为script引入**

#### 可改进的地方

可以看到`vendor.js`文件名加上了hash，之后更新了依赖库的代码或者增加了其他的依赖库，文件名就会改变，然后又得重新修改`index.html`文件，这也太麻烦了吧。

别担心，用`HtmlWebpackIncludeAssetsPlugin`插件就可以帮我们搞定这个问题了。

```js
// 将抽离出来的第三方包插入到dist文件夹下的index.html
new HtmlWebpackIncludeAssetsPlugin({
  assets:[{
    // 需要插入的文件的path
    path: 'static/vendor',
    // 匹配需要插入的文件
    glob: '*.js',
    // 指定glob属性应该搜索匹配的path
    globPath: path.resolve(__dirname, '../static/vendor'),
    // 被插入的文件
    files: path.resolve(__dirname, '../dist/index.html'),
  }],
  // 是否先于其他js文件之前注入 false: 先于其他js之前插入  true: 之后插入
  append:false
})
```
OK，大功告成。这里`path`和`globPath`好像都是必须的，还没太具体搞懂为什么。。不影响使用就先没管它了

这篇博客是下午上班写的，因为我那一大波需求还没确定好。明天开始就要被需求压扁了，按照之前的flag，这个月还有一篇博客要写，溜了溜了。
