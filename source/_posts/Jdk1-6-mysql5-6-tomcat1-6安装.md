---
title: Jdk1.6+mysql5.6+tomcat1.6安装
tags:
  - 安装部署
  - jdk
  - mysql
  - tomcat
categories:
  - 其他
date: 2021-02-07 15:34:25
---
## Jdk

### 查看java的相关信息 yum -y list java*

<div align=center>

![查看java的相关信息 yum -y list java*](Jdk1-6-mysql5-6-tomcat1-6安装/1590063041227.png)

</div>

<!--more-->

### 使用root用户安装 yum -y install java-1.6.0-openjdk*

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063063934.png)

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063070921.png)

</div>

### 查看安装情况 java -version

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063095031.png)

</div>

### 默认安装路径 /usr/lib/jvm

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063117229.png)

</div>

```conf
# vi /etc/profile
#JAVA INFO START
export JAVA_HOME=/usr/local/jdk1.8.0_111
export JRE_HOME=/usr/local/jdk1.8.0_111/jre
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
#JAVA INFO END
```

保存，退出！  

```bash
shell> source /etc/profile    #使之立即生效
```

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063195536.png)

</div>

#### 建议也设置默认启动

```conf
# vi /etc/rc.local

#JAVA INFO START
export JAVA_HOME=/usr/java/jdk1.8.0_45
export JRE_HOME=/usr/java/jdk1.8.0_45/jre
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$CLASSPATH
#JAVA INFO END
```

## tomcat

### 安装tomcat

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063283073.png)

</div>

### 启动tomcat

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063297873.png)

</div>

### 访问tomcat

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063313524.png)

</div>

### 管理tomcat

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063336994.png)

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063351495.png)

</div>

## mysql

### 环境准备

1. 查看操作系统相关信息

```bash
cat /etc/issue
uname -a
```

2. 创建需要下载rpm软件包的目录

```bash
mkdir -p /taokey/tools
```

3. 查看下是否有系统自带mysql的rpm包，如果有，需要删除自带的旧rpm包。

```bash
rpm -qa | grep mysql
```

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063466033.png)

</div>

4. yum -y remove mysql-libs-5.1*

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063488211.png)

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063494660.png)

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063507615.png)

</div>

rpm -qa | grep mysql

5. 在MySQL官网下载安装MySQL-5.6.21所需的rpm软件包

```bash
MySQL-client-5.6.21-1.rhel5.x86_64.rpm  
MySQL-devel-5.6.21-1.rhel5.x86_64.rpm  
MySQL-server-5.6.21-1.rhel5.x86_64.rpm

wget http://dev.mysql.com/Downloads/MySQL-5.6/MySQL-server-5.6.21-1.rhel5.x86_64.rpm
wget http://dev.mysql.com/Downloads/MySQL-5.6/MySQL-devel-5.6.21-1.rhel5.x86_64.rpm
wget http://dev.mysql.com/Downloads/MySQL-5.6/MySQL-client-5.6.21-1.rhel5.x86_64.rpm
```

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063560437.png)

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063565329.png)

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063572043.png)

</div>

### 安装离线包

```bash
[root@bj_db1 tools]# rpm -ivh MySQL-server-5.6.21-1.rhel5.x86_64.rpm
```

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063659593.png)

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063687872.png)

</div>

安装MySQL-server报错，原因是没有安装libaio，系统缺少libaio.so此软件包，下边yum安装一下libaio.so软件包。

```bash
[root@bj_db1 tools]# yum install -y libaio
```

```bash
rpm -ivh MySQL-client-5.6.21-1.rhel5.x86_64.rpm
```

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063754258.png)

</div>

```bash
rpm -ivh MySQL-devel-5.6.21-1.rhel5.x86_64.rpm
```

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063779874.png)

</div>

### 修改配置

```bash
cp /usr/share/mysql/my-default.cnf /etc/my.cnf
```

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063822170.png)

</div>

### 初始化root密码

```bash
[root@bj_db1 tools]# /usr/bin/mysql_install_db
```

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063871500.png)

</div>

```bash
[root@bj_db1 tools]# ps -ef | grep mysql
```

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063915297.png)

</div>

```bash
[root@bj_db1 tools]# netstat -anpt | grep 3306
```

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063943633.png)

</div>

```bash
[root@bj_db1 tools]# more /root/.mysql_secret
```

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063971034.png)

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063975827.png)

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063983400.png)

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590063991353.png)

</div>

### 开机启动

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590064022373.png)

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590064027896.png)

</div>

### 创建数据库

```sql
CREATE DATABASE IF NOT EXISTS caibaitongdb default charset utf8 COLLATE utf8_bin;
```

<div align=center>

![](Jdk1-6-mysql5-6-tomcat1-6安装/1590064060371.png)

</div>