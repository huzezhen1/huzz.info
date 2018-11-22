---
title: 使用nvm管理node版本
date: 2018-11-23 21:09:18
tags: 
- nvm
categories: 
- node
---

安装`Node.js`的最佳方式是使用`nvm`。

<!-- more -->

# 下载并安装nvm

为了方便，我们在[下载地址](https://github.com/coreybutler/nvm-windows/releases)中选择 `nvm-setup.zip`，然后解压按照正常软件安装即可。

如果是 `nvm-noinstall.zip` 版本可以[点击参考](https://blog.csdn.net/qq_41862017/article/details/82701760)。

# 使用nvm来安装和管理node版本

比如需要安装`node8.9.1`版本

```bath
nvm install 8.9.1
```

没有意外情况下就帮你安装好`node`和`npm`，我们可以查看版本

```bath
nvm list
```

切换到我们想要的`node8.9.1`版本

```bath
nvm use 8.9.1
```

比如想删除`node8.9.1`版本

```bath
nvm uninstall 8.9.1
```

# 其他情况

当时安装时因为网速问题在自动安装`npm`时一直失败，后来设置到淘宝镜像服务器就解决了。

具体操作是在`nvm`安装文件夹中找到`settings.txt`，新增了配置，最终如下：

```txt
root: C:\Users\huzez\AppData\Roaming\nvm
path: C:\Program Files\nodejs
arch: 64
proxy: none
node_mirror: http://npm.taobao.org/mirrors/node/
npm_mirror: https://npm.taobao.org/mirrors/npm/
```