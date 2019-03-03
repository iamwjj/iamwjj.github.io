---
layout: post
title: "Git生成并配置多个SSH key"
keyword: "git, ssh key"
author: "Jensen"
date: 2018-04-21 00:07:00 +0800
header-img: ""
comments: true
tags:
    - git
---

### 场景

当你拥有多个git账号时，如何确保push代码的时候能连接到正确的账号就变得很重要。比如我有一个自己的`github`账号和公司的一个`gitlab`账号，很显然这两个账号关联的邮箱肯定是不一样的，我已经生成了一个`github`的SSH key，如果再生成一个`gitlab`的SSH key，就会覆盖之前的key，就无法连接到github了。

### 生成多个SSH key

可以通过生成多个SSH key来管理多个git。

执行` ssh-keygen -t rsa -C "mymail@gmail.com"` 生成新的SSH key。

![生成SSH key](/img/in-post/post-git-SSH-key/SSH-key.png)

这里我将新的SSH key命名为`gitlab_rsa`并确保不会覆盖我原有的key。生成了新的SSH key后就将公钥添加到gitlab上。

### 配置多个SSH key

**第一种方式：将ssh key添加到ssh-agent**

因为git会默认只读取id_rsa文件，为了让SSH识别新的私钥，需将其添加到SSH agent中。

首先打开ssh-agent：
```bash
# 如果用的是github官方的bash
ssh-agent -s
```
or
```bash
# 其他的bash
eval $(ssh-agent -s)
```

然后将SSH key的私钥添加到ssh-agent中
```bash
ssh-add ~/ssh/gitlab_rsa
```

添加之后就可以执行`ssh -T git@git.umlife.net`，看能不能访问到公司的gitlab。肯定是可以访问得到啦。

但是这种方式并不是永久的，因为ssh-agent只是把私钥添加到它所管理的一个session中，当你重启后就失效了，所以推荐使用第二种方式

**第二种方式：配置config文件**

在.ssh目录下新建一个`config`文件，不需要后缀，如果已存在就不需要新建。bash下可直接使用`touch config`创建。

然后编辑config的内容如下：

```bash
# github
# 如果连接github的ssh key的名字是id_rsa，也可以不需要配置这一段
host github.com
    hostname github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa
    User wenjianjun

# gitlab
host git.umlife.com
    hostname git.umlife.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/gitlab_rsa
    User wenjianjun
```

### 注意

有多个git账号就不要设置全局的name和email，而是对每个仓库设置对应的name和email，不然commit的信息就可能跟git账号不对应了。


