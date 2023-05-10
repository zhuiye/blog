---
title: iterm2+onmyzsh
date: 2023-05-10 17:00:46
tags: git
---

`.zshrc` ：This sets the environment for interactive shells
`.zprofile` (终端的配置文件)[https://apple.stackexchange.com/questions/388622/zsh-zprofile-zshrc-zlogin-what-goes-where]

## 前言
一个外观美的终端，会让人开发效率提升，mac 原声自带的终端太丑了，
所有就有了iterm2 

>iTerm2 is a replacement for Terminal and the successor to iTerm. It works on Macs with macOS 10.14 or newer. iTerm2 brings  the terminal into the modern age with features you never knew you always wanted.

且一般而言，太多数mac的标配就是 iterm2+ onmyzsh


## [iterm2](https://iterm2.com/index.html)
下载的方式比较简单，是一个压缩包，直接解压即可。然后可以更改字体的大小，接着我们可以选一个好看的主题。
主题列表如下：

https://github.com/ohmyzsh/ohmyzsh/wiki/External-themes

这里以 [passion主题为例](https://github.com/ChesterYue/ohmyzsh-theme-passion#install)


```
git clone repo: git clone https://github.com/ChesterYue/ohmyzsh-theme-passion;
copy theme: cp ./ohmyzsh-theme-passion/passion.zsh-theme ~/.oh-my-zsh/themes/passion.zsh-theme;
modify rc: open ~/.zshrc find ZSH_THEME edit to ZSH_THEME="passion";
execute rc: source ~./zshrc;

先把库克隆下来，然后把库下的主题文件夹，移动到我们用户目录下的 .oh-my-zsh/themes/passion.zsh-theme
然后打下.zshrc 文件，设置对应的主题名字即可。其他主题的使用也是差不多。


```
有了主题，这还源源不够，我们需要强大的插件，比如自动补全路径的插件

## (plugin)[zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)

安装页面如下：
- https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md

```
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

添加插件
```zshrc
plugins=( 
    # other plugins...
    git
    zsh-autosuggestions
)
```

然后就完成了。插件的功能还是比较强大的，下次有机会再多探索几个好用的插件...

插件大部分的安装都不太相同，我们需要一个工具进行管理，那就是 (antigen)[https://github.com/zsh-users/antigen] 

