---
title: "cse-lab2a踩坑"
description: "cse-lab2a踩坑"
published: 2022-10-29
draft: false
tags:
  - "CSE"
  - "C++"
  - "踩坑"
lang: ""
category:
  - "课程笔记"
---

1. `ifstream::eof()`有bug。

   如果有一个文件`test.txt`，文件内容是：

   ```
   1 a
   2 ab
   3 abc

   ```

   （注意，最后有一个空行）

   则，当时用下面的代码时，会有问题。

   ```c++
   int main() {
       ifstream in("test.txt");
       while (!in.eof()) {
          	int length;
           in >> length;
           char tmp[length + 1];
           in.read(tmp, length);
           cout << tmp << endl;
       }
   }
   ```

   原因在于，第三行读取完成后，不管是否将最后的换行符进行了消除，`eof()`会仍然返回false，因为只有当读到最后一个字符并尝试继续读的时候，该函数才会返回true。
