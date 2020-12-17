---
layout: post
title:  "Goroutine Synchronized!"
date:   2018-12-02 10:22:16 +0800
tags: ["goroutine"]
published: true
comments: true
script: [post.js]
excerpted: |
    golang中通常有一些任务处理，需要主线程等待子线程的结果之后，再来进行处理。比如发送若干个http请求，主线程需要等待各个子线程的结果，之后再做关联处理 ...
day_quote:
  title: The word of the day!
  description: |
    Put a very powerful message.
---


# 方法

## 1，time.sleep()

```
func main(){
 var buf []byte
 var err error
 //发送HTTP请求
 go func(){
 buf, err = http.Get(&quot;http://localhost:8080/api&quot;)
 }()
 //主线程sleep 5s，等待上面的goroutine发送http请求结束
 time.Sleep(5e9)
}
```



## 2，使用channel机制

每个goroutine传一个channel进去然后往里写数据，在再主线程中读取这些channel，直到全部读到数据了子goroutine也就全部运行完了，那么主goroutine也就可以结束了。这种模式是子线程去通知主线程结束。
```
func main(){
  //生产者/消费者使用的channel
  var proChan = make(chan int)
  //主线程channel
  var mainChan = make(chan int)

  //生产者
  go func(){
    for i := 0; i < 20; i++ {
            proChan <- i
            fmt.Println("生产者写入数据", i)
        }
    close(proChan)
  }()

  //消费者1
  go func(){
     for v := range proChan {
            fmt.Println("\t消费者读出数据", v)

  }()

  //消费者2
  go func(){
     for v := range proChan {
            fmt.Println("\t消费者读出数据", v)

  }()

  <- mainChan
  <- mainChan
  //主线程通过mainChan读出数据进行阻塞，达到线程同步的目的  
}
```

## 3，使用context中cancel函数

主线程去通知子线程结束
```
func main(){
    ctx, cancel := context.WithTimeout(context.Background(), 2 * time.Second)
    defer cancel()
    wg.Add(1)
    go work(ctx)
    wg.Wait()
    fmt.Println("Finished")
}

func work(ctx context.Context) {
    tr := &http.Transport{}
    client := &http.Client{Transport: tr}
    defer wg.Done()
    c := make(chan ResPack, 1)

}
```

## 4，sync.WaitGroup模式

Add方法设置等待子goroutine的数量，使用Done方法设置等待子goroutine的数量减1，当等待的数量等于0时，Wait函数返回
```
func download(i int,wg *sync.WaitGroup){
  defer wg.Done
  //下载照片的逻辑代码
}

func main(){
  var wg = &sync.WaitGroup{}
  //创建多个协程，同时下载多个图片
  for i :=1; i<24; i++ {  
       //计数器+1  
       wg.Add(1)
       //多个协程去下载照片,在download方法中需要调用wg.Done()让计数器减1
       go download(i， wg)  
    }

   //等待所有协程操作完成  
    waitGroup.Wait()  
}
```