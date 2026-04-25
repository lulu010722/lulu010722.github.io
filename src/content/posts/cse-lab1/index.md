---
title: "cse-lab1踩坑"
description: "cse-lab1踩坑"
published: 2022-10-04
draft: false
tags:
  - "CSE"
  - "操作系统"
  - "踩坑"
lang: ""
category:
  - "课程笔记"
---

1. memcpy与strncpy。
2. get_inode需要在堆中分配内存。
3. 局部字符数组最好初始化为0。
4. ino的block不要随便free，有可能free掉别人的合法block。
