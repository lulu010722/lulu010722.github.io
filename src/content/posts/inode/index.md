---
title: inode文件系统
description: inode文件系统
published: 2022-10-02
draft: false
tags: ["文件系统", "inode", "操作系统"]
category: "课程笔记"
lang: ""
---

# 7层架构

## Block Layer

将block的序数，映射到block中实际的数据。

**块大小的权衡**

太小，则bitmap所需空间太大。

太大，则内部浪费可能比较严重。

<br>

## File Layer

```
struct inode
	integer block_nums[N]
	integer size
```

N决定了一个文件最大有多大。

与块大小的权衡一样，N的大小也存在权衡。

**解决措施**：多级inode。

inode的block_nums中的元素可以指向另外一个inode，类似于多级页表。

个人理解，在block层上又封装了一层inode的原因，就是因为inode可以做多级操作，否则就和block相同了，区别仅仅是更大。

<br>

## Inode Number Layer

将inode放在一张表里，这样可以通过index来访问某一inode。

<br>

## File Name Layer

```
struct inode
	integer block_nums[N]
	integer size
	integer type // 记录inode类型，包含常规文件，目录，和符号链接等等
```

## Path Name Layer

链接link。

可以将多个文件名映射到同一个inode。

除了.和..，不允许对目录进行链接，否则可能会出现“外存泄露”。

**重命名-1**

1. UNLINK(to_name)
2. LINK(from_name, to_name)
3. UNLINK(from_name)

但是这种实现有问题，当1和2之前出现了failure时，to_name没了，但是from_name还在。

**重命名-2**

1. LINK(from_name, to_name)
2. UNLINK(from_name)

<br>

## Absolute Path Name Layer

根目录/。其.和..都指向/。

<br>

## Symbolic Link Layer

跨存储设备。

通过文件名进行链接。

其大小指的是文件名字符串长度。

**问题**：如果通过symbolic link进入了一个目录，再运行cd ..，则会进入哪个目录？

**答案**：会进入原目录，而不是实际目录的上层目录。

因为cd ..会被bash先解析，这是一种优化。
