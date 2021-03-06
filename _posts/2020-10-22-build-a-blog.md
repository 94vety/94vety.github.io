---
title: 搭建博客
description: 从无到有，搭建博客.
tags:
- 搭建
---

## 推荐

我这里是 github 和 jekyll 搭建的博客，参考链接如下：
https://www.cnblogs.com/wxyww/p/xiaoshujiang.html

因为本人使用的是 mac，因此我是按照 mac 的安装使用方法来介绍的。

## 用到的工具

1. github desktop（个人感觉十分方便，还可以轻松查看他人的提交对比，也是比较复合我个人审美的）
官网链接：https://desktop.github.com/

## 搭建前准备

* nvm、node、npm

nvm 和 n 都是 node 版本管理工具.<br>
而在 mac 上需要先安装 nvm 再使用 nvm 命令安装相应的 node（npm 在 node 安装时便会被安装） 和 npm.<br>

nvm 的 github 地址<br>
https://github.com/nvm-sh/nvm

* nvm 安装命令

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.36.0/install.sh | bash
```

具体版本需要自己打开上面 nvm 的 github 地址查看.<br>
curl 是常用命令行工具，用来请求 web 服务器，mac 中自带的工具.<br>

* nvm 验证命令

```
command -v nvm
```

* node、npm 安装

```
nvm install v12.19.0
nvm list
```

nvm install 安装 node 的版本（我是在官网看了一下 node 的版本号）<br>
nvm list 查看 nvm 安装的 node 版本.<br>

## github

#### 申请账号

* github 官网

https://github.com/

* step1

![step1](https://s1.ax1x.com/2020/10/23/BACbD0.png)

1. 点击右上角的 sign up（注册），然后会跳到注册页面.
2. 第一行是填写注册的名字（name）.
3. 第二行是填注册的邮箱（email）.
4. 第三行是填写密码（password）.
5. 然后点击 create account.

* step2

![step2](https://s1.ax1x.com/2020/10/23/BA9om6.png)

这里是选择计划，这里有两个选择，一是创建公开仓库（repository），而是创建个人仓库（private），但创建个人需要费用。选好后，点击 continue，到此，创建完毕.

* step3

![step3](https://s1.ax1x.com/2020/10/23/BA97TO.png)

完善你的信息（tailor your experience），就是填写一些你的简历。到此，账号差不多注册完了.

#### 创建仓库

* step1

![step1](https://s1.ax1x.com/2020/10/23/BAl6Y9.png)

点击自己首页右上角的 'Your repositories' 进入我的仓库页面.
点击仓库页面中的 'New' 创建新仓库.

* step2

![step2](https://s1.ax1x.com/2020/10/23/BAl2S1.png)

1. 'Respository name' 填你的 github 名加 .github.io 这个是作为博客访问的地址.<br>
2. 下面选择 'Public' 对外公开（毕竟写博客加是为了给别人看的嘛）.<br>
3. 'Initialize this respository with:' 我选择的 'Add a README file' 这样创建的仓库中只有一个 readme 文件.<br>

![step2](https://s1.ax1x.com/2020/10/23/BAlcWR.png)

## 主题

#### 挑选主题

* jekyll 主题官网

http://jekyllthemes.org/

#### 修改主题

1. 从官网下载下来的压缩包，进行解压，就可以直接将文件传到 github 仓库搭建博客了.（将文件传上 github，去掉文件夹）.<br>
2. 如果懂得相应的代码的，其实也可以适当修改代码.<br>

* github 上传

![step2](https://s1.ax1x.com/2020/10/23/BAGR4e.png)

* 修改文件 config.yml 文件（里面有自己的基本信息）

#### 查看主题

* 然后就可以 username.github.io 查看自己的博客.

![step2](https://s1.ax1x.com/2020/10/23/BAJUqP.png)

## 参考

* 对下述文章表示感谢

https://www.cnblogs.com/wxyww/p/xiaoshujiang.html


