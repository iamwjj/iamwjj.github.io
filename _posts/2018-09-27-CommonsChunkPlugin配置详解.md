---
layout: post
title: "Webpack CommonsChunkPlugin配置详解"
keyword: "Webpack,webpack,CommonsChunkPlugin,commonschunkplugin,提取公共代码"
author: "Jensen"
date: 2018-09-27 22:30:00 +0800
comments: true
tags:
    - webpack
---

最近的状态有点颓，整个人都没什么干劲，能更新这篇博客也算是对自己负责了。

这两天趁需求少，研究了下`CommonsChunkPlugin`，如何更好的对项目代码进行打包优化。虽然说webpack4.x已经没有这个插件了，但是项目中用的还是3.x版本，所以还是有很有必要搞清楚这个插件到底有什么用，怎么用。

之前一直都是直接用vue-cli配置好的，所以也首先对vue-cli上的配置先解读一下。

```js
// 第一个是将入口文件中从node_modules引入的依赖提取出来，打包成vendor.js文件
new webpack.optimize.CommonsChunkPlugin({
  name: 'vendor',
  minChunks (module) {
    // any required modules inside node_modules are extracted to vendor
    return (
      module.resource &&
      /\.js$/.test(module.resource) &&
      module.resource.indexOf(
        path.join(__dirname, '../node_modules')
      ) === 0
    )
  }
}),
// 将上一个实例提取出来的vendor文件内webpack的runtime代码提取到manifest.js文件
// 否则每次修改入口文件的时候，vendor的`chunhash`都会发生改变，而vendor内的代码本身并没有改变，这样就不利于浏览器的缓存。
new webpack.optimize.CommonsChunkPlugin({
  name: 'manifest',
  minChunks: Infinity
}),
// 这个我目前还不知道有什么用（尴尬），目前的项目中也还没有用到这个。
new webpack.optimize.CommonsChunkPlugin({
  name: 'app',
  async: 'vendor-async',
  children: true,
  minChunks: 3
}),
```

解读完vue-cli上的配置，再了解一下`CommonsChunkPlugin`的options参数对象，这些其实在官方文档都有具体的解释，这里就简单的解释一下。

`options.name`: string  提取出来的公共chunk的名称，如果是已存在chunk的名字，则从source chunks中提取该chunk的代码

`options.names`: string [ ] 如果一个字符串数组被传入，这相当于插件针对每个 chunk 名被多次调用

`options.filename`: string  提取出来的文件名。一般不需要配置，自动取name的值

`options.minChunks`: number或Infinity或function(module, count) => boolean。

1. `number`：表示被chunks所引用的最小次数

2. `Infinity`：直接生成公共chunk，不包含模块，只有webpack的runtime代码

3. `function`： 有两个参数`module（object类型）`、`count（number类型）`，分别代表source chunks中引用的模块，引用的次数。`module`有两个常用的参数
	- `module.context`：该模块文件所在的目录
	- `module.resource`：该模块文件的完整路径，即`module.context` + 模块名称

`options.chunks`: 指定的source chunks（入口chunks），即指定哪些chunk作为CommonsChunkPlugin的处理对象，忽略该参数的话，则自动取项目的入口文件作为source chunks

这里对`chunk`解释一下，webpack中的chunk有以下几种：

- 入口文件（entry）也是 chunk，即 entry chunk；
- 入口文件以及它的依赖文件里通过 code split 分离出来／创建的也是 chunk，可以理解这些 chunk 为 children chunk 。
- CommonsChunkPlugin 创建的文件也是 chunk，即 commons chunk。

其他参数，我没用过也还不太懂，看官方我也没看懂，就不解释了。然后说一个**很重要的点**

**就以上面贴出的vue-cli配置的代码为例，new了三次CommonsChunkPlugin，第一个CommonsChunkPlugin会选择所有入口文件作为source chunks，minkChunks的参数`module`代表入口文件的所有依赖。第二个CommonsChunkPlugin开始，如果没有指定chunks参数，就以上一个CommonsChunkPlugin提取的chunk作为source chunks，`module`是vendor中提取出来的依赖文件**

好了，该睡觉了。又熬夜，每次白天都想着今晚早睡，到了总是有要先搞定才睡的事，命都不知道短了多少年了。

之后如果发现有漏的点再补，溜了溜了。

### 参考链接

[https://github.com/creeperyang/blog/issues/37](https://github.com/creeperyang/blog/issues/37){:target="_blank"}
