---
title: "CUDA篇-Cuda Driver API"
date: 2024-02-10T10:17:43Z
draft: false
author: 李双双
tags:
image: /images/mathjax.png
description:
toc:
---

### 一、Driver API
CUDA Driver是GPU驱动级底层API随显卡驱动发布，CUDA Driver的头文件与动态库对应于：cuda.h 和 libcuda.so
Driver API 主要知识点是 Context 的管理机制 以及 CUDA 系列接口的开发习惯(错误检查方法), 还有内存模型。
    
参考连接：[nvcc, cuda driver,cudatoolkit,cudnn](https://www.cnblogs.com/marsggbo/p/11838823.html "Visit nvcc，cuda driver,cudatoolkit,cudnn")



