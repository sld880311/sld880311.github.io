---
title: Java并发编程之StampedLock
tags:
  - Java
  - StampedLock
  - 锁
categories:
  - [Java]
  - [并发编程]
  - [源码分析]
date: 2021-02-08 08:30:46
---
读写锁的改进。

| 锁 | 并发度 |
|----|--------|
|  ReentrantLock  |    读读互斥、读写互斥、写写互斥    |
|  ReentrantReadWriteLock  |   读读不互斥、读写互斥、写写互斥     |
|  StampedLock  |    读读不互斥、读写不互斥、写写互斥    |
<!--more-->

## 使用场景

## 乐观读的原理

