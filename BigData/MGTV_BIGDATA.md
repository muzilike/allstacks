title: 数据收集、处理和分析经历
date: 2015-09-20 22:30:57
tags: 数据
---

## 初步了解-2015.09.20

  目前的数据团队全员30+，分三组。
  数据团队三组:
  * 数据平台组
      主要负责收集原始数据，预处理，向另两组提供数据。对视频产品提供数据记录接口，规范数据格式等等。

  * 数据产品组
      负责设计开发魔方系统前端，根据需求计算展示各项数据。

  * 数据分析组
      提出魔方等数据产品的需求。

  数据团队的工作结果，主要是提供给节目制作人员获取及时的数据反馈，灵活决策，所以实时性和历史效果数据都较为关键。
  其中部分工作结果也会提供给上层人员全局考察整站运行状态。
  数据平台组提供的原始数据较可靠，但是需要去了解其数据收集的框架和技术细节。

## 了解新老数据框架及区别-2015.09.21

  * 预热
      收集和简单了解hadoop在大数据业务上的工作方法和优缺点，为更好地了解数据框架做准备。
      参考： [为什么很多公司的大数据相关业务都基于 Hadoop 方案](http://www.zhihu.com/question/22786302)

  * 新数据框架的需求
      目前关键需求是VV和UV，新框架主要是简化后端数据计算方法，计算结果同样接入魔方前端。
      后期会将继续简化魔方上现有各个需求的计算方法。

  * 系统信息
      * Apache kafka
          分布式消息系统，相比小型消息系统而言，其支持巨大的吞吐量，基本元素也是生产者、消费者与topic。
          所谓的分布式，是指kafka可以集群部署，依赖ZooKeeper用于管理、协调每个Kafka代理。
          参考：[Apache Kafka：下一代分布式消息系统](http://www.infoq.com/cn/articles/apache-kafka)

      * MapReduce
          任务（数据）的分解和结果的汇总。
          处理过程高度抽象为两个函数：map和reduce，map负责把任务分解成多个任务，reduce负责把分解后多任务处理的结果汇总起来。
          参考：[MapReduce WordCount](http://www.cnblogs.com/xia520pi/archive/2012/05/16/2504205.html)

  * 数据系统处理流程
      基本的数据流程：
      ```
      各客户端 -> 基于flume的log server -> kafka -> strom (实时流处理)
                                \_-> HDFS -> ETL -> Hive 计算vv uv -> PG (离线方式)
      ```
      ![数据处理流程和框架](http://7xkbz1.com1.z0.glb.clouddn.com/mgo_arch.jpg)
  * 问题
      计算uv时的去重问题，不管在实时流或者离线方式中，去重都需要耗费巨大的内存，特别当时间粒度特别大的情况下。
      实时流中uv计算在时间粒度固定（而且是粒度较小时）的情况下容易处理，因为实时流可以控制时间片。

## 梳理各个流程内的接口-2015.09.22

  * 搭建本地模拟环境
      *  mac os 上安装hadoop，配置成伪分布式模式。
          参考1：[在Mac OSX Yosemite上安装Hadoop](http://www.jianshu.com/p/3aebdba32363)
          参考1：[how how to install hadoop on mac os](http://www.ifzer.com/2014/10/31/how_to_install_hadoop_on_mac_ox_x)

      *  MATRIX代码浏览
          Intellij IDEA Maven 模式导入MATRIX仓库

      *  MATRIX代码分析
          * storm 框架
              MATRIX采用了storm作为数据流处理框架，需要掌握storm编程框架。
              参考：[Storm中文版说明](http://wiki.jikexueyuan.com/project/storm/start.html)

      * 其他
          * Apache Maven 构建Java项目
              参考：[Apache Maven 入门](http://www.oracle.com/technetwork/cn/community/java/apache-maven-getting-started-1-406235-zhs.html)

## 梳理各个流程内的接口 续-2015.09.23

  * kafka-storm
      在项目的实时数据流处理中，storm接收来自kafka的实时数据，根据storm框架，即storm将有一个spout来接收kafka数据。
      storm项目提供storm-kafka类库，版本0.9.2。
      基本理清了现在storm-kafka框架内的代码逻辑，根据新的安排，梳理工作到此先暂停，转而使用新方法同步解决问题。

  * kafka-python
      新方法采用python来编写，python kafka的方式接收数据。

      * 安装kafka python模块
          pip install pykafka

      * 实现连接kafka脚本
          [connect_kafka.py](https://github.com/hdcola/dataprocess/blob/master/vvc/connect_kafka.py)

      * 实现kafka数据流切片脚本
          [split_kafka.py](https://github.com/hdcola/dataprocess/blob/master/vvc/split_kafka.py)

      * 使用方法
          ```
          python connect_kafka.py mpp_vv_pcweb | python split_kafka.py 201509232240 201509232250  -  | gzip > 201509232240.gz
          ```

      * 实现pc web端日志解析和format
          [pcp_format.py](https://github.com/hdcola/dataprocess/blob/master/vvc/pcp_format.py)

      * 使用方法
          ```
          gzcat 201509232240.gz | python pcp_format.py ./geoip > vv.log 2>err.log
          ```
      * 确定项目名
          py-dota

## py-dota部署计算vv-2015.09.24
  需要在测试节点上部署数据收集，以及定时更新最小粒度下的vv结果服务，部署panda，方便结果调试。

## py-dota部署计算vv 续-2015.09.25
  需求承接24号内容。

  * 实现5分钟粒度的单端全量VV
      在紧急需求中没有该项内容，但是需要根据该需求来积累处理方法。

  * 确认计算结果逻辑和计算中间结果文件存放路径
      每个端的所有计算都是独立的，关于最终结果需求是通过结果逻辑实现的，与计算逻辑无关，所以把整个计算过程分为计算逻辑和结果逻辑两种。

      * 计算逻辑的目录结构：
          datapath/py-dota/vv/2015/mm/端信息.yymmdd.vv.维度.时间粒度.csv
      * 计算逻辑内容结构:
          日期, 时间（维度相关），维度，VV
      * 计算逻辑的脚本分布:
          由于所有的格式化数据结构都是一致的，所以理论上各端的处理脚本都是可以通用的。
          由于各个维度的vv计算逻辑是不一致的，现在先考虑将各维度的计算逻辑也集成在同一个脚本中。
          由于5min粒度的计算逻辑与小时粒度的计算逻辑是不一致的，所以脚本是分开的，比如count_vv_5min.py，count_vv_hour.py。
          小时以上的粒度的vv计算可以使用小时粒度的计算结果，所以计算逻辑也是不一样的，所以独立脚本。
          基本上的计算脚本可能有:count_vv_5min.py count_vv_hour.py count_vv_day.py count_vv_mom.py。
          每个计算脚本都需要传入中间结果数据、维度信息，端信息。

      结果逻辑的目录结构和内容结构由产品定义决定，结果逻辑计算是独立的计算任务。

  * 确认计算任务调度逻辑
      每个端的计算任务是并行关系，所以只要先考虑一个端的计算任务调度便可。
      计算任务在每小时的半点被定时启动，假设现在为17：30，那么hour为17。
      在半点启动的任务调度：
      * 任务1
        * 开始收集hour+1 ~ hour+2之间的原始数据
      * 任务2
        * 开始清洗hour-1 ~ hour之间的原始数据
        * 开始计算hour-1 ~ hour之间的vv数据，各个维度并列
