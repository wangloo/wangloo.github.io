---
title: "面试总结：地平线秋招一面"
tags: ["work"]
categories: ["Job"]
date: 2023-08-27T08:51:49+08:00
draft: true
---
- 岗位: 编译器开发工程师
- 结果: 未知

# 我的感悟
这位工程师的水平很高, 人家虽然是编译器工程师, 但是对系统软件/架构的了解相当深,
问我的问题也是相当专业. 本次面试过程也让我学到了非常多的东西.

# 回忆

## 想一个做项目中比较复杂的问题, 说一下细节

我的回答是: 关于自研OS ARMv8架构下的 bring-up流程. 但是感觉自己回答的并不好,
还是有点卡顿, 需要重新整理一下.

TODO

## ELF文件的格式包括什么?
[ELF文件格式]({{< ref "posts/binary/elf-format" >}} "ELF文件格式")

## ELF文件的segment和section的区别是什么?
[ELF文件格式]({{< ref "posts/binary/elf-format" >}} "ELF文件格式")


## 应用程序在main函数之前和return之后会发生什么?
**我的回答:**
- main之前的要为程序分配栈空间, 将argc,argv,AUX(TODO: 
这是个什么)放到ABI规定的位置
- return 之后要发送程序退出的信号, 让OS释放本程序的资源, 这是运行时库的任务

TODO: 标定正确答案

## 操作系统角度如何加载一个ELF文件?
静态加载：



## 介绍一下ARM ABI

ABI主要规定: 函数调用的行为、寄存器使用、栈布局、可执行文件布局等。

ABI是给编译器、OS看的，只有他们遵循相同的ABI，才能顺利的OS上运行编译器编译的可执行文件。
例如，编译按照ABI生成ELF，OS安装ABI的规定的方式加载ELF，才正确。

>SystemV ABI vs ABI
>
>我们有时会看到SystemV ABI的叫法，这是Unix类操作系统基于通用ABI进行了一些修改，想要APP
>在这些系统上正确运行，就必须遵循。
### ABI: PCS函数调用规定
- 基本数据类型的大小
- 寄存器的使用
  - X30==>LR, X29==>FP
  - 超过8个的部分存到栈里
  - 划分caller-saved寄存器、callee-saved寄存器
  - 返回值存入X0
  - 变长参数一般用栈来保存，因为不能得到数量

### ABI: ELF格式
- ELF文件的格式
- 静态链接、动态链接、加载的流程



## QEMU能够执行异构指令的原理是什么?

在host上运行guest代码的能力由TCG(Tiny Code Generator)模块提供，是“边翻译边执行”的思路。

TCG将翻译工作分为前后端，中间有一层“中间指令”，能够方便添加新指令的支持（现代编译器也有类似的流程，即IR）。

一次翻译过程的单位是Translation Block，通常以分支跳转、页边界来划分。

## QEMU的user mode和system mode区别是什么?
- user mode: 接收用户态可执行程序, 所有的运行时环境由QEMU提供, 
  达到的效果就像你在guest架构linux上运行一个ELF应用一样
- system mode: QEMU只提供一个模拟的硬件mechine, 所有的软件都需要
  你来提供, 适合模拟操作系统和一些baremetal程序

### QEMU user mode为什么能够直接运行一个ELF文件?

### 手撕代码: 写简单的内存分配器, 不能调用`malloc`


## 反问

### 编译器工程师这个岗位的职责大概是什么?
- 配合硬件修改编译器的前后端, 实现加速
- 适配算子?