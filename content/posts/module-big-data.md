---
title: COMP5111M Big Data Systems
date: 2020-06-04T21:00:00+08:00
published: true
slug: module-big-data
tags:
- big data
cover_image: "./images/module-big-data.png"
canonical_url: false
description: 对「大数据」这门课程学习过的知识进行总结。
---

:::note ℹ️ Module Introduction

**Big Data** is the name of one of the largest and most profound movements in modern computing and business. It refers to the huge volumes of data being generated in modern life, and the challenges, risks, and rewards of storing and extracting meaningful information from this data.

:::

## 1. Definitions

Doug Laney first defined 3 characteristics of Big Data (Three V's) in 2001.

![](https://i.loli.net/2021/01/07/2SrqzOnRk98ZvV7.png)

* **Volume** means that many different factors can contribute to the increase in data volume.
* **Data velocity** is both the speed at which data streams in, and the timely manner in which data must be dealt with to maintain time based relevance.
* **Variety**: Data comes in all kinds of formats, but can be grouped into two types: **structured** and **unstructured**.
  * **Structured data** is the numeric data in traditional databases. Created from line-of-business and pre-formatted data collected over time.
  * **Unstructured data** is the relational and seemingly unrelated data that comes from unstructured sources (social media, text documents, etc.).

Although the three V's have traditionally been used to define Big Data, increasingly extra Vs have been proposed. However, these typically describe characteristics rather than being deinitional.

![](https://i.loli.net/2021/01/07/Ri5qSWl4Hd2AzYh.png)

Therefore, The Five V's:

* Volume    容量
* Velocity  速率
* Variety   多样性
* Veracity  准确性
* Value 值

## 2. MapReduce

MapReduce is

* Programming model for expressing distributed computation in massive-scale systems.
* Execution framework for organizaing and performing computation.
* Originally developed by Google.
* Open-source implementation called Hadoop.

GFS(Google File System) for Google's MapReduce, and HDFS(Hadoop Distributed File System) for Hadoop.

### Process

1. 遍历大量的记录
2. 提取感兴趣的东西
3. 随机排序和整理实时的结果
4. 将中间结果汇总为可用的内容
5. 生成最终的输出

其最重要的两个步骤就是一个是`parition`，一个是`combine`，即「分离」和「联合」。

Counting the number of occurrences of each word in a large collection of documents.

The **map** function emits each word *w* plus an associated count of occurrences.

```
map(String key, String value):
    // key: document name
    // value: document contents
    for each word w in value:
        EmitIntermediate(w, "1");
```

The **reduce** function sums together all counts emitted for a particular word.

```
reduce(String key, Iterator values):
    // key: a word
    // values: a list of counts
    int result = 0;
    for each v in values:
        result += ParseInt(v);
    Emit(AsString(result));
```

### MapReduce in Hadoop

客户端提交 MapReduce 到 Job Tracker，Job Tracker 查询 NameNode，哪些 DataNodes 有文件夹。Job Tracker 让 Task Tracker 运行在这些节点之上，代码在本地数据上执行 Map 运算。Task Tracker 启动 Map 服务，并监视进度。

Job Tracker 会始终尝试为 Map 任务选择具有本地数据的节点，NameNode 指示节点会从相关的 DataNode 复制数据。最后，Job Tracker 会执行 Reduce Task。

所以，一个完整的执行步骤就是：

$$Input \rightarrow Splitting \rightarrow Mapping \rightarrow Shuffling \rightarrow Reducing \rightarrow Final Result$$

![](https://i.loli.net/2021/01/07/9janJXCMkwuEoWc.png)

## 3. Hadoop

Hadoop 的第一个核心版本就是 HDFS 和 MapReduce，HDFS 是 Hadoop 的第一层面，MapReduce 是 Hadoop 的第二层面。

### HDFS

HDFS was inspired by the GFS(Google File System), published in 2003. It has a **master/slave** architecture.

![](https://i.loli.net/2021/01/07/DvUSRMtZpfme9gQ.png)

An HDFS cluster consists of **a single NameNode**, additionally, there are a number of **DataNodes**.

也几乎是说，一个 NameNode 会有很多个 DataNode，在内部，一个文件会被分成一个或者很多个块，这些块会被存储进 DataNode 集合中。这样的好处就是，一个 DataNode 的损坏不要紧，其它的 DataNodes 仍然能够自动保存数据。

### YARN

Hadoop 2 moves from a restricted batch-oriented model to **more interactive and specialized processing models**. The biggest changes in Hadoop 2 are **HDFS Federation**, **YARN**, a highly available **NameNode**, and the concept of **Containers**.

![](https://i.loli.net/2021/01/07/YMBH4lzwkZeGyUc.png)

The fundamental idea of YARN is to **Split Hadoop resource management and job scheduling into separate processes (daemons)**.   将 Hadoop 资源管理和作业调度划分为单独的流程。

**ResourceManager** is the authority that arbitrates resources among all applications. Replaces the **JobTracker**. ResourceManger 决定哪个应用会获得下一个集群资源。

**NodeManager** is a per-machine framework responsible for **containers**, monitoring resource usage, and reporting to the **ResourceManager**. Each machine in a cluster is a NodeManager and a DataNode.

**ApplicationMaster** is tasked with negotiating resources from the **ResourceManager** and working with **NodeManagers** to execute & monitor tasks.

The ResourceManager, **NodeManager**, and **Container** are not connected about the type of task or application they are to run. Any application can run as long as an appropriate ApplicationMaster is implemented for it.

## 4. Database Intro

There are several main types that fit into 2 broad categories:

* Relational
* Non-relational

### NoSQL

NoSQL doesn't mean there is literally no SQL! For some, it means "Not Only SQL". Generally, NoSQL isn't relational, has no fixed schema, and no joins.

NoSQL databases can be categorised into four types:

* Key-value stores
* Column-oriented databases
* Document databases
* Graph databases

![](https://i.loli.net/2021/01/07/2wlzIPZqc3msDOi.png)

#### Key-value Stores

K-V 键值对是 NoSQL 最简单的类型，易于实现。

It good at:

* storing session information
* user profiles
* preferences

Bad at:

* query by data
* have relationships between the data being stored
* operating on multiple keys at the same time

#### Column-oriented Databases

基于列的数据库对于大型数据库执行聚合十分有效，可以轻易地分区。

#### Document-oriented Databases

Document-oriented databases are similar to key-value, except that the key links to a document of standard format/encoding (XML, JSON, etc.)

面向文档的数据库的查询更加有效，能够直接查询和更新。

#### Graph/Tree Databases

Graph databases use a flexible graph model, which can scale across multiple machines. It consists of two elements: a **node** and a **relationship**. Each node represents an entity and each relationship represents how two entites are related.

即每个 node 都代表一个实体，而 relationship 代表两个节点之间的关系。

It good at:

Problem spaces with connected data:

* social networks
* spatial data
* routing information for goods and money
* recommedation engines

## 5. Data Warehousing

> A data warehouse is a subject-oriented, integrated, time-variant and non-volatile collection of data in support of management's decision-making process.

数据仓库，是为企业所有级别的决策制定过程提供所有类型数据支持的战略集合。

![](https://i.loli.net/2021/01/07/1hHbDMrynxTCg3w.png)

![](https://i.loli.net/2021/01/07/LIfwcQFpOh6G2n4.png)

* Subject-oriented: 着重于将资料按照其意义归类至相同的主题区
* Integrated: 资料来自于企业的各个系统，在数据仓库中是集成并且一致的
* Time-variant: 时间差异性是指资料的变动，在数据仓库中是能够被记录并且追踪变化的，有助于反应出随着时间变化的资料轨迹
* Non-volatile: 不变动性是指资料一旦确认写入后，是不会被取代或者删除的

## 6. Data Deduplication

现在流行的重复数据删除主要采用三种级别：

* File-level: 以特定文件为基础，整合数据并且删除重复（速度快，但是去重性能一般）
* Block-level: 以磁盘 block 为单位来删除整合数据（能去除更多的重复，但是 CPU、IO 操作要求高）
* Byte-level: 字节级重复数据的删除（基于字节，对 CPU 要求更高）

### Source/Target - Based Deduplication

## 7. BigTable(HBase)

Google BigTable is one of the first major NoSQL databases, published in 2006. It is designed to reliably scale to petabytes of data across thousands of machines.

In NoSQL classification, **BigTable is a column-oriented database**. It is highly distributed, has no joins available, and assumes write-once-read-many.

BigTable is a simple concept - map two arbitrary string values (a row key and a column key) together with a timestamp and place in into an associated arbitrary byte array:

```
(row:string, column:string, time:int64) -> string
```

![](https://i.loli.net/2021/01/07/EJYP7AzyiRkmq3I.png)

## 8. Neo4j

> Neo4j is an ACID-compliant transactional database with native graph storage and processing.

Neo4j has Java integration through both a native API and the Cypher API. Cypher API is a Java API that allows us to execute CQL commands directly.

It is designed to find patterns, it can scans the database and quickly finds nodes and relationships that match the pattern asked for.

## 9. Spark

MapReduce on Hadoop has a number of limitations, like difficulty and performance. The result is that MapReduce dose not compose well for large applications. This has led to the development of a very popular system that tries to combine all of this.

Apache Spark is a general-purpose data processing engine. The features of Spark: In memory computation engine, almost 10x faster than Hadoop MapReduce using computations with Disk IO, almost 100x faster than Hadoop MapReduce with in-memory computations. 总而言之就是，Spark比Hadoop MapReduce快很多。

![](https://i.loli.net/2021/01/07/XCYlJNtzMe9rDTB.png)

### Spark Streaming

As a traditional stream processing system(an example is Apache Storm), there are some issues:

* Failures and straggling tasks
* Load balancing
* Unification of streaming, batch and interactive workloads
* Advanced analytics

To address these issues, the Spark Streaming component uses a new architecture called discretized streams that directly leverages the libraries and fault-tolerance of the Spack engine. Instead of reading a single data record at a time, Spark Streaming receivers discretize the streaming of data into tiny, sub-second micro-batches.

Each of these batches of data is an RDD. This allows the streaming data to be processed using any Spark code or library.

![](https://i.loli.net/2021/01/07/sUWL4bY71ZwKlmG.png)
## 10. Storm

> Apache Storm is a distributed real-time computation system for processing large volumes of high-velocity data.

It is extremely fast, and can process over one million records per second per node.

![](https://i.loli.net/2021/01/07/24Ayoh7g5HSTIP1.png)

The core abstraction in Storm is the **Stream**. A stream is data in the form of an unbounded sequence of tuples.

On a Storm cluster there are two types of nodes: a master node and workder nodes.

## 11. ZooKeeper

> Zookeeper was originally a Hadoop sub-project but is now a full Apache project. ZooKeeper is a centralized service for distributed configuration, synchronization services, and naming registry for distributed systems. It allows distributed processes to coordinate with each other through a shared hierarchical namespace of data registers.

![](https://i.loli.net/2021/01/07/dAxBoaNCmUv72tF.png)

ZooKeeper 是一个针对大型应用提供高可用的数据管理、应用程序协调服务的分布式服务框架，基于对 Paxos 算法的实现，使该框架保证了分布式环境中数据的强一致性，提供的功能包括：配合维护、统一命名服务、状态同步服务、集群管理等。

## 12. Kafka

> Apache Kafka is an open-source distributed event streaming platform for high-performance data pipelines, streaming analytics, data integration, and mission-critical applications.

Apache Kafka 是一个快速、可扩展的、高吞吐、可容错的分布式发布-订阅消息系统，具有高吞吐量、内置分区、支持数据副本和容错的特性，适合在大规模消息处理的场景中使用。

在发布-订阅消息系统中，消息被持久化到一个 topic 中，消费者可以订阅一个或者多个 topic，消费者可以消费该 topic 中所有的数据，同一条数据也可以被多个消费者消费。消费的生产者被定义为发布者，消费者被定义为订阅者。

![](https://i.loli.net/2021/01/07/gCb5TRKqV7tEHZY.png)