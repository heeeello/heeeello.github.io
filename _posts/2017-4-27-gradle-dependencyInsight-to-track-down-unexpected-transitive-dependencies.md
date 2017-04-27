---
layout: post
title: 使用gradle dependencyInsight来查找冲突的依赖包
---

1. 问题描述
   
   项目使用grails3开发， 包含了一些依赖之后，运行的时候会报告slf4j-logback和slf4j-log4j12有冲突.
但是并不知道是哪些包使用了logback, 那些包使用了slf4j-log4j12.

   可以使用如下的配置块来exclude slf4j-log4j12包的依赖

    ```shell
    configurations.all {
    //    resolutionStrategy.eachDependency { DependencyResolveDetails details ->
    //        if (details.requested.name == 'log4j') {
    //            details.useTarget "org.slf4j:log4j-over-slf4j:1.7.21"
    //        }
    //        if (details.requested.name == 'commons-logging') {
    //            details.useTarget "org.slf4j:jcl-over-slf4j:1.7.21"
    //        }
    //    }

       exclude group: 'org.slf4j', module: 'slf4j-log4j12'
    }
    ```

   这样在整个工程中都会排除掉slf4j-log4j12包。

   如果想要查找这个log包是被哪个项目中配置的依赖包含进来的，怎么办？


2. 使用gradle dependencies
   
   可以使用gradle dependencies，将所有依赖重定向到一个文件中，然后查找. 这样会发现很多slf4j的依赖，比较难找

    ```shell
    ./gradlew dependencies >aa.txt 2>&1
    
    ```

3. 使用gradle dependencyInsight， 反过来查找transitive dependency对应的项目中的最顶层的依赖配置项
   
   这样就很清楚的可以查看冲突的传递包被哪些顶层依赖包包含了。 可以有选择的exclude掉

    ```shell
    ./gradlew dependencyInsight --dependency slf4j-log4j12
    :api-portal:dependencyInsight
    org.slf4j:slf4j-log4j12:1.7.22 (selected by rule)

    org.slf4j:slf4j-log4j12:1.6.1 -> 1.7.22
    +--- com.101tec:zkclient:0.7
    |    \--- org.apache.kafka:kafka_2.11:0.9.0.1
    |         +--- compile
    |         +--- project :common
    |         |    \--- compile
    |         \--- project :myplugin
    |              \--- compile
    \--- org.apache.zookeeper:zookeeper:3.4.6
         +--- org.apache.kafka:kafka_2.11:0.9.0.1 (*)
         \--- com.101tec:zkclient:0.7 (*)

    org.slf4j:slf4j-log4j12:1.7.6 -> 1.7.22
    \--- org.apache.kafka:kafka_2.11:0.9.0.1
         +--- compile
         +--- project :common
         |    \--- compile
         \--- project :myplugin
              \--- compile

    (*) - dependencies omitted (listed previously)

    BUILD SUCCESSFUL

    Total time: 3.183 secs

    ```
    ```
    compile (group: 'org.apache.kafka', name: 'kafka_2.11', version: '0.9.0.1') {
        exclude group: 'org.slf4j', module: 'slf4j-log4j12'
    }
    or when appropriate, do it globally for all configurations:

    configurations {
        all*.exclude group: 'org.slf4j', module: 'slf4j-log4j12'
    }
    ```

4. 参考：

- https://solidsoft.wordpress.com/2014/11/19/gradle-tricks-tracking-down-not-expected-transitive-dependencies/
- https://solidsoft.wordpress.com/2014/11/13/gradle-tricks-display-dependencies-for-all-subprojects-in-multi-project-build/
