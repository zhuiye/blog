---
title: vscode-eslint-learn
date: 2019-03-31 22:44:57
tags: eslint
---

## Visual Studio code

近期认真的学习了一下Visual studio code 下面，仅为记录学习所学

### 一些快捷键

`Ctrl+k v`  打开侧边栏预览 markdown 文件
`Ctrl+Shift+W` 关闭窗口
`Alt+Shfit+A`  块级注释
`Shift+Alt+upArrow`  向上复制一行
`Shift+Ctrl+F` 在文件中查找
`Ctrl+Shift+H` 替换

关于markDown 的语法，具体参照这篇文章[markdown的基本语法](http://zefey.com/detail.html?id=63)
***
### 一些基本的配置
文件-> 首选项-> 设置  
里面有个用户设置和工作区设置 ，["区别点击看这篇文章"]("https://zhuanlan.zhihu.com/p/54770077") 
以下仅列出一些个人所需配置 本人的setting.json 路径 "C:\Users\Administrator\AppData\Roaming\Code\User"

```json
"editor.fontSize": 18   // 用户设置中配置编辑器中显示的字体大小
"files.exclude": {
  "**/.git": true,
  "**/.svn": true,
  "**/.hg": true,
  "**/CVS": true,
  "**/.DS_Store": true
}  // 配置在工作目录中不显示如下的目录
"editor.formatOnSave": true  //保存文件格式化
"editor.suggestOnTriggerCharacters": true  //提供建议代码
"files.trimTrailingWhitespace": false   //启用后,保存文件时，剪裁尾随空格
```

### 终端

`ctrl+shift+` `  调出终端 
有四大tab
- 问题          eslint 检查文件出问题会在这里显示
- 输出          关于启用一些服务时的输入，如eslint, prettier
- 调试控制台   
- 终端  

### 常用的插件

- vscode-icons
  一款漂亮的文件图标插件
- Path Intellisense 
   智能的提示文件的路径
- EditorConfig for vs code 
   为项目配置单独的编辑设置,覆盖用户设置
- ESLint
   js代码检验工具
- prettier 
    不错的格式化代码工具

## ESlint
一个静态的检查js 代码的工具,它提供一定的规范来检查你的代码，规范可以自定义
### 基本使用
为一个项目使用eslint 首先需先安装,项目目录下执行
`npm init`   创建一个package.json
`npm install eslint --save-dev`
`.\node_modules\.bin\eslint --init`
[eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb)

我选用airbnb 的配置

`npm info "eslint-config-airbnb@latest" peerDependencies`

`npx install-peerdeps --dev eslint-config-airbnb`
直接下载所有依赖
`npm install --dev eslint-config-airbnb`
`npm install --dev eslint-plugin-import `
`npm install --dev eslint-plugin-react `
`npm install --dev eslint-plugin-jsx-a11y`

> npm install --save-dev eslint-config-airbnb eslint@^#.#.# eslint-plugin-jsx-a11y@^#.#.# eslint-plugin-import@^#.#.# eslint-plugin-react@^#.#.#

.eslinttrc.js

`extends: ["airbnb"]`




### question

***JSX not allowed in files with extension '.js'eslint(react/jsx-filename-extension)***

```js
rules: {
    "react/jsx-filename-extension": [
      1,
      {
        extensions: [".js", ".jsx"]
      }
    ]
  }
```
***
***eslint检测报错error Expected linebreaks to be 'LF' but found 'CRLF' linebreak-style***

 airbnb 规则下:linebreak-style 用的是 ["erro","unix"]; 也就是不用LF 行尾换行的话会报错

解决之道:

**1: 不校验......**

.eslinttrc.js rules规则下

```
// 统一换行符，"\n" unix(for LF) and "\r\n" for windows(CRLF)，默认unix
// off或0: 禁用规则
'linebreak-style': 'off'
```

**2:vscode  配置使用 LF 行尾换行符**

这个时候就要出现一个插件

EditorConfig for vs code 

> 此插件尝试使用.editorconfig文件中的设置覆盖用户/工作区设置，不需要其他或特定于vscode的文件，与任何EditorConfig插件一样，如果未指定root = true，EditorConfig将继续在项目外部查找.editorconfig文件。

简单的一配置文件如下: **.editorconfig**

```
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 2
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
```
## Prettier
[prettier的介绍与基本用法](https://www.jianshu.com/p/d6a69eb08f07)
非常好用的格式美化工具,经过一些配置，自动帮你美化代码，如自动插入分号..
### usage
`npm install  prettier --dev`

也可以配合 eslint 使用
`npm install --dev prettier eslint-plugin-prettier`

### Question
***prettier 自动化格式出现问题***

经过发现是vscode 里面的一个格式化插件冲突了，我有了beauty,再使用prettier 就出问题了,禁用就好了

### 配置
https://segmentfault.com/a/1190000012909159

.prettierrc
```
{
  "useTabs": false,
  "tabWidth": 2,
  "trailingComma": "es5",
  "singleQuote": true
}

```


