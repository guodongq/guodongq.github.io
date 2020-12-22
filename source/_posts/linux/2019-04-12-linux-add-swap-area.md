---
layout: post
title:  "Linux new and add Swap partition!"
date:   2019-04-12 10:22:16 +0800
tags: ["linux_swap"]
published: true
comments: true
script: [post.js]
excerpted: |
    Linux new and add Swap partition ...
day_quote:
  title: The word of the day!
  description: |
    Put a very powerful message.
---
<!--more-->

**文章转载自 https://www.cnblogs.com/wuxie1989/p/5888595.html**    

 我们都知道在安装Linux系统时在分区时可以分配swap分区，而系统安装后（在运行中）如何建立或调整swap分区呢？    

 在装完Linux系统之后，建立Swap分区有两种方法。    

+ 1.新建磁盘分区作为swap分区    

+ 2.用文件作为swap分区 （操作更简单，我更常用）    
  
  下面介绍这两种方法：（都必须用root权限，操作过程应该小心谨慎。）    
  
  # 方法一：新建磁盘分区作为swap分区
  
  1.以root身份进入控制台（登录系统），输入    
  
  ```
  swapoff -a #停止所有的swap分区    
  ```
  
  2. 用fdisk命令（例：# fdisk /dev/sdb）对磁盘进行分区，添加swap分区，新建分区，在fdisk中用“t”命令将新添的分区id改为82（Linux swap类型），最后用w将操作实际写入硬盘（没用w之前的操作是无效的）。    
  
  3. mkswap /dev/sdb2 #格式化swap分区，这里的sdb2要看您加完后p命令显示的实际分区设备名    
  
  4. swapon /dev/sdb2 #启动新的swap分区    
  
  5. 为了让系统启动时能自动启用这个交换分区，可以编辑/etc/fstab,加入下面一行    
     
     ```
     /dev/sdb2 swap swap defaults 0 0    
     ```

# 方法二：用文件作为Swap分区

1.创建要作为swap分区的文件:增加1GB大小的交换分区，则命令写法如下，其中的count等于想要的块的数量（bs*count=文件大小）。    

```
dd if=/dev/zero of=/root/swapfile bs=1M count=1024    
```

2.格式化为交换分区文件:    

```
mkswap /root/swapfile #建立swap的文件系统    
```

3.启用交换分区文件:    

```
swapon /root/swapfile #启用swap文件    
```

4.使系统开机时自启用，在文件/etc/fstab中添加一行：    

```
/root/swapfile swap swap defaults 0 0    
```

 **总结：**    

- 新建和增加交换分区用到的命令为：mkswap、swapon等    
- 关闭掉某个交换分区则用“swapoff /dev/sdb2”这样的命令
