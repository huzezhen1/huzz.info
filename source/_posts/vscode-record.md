---
title: 关于vscode的一些记录
date: 2018-05-07 13:48:53
categories:
- 工具
tags:
- vscode
toc: true     // 是否显示目录　　
---

对`vscode`的相关记录

<!-- more -->

# 前言

最近在试用[`vscode`](https://code.visualstudio.com/)，先把大老婆`sublime`丢到一边，记录下`vscode`一些插件及用法，当作自己备忘。

`sublime`启动的速度还是无敌，不过有ssd情况下`vscode`给我的体验也很好，不像`atom`这个仿佛开挂的卡逼。

# 操作记录

### 安装插件

在最左侧的“扩展”中搜索并安装，简单方便，快捷键是`ctrl + shift + x`

### 打开用户设置

在安装好插件后想对插件进行自定义设置

`ctrl + shift + p`， 然后搜索`ouss`（ 即`open user settings`） 或者 【文件】->【首选项】->【设置】

# 插件记录

### HTML Snippets

实用且初级的`HTML5`代码片段以及提示

但是好像新版的`vscode`自带的`emmet`已经有这个功能

### HTML CSS Support

让`HTML`标签上写`class`， 智能提示当前项目所支持的样式

### fileheader

`ctrl + shift + i`， 可在头部插入注释， 包括作者信息， 需用户配置自己的名字等

比如我自己的设置：

```json
    // setting.json，在用户设置中

    "fileheader.Author": "hu.zezhen",
    "fileheader.LastModifiedBy": "hu.zezhen"
```

### jQuery Code Snippets

编码的时候输入`jq`， 会有相关`jQuery`代码提示

不过觉得这样蛮傻的，不知道还有没有更友好点的提示

### vscode - icon

让`vscode`资源树目录加上图标

### Path Intellisense

自动补全路径，在输入路径时比较好用，比如输入`img`标签的`src`路径时

### HTMLHint

`HTML`代码检测

### Project Manager

多个项目之间快速切换

需要先`ctrl + shift + p`，搜索`Project Manager`然后选择`Save Project`

然后才会在左侧的`project`中看得到这个项目，然后可以保存多个之后就可以在里面切换

这一点`sublime`就方便多了，直接文件夹拖入左侧的`Side Bar`就行了

### Atuo Rename Tag

修改`HTML`标签，自动帮你完成尾部闭合标签的同步修改

### GitLens

丰富的`git`日志插件

打开有关联`git`远程仓库的文件夹时会在左侧中出现`GitLens`项

还能显示当前行的`commit`信息

### Git History

`git log`，会在标题页右侧有个按钮`Git: View History`

### Open HTML in Default Browser

HTML文件中，右键会显示“在浏览器中打开”，会使用默认浏览器打开当前文件

### filesize

装了之后会在左下角显示当前文件大小

### Bracket Pair Colorizer

让括号有独立颜色，便于区分，可查看安装页的详细配置，例如我自己的用户配置：

```json
    "bracketPairColorizer.forceIterationColorCycle": true,
    "bracketPairColorizer.forceUniqueOpeningColor": true,
    "bracketPairColorizer.showBracketsInGutter": true
```

### vetur

开发vue时必备，语法高亮、智能感知、`Emmet`等

### VueHelper

`vue`相关的`snippet`代码片段

### Markdown Preview Enhanced

预览`markdown`文件，右键选择`Markdown Preview`

### Import Cost

会显示`import`引入的包的大小

### Dark-Dracula

这是一个主题，目前自己用的这个

### vscode-background

可以给编辑器加背景图片，详细配置也可查看安装页的说明，我这里的配置是：

```json
    "background.enabled": true,
    "background.useDefault": false,
    "background.customImages": [
        // 支持本地图片和https开头的图片
        // 最多可分三栏，所以最多支持三张
        "https://xxx.com/cdn/ybmq.min.png",  
        "https://xxx.com/cdn/ybmq.min.png",
        "https://xxx.com/cdn/ybmq.min.png"                
    ],
    "background.style": {
        "content": "''",
        "pointer-events": "none",
        "position": "absolute",
        "z-index": "99999",
        "width": "100%",
        "height": "100%",
        "background-position": "100% 100%",
        "background-repeat": "no-repeat",
        "opacity": 0.3
    }
```
![例图](https://youss.cc/cdn/vscode-001.png)

需要注意的是安装了这个插件，`vscode`会提示安装损坏，选择“不再提示”即可，作者自己在安装页也有解释。

### Beautify

格式化代码格式，可在用户配置中配置自己的风格，例如我这里配置了缩进为2个空格

```json
    "beautify.config": {
        "indent_size": 2,
        "indent_char": " "
    }
```

### Setting Sync

上传配置 `Shift + Alt + U`

下载配置 `Shift + Alt + D`

同步vscode插件等设置用的，测试了给宿舍的电脑同步公司上的设置可用

详细配置可[点击](https://www.cnblogs.com/kenz520/p/7416836.html)参考