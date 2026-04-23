---
title: CNAME的必要性
description: CNAME的必要性
published: 2023-06-27
draft: false
tags: ["GitHub Pages", "域名"]
category: "运维"
lang: ""
---

首先，如果不写`CNAME`的话，我们只能从`https://username.github.io`这个网址进行访问，但是显然如果有一个更好记忆的自定义域名会更好。

于是，我在腾讯云上买了两个域名`lulu010722.cn`和`lulu010722.com`。

之后需要在域名提供商处进行域名解析，一般是通过CNAME类型进行解析。

最后，在项目目录中也需要添加`CNAME`文件，在其中输入使用的域名。

之前一直不懂的地方就是这里，为什么腾讯云的域名已经解析到`github page`了，还是无法访问，现在推测，可能是权限问题。

在将`CNAME`文件定义好之后，我们就可以从原本地址和新购买的地址进行访问了。

不过，用原地址访问时，浏览器导航栏会自动切换为自定义地新网址。