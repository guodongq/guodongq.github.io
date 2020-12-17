---
layout: post
title:  "Deduplicate slice elements!"
date:   2019-07-21 10:22:16 +0800
tags: ["duplicate_slice"]
published: true
comments: true
script: [post.js]
excerpted: |
    How to deduplicate golang slice elements ...
day_quote:
  title: The word of the day!
  description: |
    Put a very powerful message.
---


开发过程中，我们经常会遇到对切片中的数据进行加工，如何很优雅的对切片中的元素进行去重，本文通过代码的实例告诉大家优雅的对切片进行去重处理。

# 背景
现有一个字符串的切片，如何将该切片中的元素进行去重。


# 代码逻辑实现(参考k8s， clientcmd包中的deduplicate方法)
```
func deduplicate(s []string) []string{
  encountered := map[string]bool{}
  ret := make([]string,0)
  for i := rang s{
    if encountered[s[i]] {
      continue
    }
    encountered[s[i]] = true
    ret = append(ret, s[i])
  }
  return ret
}
```
