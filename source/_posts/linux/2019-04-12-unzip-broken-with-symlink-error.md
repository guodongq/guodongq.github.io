---
layout: post
title:  "unzip error:symlink error:File name too long!"
date:   2019-04-12 10:22:16 +0800
tags: ["linux_unzip"]
published: true
comments: true
script: [post.js]
excerpted: |
    unzip error:symlink error:File name too long ...
day_quote:
  title: The word of the day!
  description: |
    Put a very powerful message.
---
<!--more-->

# 问题提出

从github上下载了kubernetes源码的压缩包kubernetes-master.zip，将其放到linux环境上进行解压，解压过程中出现symlink error: File name too long

# 问题分析

通过对出错文件的分析，事实上这些文件本来可能就是一个文本文件，现在变成了一个符号链接文件，文件名指向的“文件”就是文件内容本身，所以才会有文件名过长的错误报出。  
网上搜了一下，说是Linux下的unzip的一个bug：在OSX上解压,OSX的HFS+文件系统是不区分大小写的,而linux文件系统是区分大小写的，这就会造成文件Fuck.TXT会被fuck.txt覆盖的悲惨命运。

# 解决方案

`网上有人分析出问题出在unzip身上，并且给出了unzip 6.0版本的补丁patch，并且通知了info-zip，很不幸，unzip 6.0版本自从2009年4月20号发布以来，再未更新过。http://www.info-zip.org/UnZip.html`

## 解决方法一：搞到unzip6.0的源码，打上补丁，重新编译

```
--- a/process.c    2014-07-11 18:02:34.671611514 +0800
+++ b/process.c    2014-07-11 18:03:09.639609859 +0800
@@ -1751,6 +1751,12 @@
         = (G.crec.general_purpose_bit_flag & (1 << 11)) == (1 << 11);
 #endif

+#ifdef SYMLINKS 
+    /* Initialize the symlink flag, may be set by the platform-specific 
+       mapattr function.  */ 
+    G.pInfo->symlink = 0; 
+#endif 
+
     return PK_COOL;

 } /* end function process_cdir_file_hdr() */
```

## 解决方法二：使用替代软件

Linux下可以使用p7zip,ubuntu下的安装过程

```
sudo apt-get install p7zip-full
```

解压文件

```
7za x archive.zip
```
