---
title: git——人类好朋友
description: git——人类好朋友
published: 2022-10-16
draft: false
tags: ["git", "GitHub"]
category: "编程"
lang: ""
---

# 本地/远程分支创建和删除

```shell
# 删除本地分支
git branch -d <branch-name>
# 删除远程分支
git push -d <remote-name> <branch-name>
# 删除已经不存在于远程仓库的本地远程分支
git remote prune <remote-name>
```

# git submodule初步运用

实际场景：在博客主仓库中日常更新日志内容的同时，想要对主题仓库进行个性化修改，或者更进一步，对原主题仓库提出PR，进行contribute。主仓库需要拥有子仓库的文件内容，因为前者要根据后者的资源进行页面的渲染。

这样，我们就需要在主仓库（blog）中包含子仓库（themes\archer）。为了更方便地管理他们，需要使用git submodule。

## 原来的做法

将themes\archer中的.git文件夹删除，使其失去版本控制，并将themes\archer中的所有文件（.gitignore的除外）加入主仓库的版本控制。在本地的修改可以直接更新到主仓库，在github直接执行npm run build的CI任务即可。

**但是**，这样有很多问题。

1. 如果遇到明显的bug，只能在本地修改，无法对原主题仓库进行贡献。
2. 原主题仓库更新后，本地无法通过git快捷的更新。

## 使用git submodule

git对于子模块的管理有如下几个关键点。

- 首先，.git目录中会有一个modules目录，里面记录了所有子模块的版本控制信息。
- 其次，在主仓库下，会创建一个.gitmodules文件，里面以条目的形式记录了子仓库名称，本地相对地址，以及远程仓库地址。
- 最后，git会生成一个**非常特殊**的文件，他的文件模式是160000，与一般的100644文件不同。它在windows的资源管理器下并不可见。他被用于覆盖子模块真实的目录内容，仅仅以子模块的当前所在记录的hash值，存入该文件来代替。每当子模块更新，该文件也会更新。

创建流程是：

```shell
# 如果是第一次创建子模块，则项目中会出现两个新文件：.gitmodules和以relative-path为名的特殊文件。同时，该命令会将子模块克隆到本地的relative-path目录下。
$ git submodule add <url(https://github.com/username/submodule-repo.git)> <relative-path>

# 在主仓库，将更改提交并push到远程
$ git add --all
$ git commit -m <msg>
$ git push origin master
```

此时，在主仓库中，relative-path对应的地方会出现一个链接，点击可以跳转到子模块的仓库。

这就意味着，主仓库是没有子仓库的内容的，只保留了一个链接。

想要获得子模块的内容，需要在workflow file里面加入新的CI任务。

```yaml
# 注意，该任务需要放在build之前
name: Fetch submodule
run: |
	git submodule sync
	git submodule update --init --recursive
```

一般情况下，主仓库会获得子仓库的内容，并根据子仓库的内容build博客。

## 权限问题

如果使用了ssh的仓库地址，则可能会出现权限问题。

由于我的子仓库是公有仓库，所以简单的解决办法是，使用https的仓库地址。

## 与github actions/checkout@v3集成

github仓库服务器每次运行CI/CD任务，其实都需要将主仓库克隆到本地。这是通过复用`actions/checkout@v3`实现的。而该action同时提供了对子模块的支持。只需要进行如下配置即可。

```yaml
- name: Checkout
  uses: actions/checkout@v3
  with:
    submodules: true
```

该配置还能自动在没有提供ssh-key的情况下，将`.gitmodules`中由ssh协议指定的url转换成https来使用。
