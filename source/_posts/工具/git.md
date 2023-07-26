---
title: git
date: 2023-05-1 17:00:46
tags: tool
---

## git 是什么

**git 是一款 代码管理，多人协作的工具**。可以思考一下，如不借助现成的流行工具，怎么进行项目代码的管理，以及多人协作？

可以想象一下最简单的模式：一人开发，先简单的搭建一个文件服务器(为了分发），项目代码放在上面，每次开发完毕后，就上传上去。如果是一个独立开发者，这或许没什么问题。现在再加入一个人，你可以约定互相划分模块，互不干扰，各干各的,很好，但我现在要提出问题了，现代软件开发模式大部分都是迭代式开发。如何做版本的记录以及版本的回退？多人协作总会有冲突，一不小心，修改了同段代码，该如何解决冲突？git 就是解决此类问题的工具.

## 我们的代码放在哪里

首先，为了我们的代码能够很好的分发出去，我们的代码是要放在一个服务器上，这个服务器可以称之为源码服务器，如大家所熟知的 github 平台提供 git 仓库让我们存放代码。以及可以自己借助 gitLab 搭建一个。再如我最近用到腾讯工蜂平台。提供代码托管的平台比较多，就不一一列出了，但提供的功能都大同小异，具体用哪个，自己喜欢就好。

## git 本地 仓库与远程仓库通信

在了解 git 仓库之间的通信之前，我们先来了解一下，ssh 通信协议。它就是一个让你在通过在本机通过这个协议与远端的机器进行通信。
前置条件：client 端生成一对公私密钥，然后把公钥放在 service 端。接下来简单说下，建立通信密验证钥过程：

1. client 端发送请求到 service 端（带公钥）
2. service 端接收到请求，与本机上的公钥比对，如一致，对公钥加密 产生一个 “密码”
3. client 端接收到 service 发送的加密公钥，然后私钥解密出，"密码"，把其发送给 service
4. service 如果一致，那么就建立链接。

参考链接如下 ：https://www.shangmayuan.com/a/7ea0a81cac984d5aa9eacb51.html

我们 本地的 git 仓库 和 远程的 git 仓库之间的通信(代码同步)，其中的一种方式就是采用 ssh 通信。

## git 中使用的术语

    stash :存储区
    branch: 分支
    conflict: 冲突
    merge :合并

## Git 的使用

接下来的部分，我会从上到下，讲讲 git 的实际使用

### 生成 ssh 密钥对

1. 生成 ssh 代码如下:

```
   ssh-keygen -t rsa -C "503181017@qq.com"
   密码可以为空
```

2. 生成的 ssh 路径 如下 Mac/Window:

   `C:\Users\用户名\.ssh`

3. 在 代码托管平台 上 添加生成的 id_rsa.pub 内容

### 本机设置用户名和账号

进入新公司，我们一般要更改电脑 git 中配置的用户名和邮箱，此为识别操作的用户。

```
    git config --global user.name "hengcheng"
    git config --global user.email "503181017@qq.com"
```

查看

```
    git config user.name
    git config user.email
```

详情请参考如下:

https://www.cnblogs.com/gudi/p/6597660.html

### git clone

上述操作，完毕后，我们就可以从 gitLab 上拉代码了，一般格式如下:

```
git clone git@github.com:zhuiye/zhuiye.github.io.git
```

### 常规操作

初始化一个 git 仓库

```
  git init
```

一般我们进行文件修改之后，我们可以使用 如下命令，把我们的文件暂存，

```
  git add .
```

当然我们也可以使用下列命令进行撤销暂存区

```
  git reset
```

对于工作区的文件，我们可以使用

```
  git checkout  --文件名
```

当然，对于以上操作，我们都可以借助 vscode 来完成，非常方便，不用使用命令来进行繁琐操作

把文件放入暂存区后，我们想对这次的更改存一次提交，我们就得使用

```
   git commit -m "注释"
```

如果要撤销这次提交，我们可以使用下列命令
，git 会生成一个新的 commit，将指定的 commit 内容从当前分支上撤除。

```
  git revert <commit id>
```

这在项目中非常有用，例如如果你在后续的版本中，发现某一次的 commit 代码有问题，这个时候就可以 revert 一下。那么这次 commit 的代码就会被剔除.

**提交到远程库**

```js
   git pull --rebase  // 把远程库上的代码，加到我们commit记录之后
   git push origin  // 代码提交至远程
```

### git stash

当我们在某个分支上进行开发的时候，突然接受到从主分支上代码的更新，我们需要合并到正在开发的分支中，这个时候，又不想 git commit 因为我们手头的工作还没完成，不想多提交一个记录，我们就可以使用
git stash 相关的命名，把这次的更新都暂时存起来。然后我们先去合并主分支，合并好之后，再 弹出 stash 中存储的内容即可.

```
   git stash save "hint"
   git stash pop
```

详情可以参考这篇文章
https://www.cnblogs.com/zndxall/archive/2018/09/04/9586088.html

### 代码审查

一般我们在功能分枝开发完之后，需要经过大佬们代码审查需要，我们在代码审查的平台创建
一个 pr 合并请求 master 分枝即可
git pull request

### 保护分枝

一般而已，我们都会把 master 分枝设置为保护分枝，只有授权的人才可以进行代码的推送，
这也是为了更好的进行代码审查。

- https://www.cnblogs.com/mingerlcm/p/12602425.html

### git patch

patch 也是补丁的意思，一般而言什么样的情况会使用到它的，就拿最近的使用场景来说，大佬代码中有一个试行的方案(并不完整）也不想开分支，就打了一个 patch 给我，让我去完善它。至于如何使用，那就请看下面的链接了.

https://blog.csdn.net/ustccw/article/details/84546873

### 本地添加远程库

```
git remote add git@github.com:zhuiyeaiden_study_record.git
默认为origin 名，当然我们也可以自定义
git remote add "name" url

删除本地远程地址
git remote rm "origin"
```

### git cherry-pick commit

当我们项目中有两个"主分支"共享一个功能时，这个功能就派上了用场，master1 master2 ,当我们在 master1 开发完后，把这次的 commit 在 master2 再次提交一遍就可以了.master2 分支中操作如下:

```
  git cherry-pick <commit id>
```

### 解决冲突

多人合作中，代码冲突是时有发生的，如何解决冲突呢？

1. 找到冲突文件，然后选择保存传入的或者是保留已有的代码
2. git add .
3. git commit -m
4. 继续后续操作...

### 对远程分支操作

删除远程分支

```
  git push -d origin branch-name
```

显示(本地)git 所知道的所有本地和远程分支。

```
git branch -a
```

若要更新远程分支的本地列表，请执行以下操作：

```
git remote update origin --prune
```

### 给版本打标签

```
  git tag -a v0.1 -m '注释'
```

本地打完标签之后，推送到远程服务器中

```
  git push origin --tags

```

## 多人协作(git workflow)

一般而言，我们并不在 master 分支上进行功能开发，多人协作，我们都会在开一个属于自己"功能分支"，创建分支，可以使用如下命令:

```
   git checkout -b function  // 创建并切换到function分支

   git checkout function  //切换到function 分支

   git branch -D function   //删除function 分支
```

当我们正在开发的途中，我们在 function 分支，如何同步 master 分支的代码呢?如下命令:

```
   master 分支进行  git pull --rebase

   切换到function分支  git rebase master
```

开发完成后，我们就得合并分支到 master 上

```
   master 分支下执行 git merger function --rebase
```

当我们不想提交一个 commit，切换分支的时候，可以使用 git stash

https://www.cnblogs.com/zndxall/archive/2018/09/04/9586088.html

## 关于 git window 下 文件 lf 与 crlf 的问题

文件设置为 lf 行尾符，在 window 环境下 ,git clone 下来却是 crlf ,原因是 git 的自动转化，我们只需要关闭它就好了。

```
  git config --global core.autocrlf false
```

## 关于 window 下 git 对文件名大小写的忽略的问题，我们需要开启

```shell
  git config core.ignorecase false
```

## git 提交规范

- chore:
- style:
- fix:
- upgrade:
- feat:

## vscode 中 git 相关推荐使用

[点击查看更多详情](https://blog.csdn.net/weixin_33275327/article/details/81485051)

## git book

https://git-scm.com/book/zh/v2

git 与其他 vcs 不同，不是增量存储，而是快照引用

```

```

## git-flight-rules

总结遇到的 git 问题列表,并给出解决方案

- https://github.com/k88hudson/git-flight-rules
