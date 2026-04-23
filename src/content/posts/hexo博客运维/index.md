---
title: 配置hexo博客，并更换多种主题
description: 配置hexo博客，并更换多种主题
published: 2022-09-16
draft: false
tags: ["Hexo", "博客", "主题"]
category: "运维"
lang: ""
---

# hexo 博客的基本维护方式

搭建过程可参考[hexo 博客搭建教程](https://blog.csdn.net/ai_green/article/details/121675790)。

hexo 提供了便捷的构建、部署功能。在本地添加了新的 markdown 日志或者图片之后，只需执行如下代码：

```shell
# 清除已有的构建目录，默认为public
hexo clean
# 构建新的静态文件，g是generate的缩写
hexo g
# 一条命令直接部署，d是deploy的缩写
hexo d
```

就会把本地的 public 文件夹 push 到对应的 github 仓库，并将网站自动部署到 `{github用户名}.github.io`。

此外，如果想要维护本地源文件，还可以单独创建一个私有 github 仓库，用于存放 source 文件中的源文件等等。

（已弃用，请看[进阶配置](https://lulu010722.com/2022/09/18/%E5%85%B3%E4%BA%8Earcher%E4%B8%BB%E9%A2%98%E7%9A%84%E4%B8%80%E4%BA%9B%E8%BF%9B%E9%98%B6%E9%85%8D%E7%BD%AE/)）

<br>

# 配置 hexo

hexo 的默认在配置文件是根目录下的`_config.yml`，称之为**hexo 配置文件**。更改其中的属性，可以改变网页的默认文本和样式。

其中的 theme 项默认的值是 auto，我们可以更改这一个属性的值，来更换不同主题。

<br>

# 下载并配置主题

要更换主题，需要在[hexo 主题网站](https://hexo.io/themes/)选择喜欢的主题进行下载安装。

有两种方式。

**第一种（所有主题均支持）**：将主题对应的仓库克隆到`/themes/{主题名}`文件夹下。例如，对于 archer 主题，可以执行如下代码：

```shell
# --depth=1表示，仅克隆最近一次commit
git clone https://github.com/fi3ework/hexo-theme-archer.git themes/archer --depth=1
```

克隆完成后，在`archer`文件夹的配置文件`_config.yml`中进行修改。

**注意！每个主题的仓库中都有一个`_config.yml`文件，称之为主题配置文件，与根目录下的`_config.yml`文件是不同的。**

**第二种（仅部分主题支持）**：通过 npm 下载主题对应的依赖包。例如，对于 keep 主题而言，可以执行如下代码：

```shell
npm install hexo-theme-keep --save
```

下载完成后，themes 文件夹下不会出现对应的文件夹。若想对该主题进行配置，需要在 hexo 配置文件中，添加 theme_config 项，在 theme_config 属性后，添加主题配置属性。

<br>

# 配置加载原理（推测）

首先，hexo 加载根目录下的**hexo 配置文件**，若发现 theme 的值不为默认的 auto，则顺序进行如下过程：

对于**第一种**方式下载的主题：

1. 在根目录中搜索`_config.{主题名称}.yml`文件，如果找到，则继续加载该配置文件中的内容，否则，
2. 在`themes`文件夹中寻找主题名称对应的文件夹，如果找到，则加载`themes/{主题名称}/_config.yml`文件。若文件夹或者配置文件没有找到，则会报错。

对于**第二种**方式下载的主题：

在**hexo 配置文件**中寻找`theme_config`属性，并加载。

# PS：已经证实

配置文件的优先级顺序为

**根目录配置文件中的 theme_config 属性** > **\_config.{主题}.yml** > **根目录配置文件**

<br>

# 踩过的坑

1. 首先，更改了主题名称之后，**一定要清除原来的构建目录并重新构建**，也就是运行 hexo clean && hexo g。
2. 不能手动在 github 的仓库设置里面添加 custom domain，因为这样每次部署后，自定义的域名都会丢失。需要在`source/`下创建一个**CNAME**的文件，将自定义域名写入。
3. 如果添加了文件等静态资源，需要 hexo g。否则部署之后，服务器无法获得图片等静态资源文件。
