---
title: "docker在WSL2环境中的代理问题"
description: "docker在WSL2环境中的代理问题"
published: 2025-05-06
draft: false
tags:
  - "docker"
  - "wsl2"
  - "bug"
lang: ""
category:
  - "技术"
---

# 事情的起因

是，电脑最近重装了系统之后，docker一直还没安装，最近做毕设正好有需求，再加上久闻docker大名，想要成为docker糕手😍

但是，如果你：

1. 人在中国
2. 使用windows系统下的docker desktop
3. 使用WSL2而不是WSL1

那么恭喜你，你将喜提一个超~~级难debug的bug（好像是最近一个新的bug，[仓库](https://github.com/docker/for-win/issues/14706#signed-out-banner-sign-up)里有人发了关于这个问题的issue

---

# 如何复现这个bug

默认情况下，WSL2会在windows的用户目录（一般是C:/Users/<你的用户名>/）下创建一个**隐藏**的`.wslconfig`文件，这个文件中，有一个设置`networkingMode`，默认情况下可能是这样的：

```sh .wslconfig
networkingMode=mirrored
```

这个配置如果不填，则会使用NAT模式

如果是mirrored模式，那么docker无法找到正确的主机代理

此时，如果你想运行`docker pull xxx`命令，就会出现类似于下面的bug

```bash bash
Error response from daemon: Get "https://registry-1.docker.io/v2/": proxyconnect tcp: dial tcp 127.0.0.1:7890: connect: connection refused
```

我尝试过的方法有：

1. 打开clash的系统代理，并重启docker-->😢
2. 修改docker desktop界面的`Settings`->`Resources`->`Proxies`，在这里打开`Manual proxy configuration`，并将http和https的代理都设置为`http://127.0.0.1:7890`（7890是clash的默认端口）-->😢
3. `Settings`->`Docker Engine`，在右侧编辑框里加入下面的规则：
   ```json docker engine
   {
       "xxx": {},
       "yyy": "zzz",
       "proxies": [
           "httpProxy": "http://127.0.0.1:7890",
           "httpsProxy": "http://127.0.0.1:7890"
       ]
   }
   ```
   然后重启docker-->😢
4. 使用clash的TUN模式-->😢
5. 以及上述各种方式的排列组合-->😢😢😢😢

**但是其实解决方法就是，把`networkingMode`这一行的配置删了就行**

原理不太懂，但是感觉是mirrored模式下，docker的代码写死了一定会使用127.0.0.1:7890这个代理，但是WSL2环境中，127.0.0.1也就是localhost所代表的回环地址，已经不像WSL1中那样表示windows主机了，而是指的虚拟机自身的虚拟网卡，所以无法访问到主机的代理设置

---

# 话说中国docker

哎，听说是2024年年中六七月的时候，高层强行封禁了docker hub的内地访问，并且关停了很多国内的镜像源

不过，这个镜像源再禁，也总有漏网之鱼。另外，都用docker了，肯定至少有些计算机和编程基础，那还可能不知道怎么科学上网么（

所以就想着，与其找一个可能随时就会再次被封的国内镜像源，还不如直接访问docker hub

---

# 总之

不要盲目怀疑自己，**路见不平一声吼，github找issue！**
