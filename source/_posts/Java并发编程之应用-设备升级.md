---
title: Java并发编程之应用-设备升级
tags:
  - Java
  - 线程池
categories:
  - [Java]
  - [并发编程]
  - [源码分析]
  - [Java线程池]
date: 2021-02-08 14:42:59
---
## 背景

功能需求：设备策略升级  

详细描述：用户可以不定期的创建1条策略来升级选择的多个设备从a版本到b版本  

<div align=center>

![](Java并发编程之应用-设备升级/1588229421629.png)

</div>
<!--more-->

## 解决方案

<div align=center>

![](Java并发编程之应用-设备升级/1588229450427.png)

</div>

## 参考代码

### 创建个无界带自动回收机制的线程池

`ExecutorService threadPool = Executors.newCachedThreadPool();`

### 创建策略

```java
//把每条策略要升级设备放入队列中
LinkedBlockingQueue<Integer> queue = new LinkedBlockingQueue<Integer>();
for(int j=startNum;j<endNum;j++){
    queue.offer(dev[j]);
}
StratageConsumer consumer=new StratageConsumer(queue);
```

### 每次创建策略后加入线程池

`threadPool.execute(consumer);`

### 启动策略

```java
@Override  
public void run() {  
    Integer data=null;  
    while(Thread.currentThread().isInterrupted()==false && isRunning) {  
        //System.out.println("运行线程数"+Thread.getAllStackTraces().size());  
        try {  
            //因为BlockingQueue是线程安全的，所以不用考虑同步问题  
            data = queue.take();  
            //升级处理  
            if(data!=null) {  
                iCrawcomplete.update(data);  
            }  
            if(queue.isEmpty()){  
                try {  
                    Thread.sleep(360*1000);  
                } catch (Exception e) {  }
                Thread.currentThread().interrupt();
                isRunning = false;
            }  
            Thread.sleep(60*1000);  
        }catch (Exception e) {  
            logger.info("抓取"+url+data+"出现异常"+e.getStackTrace());  
        }  
    }  
}
```
