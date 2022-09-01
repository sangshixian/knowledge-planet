---
title: Jenkins的安装与配置
date: 2022-09-01 14:03:38
categories:
- learn
tags:
- 软件安装
- Jenkins
---


# jenkins安装与配置
### 环境说明

- linux版本:Remote-monitoring information for session 10.0.1.137


- Host:
  Hostname: zkzn137
  Version:  Linux version 3.10.0-1160.71.1.el7.x86_64 (mockbuild@kbuilder.bsys.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-44) (GCC) ) #1 SMP Tue Jun 28 15:37:28 UTC 2022

- CPU:
  Current CPU load:  6%

- RAM:
  Total RAM:  7821 MB
  Used RAM:   2019 MB
  Cached RAM: 3141 MB
  Buffers:    0 MB

- JDK:
    -  java version "17.0.4" 2022-07-19 LTS (注意:这一环境不行,容器启动不了)
       Java(TM) SE Runtime Environment (build 17.0.4+11-LTS-179)
       Java HotSpot(TM) 64-Bit Server VM (build 17.0.4+11-LTS-179, mixed mode, sharing)

    - java version "11.0.16" 2022-07-19 LTS
      Java(TM) SE Runtime Environment 18.9 (build 11.0.16+11-LTS-199)
      Java HotSpot(TM) 64-Bit Server VM 18.9 (build 11.0.16+11-LTS-199, mixed mode)


### 安装
1. RPM方式
    * 下载命令 wget https://get.jenkins.io/redhat-stable/jenkins-2.222.1-1.1.noarch.rpm #这里可以换成阿里的镜像地址 (https://mirrors.aliyun.com/jenkins/redhat/jenkins-2.309-1.1.noarch.rpm?spm=a2c6h.25603864.0.0.6cb6fad6NNDtNr)
    * 安装命令

    ```shell
        [root@hecs-82454 local]# rpm -ivh jenkins-2.222.1-1.1.noarch.rpm 
        warning: jenkins-2.222.1-1.1.noarch.rpm: Header V4 DSA/SHA1 Signature, key ID d50582e6: NOKEY
        Preparing...                          ################################# [100%]
        Updating / installing...
        1:jenkins-2.222.1-1.1              ################################# [100%]
        [root@hecs-82454 local]#
    ```

    * 安装之后的目录如下
    ```shell
    /usr/lib/jenkins/jenkins.war WAR包
    /etc/sysconfig/jenkins 配置文件
    /var/lib/jenkins/ 默认的JENKINS_HOME目录,这个从上面的配置文件就可以看出来
    /var/log/jenkins/jenkins.log Jenkins日志文件
    ```
    * 配置项
        * 文件: /etc/sysconfig/jenkins
            * 修改端口

            ```
                ## Type:        integer(0:65535)
                ## Default:     8080
                ## ServiceRestart: jenkins
                #
                # Port Jenkins is listening on.
                # Set to -1 to disable
                #
                JENKINS_PORT="9200"
            ```

            * 修改工作目录

            ```
              JENKINS_HOME="/var/lib/jenkins"
            ```

            * jdk环境配置
            >注意:(这里JDK17不行,JDK11和JDK8可以)
            ```
            vim /etc/init.d/jenkins # 添加自定义的jdk环境(如下:/home/service/java/jdk-11.0.16/bin/java)
    
            # Search usable Java as /usr/bin/java might not point to minimal version required by Jenkins.
            # see http://www.nabble.com/guinea-pigs-wanted-----Hudson-RPM-for-RedHat-Linux-td25673707.html
            candidates="
            /etc/alternatives/java
            /usr/lib/jvm/java-1.8.0/bin/java
            /usr/lib/jvm/jre-1.8.0/bin/java
            /usr/lib/jvm/java-11.0/bin/java
            /usr/lib/jvm/jre-11.0/bin/java
            /usr/lib/jvm/java-11-openjdk-amd64
            /usr/bin/java
            /home/service/java/jdk-11.0.16/bin/java
            "
    
            # 修改以后必须使用此命令才能生效
            systemctl daemon-reload
    
    
            ```
    * 启动Jenkins，并配置服务器重启后自动启动
        ```
        systemctl start jenkins # 参数:start/stop/restart
        /sbin/chkconfig jenkins on #配置开机自启
        service jenkins status #查看运行状态(也可以通过ps -ef|grep jenkins 查看)        
        ```
    