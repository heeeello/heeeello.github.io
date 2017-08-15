---
layout: post
title: single node hadoop yarn cluster setup on ubuntu 14.04
---

1. 问题描述

   需要开发hadoop的应用程序, 需要本机搭建一套开发环境.  单机环境参考了官方文档:
   - <https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html>

   但是在跑例子的时候, 发现job 一直会卡住, 无法向下进行. 
   在hadoop cluster页面(localhost:8088)查看任务为UNDEFINED状态. 
   cluster metrics中发现node有一个为unhealthy state.

   


2. 定位原因

   通过搜索相关现象, 发现类似现象解决方案: <https://stackoverflow.com/questions/29010039/yarn-unhealthy-nodes>
    通过更改yarn-site.xml中的关于yarn.nodemanager.disk-health-checker.min-healthy-disks, yarn.nodemanager.disk-health-checker.max-disk-utilization-per-disk-percentage, node变为healthy state. hadoop jar运行的命令也可以后面执行了.
    ```shell
   <property>
    <description>The minimum fraction of number of disks to be healthy for the
    nodemanager to launch new containers. This correspond to both
    yarn-nodemanager.local-dirs and yarn.nodemanager.log-dirs. i.e. If there
    are less number of healthy local-dirs (or log-dirs) available, then
    new containers will not be launched on this node.</description>
    <name>yarn.nodemanager.disk-health-checker.min-healthy-disks</name>
    <value>0.02</value>
  </property>

  <property>
    <description>The maximum percentage of disk space utilization allowed after
    which a disk is marked as bad. Values can range from 0.0 to 100.0.
    If the value is greater than or equal to 100, the nodemanager will check
    for full disk. This applies to yarn-nodemanager.local-dirs and
    yarn.nodemanager.log-dirs.</description>
    <name>yarn.nodemanager.disk-health-checker.max-disk-utilization-per-disk-percentage</name>
    <value>98.0</value>
  </property>
 
    ```

3. 常用的启动命令
  首先根据配置更改etc/hadoop/下面的xml配置文件. 
  还需要设置hadoop-env.sh中的java home
   

```shell
# Start hdfs
./sbin/hadoop-daemons.sh  start namenode
./sbin/hadoop-daemons.sh  start secondarynamenode
./sbin/hadoop-daemons.sh  start datanode

```
```
# Start yarn
sbin/yarn-daemons.sh start resourcemanager
sbin/yarn-daemons.sh start nodemanager

```
```
# .bashrc中的环境变量
export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64/
# hadoop
export HADOOP_HOME=/root/workspace/bigdata/test/hadoop-2.6.0
export HADOOP_PREFIX=$HADOOP_HOME
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
 
   ```

4. 参考：

Some googling
