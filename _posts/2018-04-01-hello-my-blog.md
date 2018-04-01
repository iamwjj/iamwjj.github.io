---
layout:     post
title:      "Welcome to Jw Blog"
subtitle:   "Hello, My Blog"
date:       2018-04-01 00:27:20 +0800
author:     "Jensen"
header-img: "img/post-bg-life.jpg"
---



> 汗水永远不会辜负你的努力

2018.04.01，我终于有了自己的博客网页，虽然今天是愚人节，但这不是在搞笑。

在这里再次感谢[黄玄](http://huangxuan.me){:target="_blank"}大神，得益于你改造的模板，第一眼就惊艳到我，
所以才有了让我想马上搭建这个博客的动力。

我之前费了很大的力气在Jekyll的文档上面，发现最新版本的Jekyll new出来的项目，目录结构和之前版本的不一样，少了`_includes`,`_layouts`目录，增加了`Gemfile`和`Gemfile.lock`文件，这两个文件是用来管理整个项目的依赖的。默认的主题文件的`_includes`,`_layouts`会下载到你的`ruby`安装目录下面，具体路径可以使用命令`bundle show 主题名`来查看，比如查看默认主题的文件目录：

```bash
bundle show minima
# 我电脑上输出的是在 /Users/xxx/.rvm/gems/ruby-2.4.1/gems/minima-2.4.1, xxx是你的用户名
```

主题的`_includes`和`_layouts`目录默认是存放gem依赖的安装目录，不存放在当前目录，我猜测可能是为了复用，不用每个项目都安装一个额外的主题包。因为可以直接Gemfile中指定主题名字，在新版本Jekyll创建的项目的Gemfile文件中你可以找到`gem: "minima"`这一行。

现在网上的大部分 [GithubPage](https://pages.github.com) + [Jekyll](https://jekyllrb.com/) 搭建教程都是基于旧版本的目录结构，然后我暂时也不想再花费更多的时间去研究新版本如何搭建，索性就直接clone了黄玄大大的博客模板，快速搭建起来再说。新版本Jekyll如何搭建，日后有时间再去研究研究。

对于这个博客，我可能会更多地记录一下有关读书、技术、还有生活方面的东西，后面会先写一下这个博客的搭建过程和需要注意的东西。

*最后这里立个flag，希望自己每两周至少能写一篇博客，后面可能的话，最好一周一篇*。