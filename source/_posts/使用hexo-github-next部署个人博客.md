---
title: 使用hexo+github+next部署个人博客
date: 2021-02-03 15:40:46
tags:
  - Hexo
  - github
  - Next
categories:
  - 其他
---

## 环境准备

### 依赖环境

```bash
git version
node -v
npm -v
# 修改原
npm config set registry http://registry.npm.taobao.org/
# 改回去
npm config set registry https://registry.npmjs.org/
```

### 初始化博客

使用npm安装hexo([详细文档](https://hexo.io/zh-cn/))，命令是`npm install -g hexo-cli`,安装完成之后初始化博客,命令如下：

```bash
mkdir sld880311.github.io
cd sld880311.github.io
hexo init 
npm install
```
初始化完成之后目录结构如下：<!--more-->

```bash
.
├── _config.yml # 网站的配置信息，您可以在此配置大部分的参数。 
├── package.json
├── scaffolds # 模版文件夹
├── source  # 资源文件夹，除 _posts 文件，其他以下划线_开头的文件或者文件夹不会被编译打包到public文件夹
|   ├── _drafts # 草稿文件
|   └── _posts # 文章Markdowm文件 
└── themes  # 主题文件夹
```

## 常用命令

```bash
hexo s         # 启动服务，然后可以使用 http://localhost:4000访问
hexo init      #生成文档
hexo g         #生成网页
hexo clean     #清除网页
hexo d         #部署博客
hexo clean && hexo g && hexo d
```

## 特殊配置

### 配置SSH Key

获取`cat ~/.ssh/id_rsa.pub`中的数据，如果没有数据需要按照以下命令配置：

```bash
git config --global user.name "sunliaodong"
git config --global user.email "sld880311@hotmail.com"
ssh-keygen -t rsa -C 'sld880311@hotmail.com'
```

然后把生成的key添加中自己的github中即可。

### 部署到github

#### 修改根目录下的_config.yml

```yml
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: 'git'
  repo:
    github: https://github.com/sld880311/sld880311.github.io.git
  branch: master
```

#### 安装部署插件hexo-deployer-git

```bash
npm install hexo-deployer-git --save
```

#### 部署命令

```bash
hexo g -d
```

### 添加字数统计和阅读时长

#### 添加插件

```bash
npm install hexo-symbols-count-time --save
```

#### 修改根目录的_config.yml

```yml
# 文章字数统计
symbols_count_time:
  symbols: true
  time: true
  total_symbols: true
  total_time: true
```

#### 修改主题_config.yml

```bash
# Post wordcount display settings
# Dependencies: https://github.com/theme-next/hexo-symbols-count-time
symbols_count_time:
  separated_meta: true
  item_text_post: true
  item_text_total: true
  awl: 4
  wpm: 275
```

## 开始写作

```bash
hexo new "文章名称"      # 使用命令创建文章
hexo new page categories  # 定义分类
hexo new page tags        # 定义标签
hexo new page about       # 定义关于
```

## 参考文档

1. [theme-next.iissnan](http://theme-next.iissnan.com/)
2. [theme-next.js](https://theme-next.js.org/docs/)
3. [Hexo+Next搭建个人博客](https://www.jianshu.com/p/446ec02bb0a8)
4. [hexo之主题优化篇](https://zhuanlan.zhihu.com/p/185015237)
5. [超详细Hexo+Github Page搭建技术博客教程【持续更新】](https://segmentfault.com/a/1190000017986794)
6. [Hexo+NexT搭建个人博客](https://blog.csdn.net/u014786530/article/details/103548737)
7. [从头开始搭建hexo+github+hexo-theme-next主题博客（高级设置）](https://blog.csdn.net/qq_40930491/article/details/87902310)
8. 



