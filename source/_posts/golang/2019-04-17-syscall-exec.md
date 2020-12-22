---
layout: post
title:  "Syscall.Exec!"
date:   2019-04-17 10:22:16 +0800
tags: ["syscall.Exec","golang"]
published: true
comments: true
script: [post.js]
excerpted: |
    golang syscall.Exec ...
day_quote:
  title: The word of the day!
  description: |
    Put a very powerful message.
---

<!--more-->

<strong style="color:green">文章转载自： https://www.jianshu.com/p/e1de8fc52718</strong>

# 背景
在docker的docker-entrypoint.sh脚本中我们经常会看到有如下的一句话
```
exec $@
```
**Q:** 在shell脚本中exec有什么作用？   
**A:** exec会执行参数指定的命令，但是并不创建新的进程，只在当前进程空间内执行，即替换当前进程的执行内容，他们重用同一个进程号PID.

# golang中Exec
go语言syscall.Exec完成和shell中同样的功能

请看如下代码：
```
executablePath, err := exec.LookPath("ls")
if err != nil{
  panic(err)
}

args := []string{"ls", "-l"}
env := os.Environ()

if err := syscall.Exec(executablePath, args, env); err != nil {
        panic(err)
}
```
syscall.Exec需要三个参数：
- 第一个参数是可执行文件的路径，注意不会自动从PATH下面去搜索
  - 要么是显式的指定全路径：/path/to/executable
  - 要么是显式的指定相对路径: ./relpath/to/executable
  - 要么通过exec.LookPath从PATH里面搜索出来,参考本例
- 第二个参数是参数列表
  - 注意args[0]是可执行程序名，这个内容会显示在ps -ef的输出中。用户可以改这个值，例如明明执行的是/usr/bin/sleep的可执行程序，但是这里可以改成任意字符串，例如ls，这样用户在ps -ef查看到的就是ls的命令在运行，而不是sleep命令，混淆用户。
  - 后面是正常的参数。
- 第三个参数是环境变量
  - 如果没有传，那么不会自动继承caller的环境变量的。
  
<strong style="color:red">syscall.Exec只能是main函数的最后一条指令，它后面的代码不会被执行到</strong>
