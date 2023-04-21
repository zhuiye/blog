---
title: git
date: 2020-01-11 17:00:46
tags: git
---

## 前言

    我想从头好好梳理一下代码管理工具，按照我的理解

## git 究竟是什么

简言之：项目管理工具。

## 本机设置用户名和账号

进入新公司的第一步，我们一般要更改电脑 git 中配置的用户名和邮箱，此为识别操作的用户。

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

## 对于新电脑，添加 ssh 的公钥于 gitlab 账号上

1. 生成 ssh 代码如下:

```
   ssh-keygen -t rsa -C "503181017@qq.com"
   密码随后输入
```

2. 生成的 ssh 路径可能如下:

   C:\Users\Aiden\.ssh

3. 在 gitlal 的 ssh 添加生成的 id_rsa.pub 内容

## git clone

上述操作，完毕后，我们就可以从 gitLab 上拉代码了，一般格式如下:

```
git clone git@github.com:zhuiye/zhuiye.github.io.git
```

## 常规操作

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

## git stash

当我们在某个分支上进行开发的时候，突然接受到从主分支上代码的更新，我们需要合并到正在开发的分支中，这个时候，又不想 git commit 因为我们手头的工作还没完成，不想多提交一个记录，我们就可以使用
git stash 相关的命名，把这次的更新都暂时存起来。然后我们先去合并主分支，合并好之后，再 弹出 stash 中存储的内容即可.

```
   git stash save "hint"
   git stash pop
```

详情可以参考这篇文章
https://www.cnblogs.com/zndxall/archive/2018/09/04/9586088.html

## 团队合作

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

## 本地添加远程库

```
git remote add git@github.com:zhuiyeaiden_study_record.git
默认为origin 名，当然我们也可以自定义
git remote add "name" url

删除本地远程地址
git remote rm "origin"
```

## git cherry-pick commitid

当我们项目中有两个"主分支"共享一个功能时，这个功能就派上了用场，master1 master2 ,当我们在 master1 开发完后，把这次的 commit 在 master2 再次提交一遍就可以了.master2 分支中操作如下:

```
  git cherry-pick <commit id>
```

## 解决冲突

多人合作中，代码冲突是时有发生的，如何解决冲突呢？

1. 找到冲突文件，然后选择保存传入的或者是保留已有的代码
2. git add .
3. git commit -m
4. 继续后续操作...

## 对远程分支操作

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

## 给版本打标签

```
  git tag -a v0.1 -m '注释'
```

本地打完标签之后，推送到远程服务器中

```
  git push origin --tags
```

## 关于 git window 下 文件 lf 与 crlf 的问题

文件设置为 lf 行尾符，在 window 环境下 ,git clone 下来却是 crlf ,原因是 git 的自动转化，我们只需要关闭它就好了。

```
  git config --global core.autocrlf false
```

## git 提交规范

- chore:
- style:
- fix:
- upgrade:
- feat:

[点击查看更多详情](https://blog.csdn.net/weixin_33275327/article/details/81485051)
