# 一级标题

## 二级标题

### 三级标题

#### 四级标题

# git 仓库初始化

``` bash
# 1. 进入你的目录
cd /path/to/mydir

# 2. 初始化 git 仓库
git init

# 3. 添加远程仓库（这里命名为 origin）
git remote add origin https://github.com/yourname/yourrepo.git

# 4. 添加当前目录所有文件
git add .

# 5. 提交
git commit -m "初始提交"

# 6. 推送到 GitHub main 分支（有的默认是 master）
git branch -M main
git push -u origin main

```

### package vs repackage

```mermaid
flowchart TB
  %% 定义子图 CLI
  subgraph CLI["命令"]
    A["mvn package"]
    B["mvn spring-boot:repackage"]
  end

  %% package 阶段
  A -->|触发生命周期| PHASE["Package Phase"]

  %% package 阶段触发的插件
  PHASE -->|默认| JAR["JAR/WAR plugin"]
  PHASE -->|如果配置了| REPK["spring-boot:repackage"]

  %% 显式调用 repackage
  B -->|显式执行| REPK

  %% 注释说明
  PHASE_NOTE["package 会触发 Maven 生命周期；spring-boot:repackage 如果绑定在 package 阶段则会执行"]
  PHASE --> PHASE_NOTE

```

### **阿里云 Tair、Redis 协议、客户端** 的关系

```mermaid
flowchart LR
    A[你的应用/客户端] -->|使用 Redis 协议| B[Tair / Redis 兼容版]
    B -->|内部存储| C[Tair 分布式存储系统]
    B -->|兼容 Redis 命令| D[Redis 命令集合]
    C -->|持久化/分片| E[(数据节点/集群)]
    F[官方 Redis 服务] -->|原生 Redis 命令| D

    style A fill:#f9f,stroke:#333,stroke-width:2px
    style B fill:#9f9,stroke:#333,stroke-width:2px
    style C fill:#ff9,stroke:#333,stroke-width:2px
    style D fill:#9ff,stroke:#333,stroke-width:2px
    style E fill:#fcc,stroke:#333,stroke-width:2px
    style F fill:#ccc,stroke:#333,stroke-width:2px

```

![image-20251011153558980](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20251011153558980.png)

### 简明版的“买 Tair vs 买 Redis 的区别图

```mermaid
graph TD
    A["阿里云数据库选择"] --> B["买 Tair (Redis 兼容版)"]
    A --> C["买 Redis (原生版)"]

    %% Tair 特性
    B --> B1["兼容 Redis 协议"]
    B --> B2["阿里云自研，分布式存储"]
    B --> B3["高可用、自动分片"]
    B --> B4["支持持久化"]
    B --> B5["按需扩展，按使用量付费"]
    B --> B6["适合波动流量、小型/中型项目"]

    %% Redis 特性
    C --> C1["原生 Redis 版本"]
    C --> C2["完全支持 Redis 新特性和模块"]
    C --> C3["高性能，企业级可靠性"]
    C --> C4["包年/包月固定规格"]
    C --> C5["适合大流量、生产环境、企业级应用"]

```

| 对比项   | Tair (Redis 兼容版)                               | Redis 原生版                                            |
| -------- | ------------------------------------------------- | ------------------------------------------------------- |
| 协议兼容 | 支持 Redis 协议，客户端无需改动                   | 原生 Redis 协议                                         |
| 功能     | 支持大部分 Redis 命令，部分新特性或模块可能不支持 | 完全支持 Redis 新特性和模块（如 Streams、RedisJSON 等） |
| 内部架构 | 阿里云自研分布式存储，高可用、自动分片            | 官方 Redis 存储，单机或集群模式                         |
| 持久化   | 支持持久化                                        | 支持持久化                                              |
| 扩展能力 | 弹性扩展，按使用量付费                            | 固定规格（包年/包月），可手动扩容                       |
| 运维     | 云端全托管，免运维                                | 云端托管或自建集群，需运维能力                          |
| 适用场景 | 流量波动大、中小型项目、开发测试环境              | 稳定大流量、企业生产环境、低延迟、高性能应用            |
| 成本     | 按使用量付费，灵活                                | 固定费用，成本可预测                                    |

###  **阿里云 Kafka 版实例** 与 **Confluent 版实例** 的架构与生态差异

```mermaid
graph TD
    %% 左侧：阿里云 Kafka 版
    A1(阿里云 Kafka 版实例)
    A2(Producer / Consumer)
    A3(Topic / Partition / Offset)
    A4(基础监控与管理)
    
    A2 --> A1 --> A3 --> A4

    %% 右侧：Confluent 版
    B1(Confluent 版实例)
    B2(Producer / Consumer)
    B3(Schema Registry)
    B4(Kafka Connect)
    B5(ksqlDB)
    B6(Control Center)
    B7(Replicator)
    
    B2 --> B1
    B1 --> B3
    B1 --> B4
    B1 --> B5
    B1 --> B6
    B1 --> B7

    %% 对比连接
    A4 -.-> B1

```

### Kafka 版 vs Confluent 版

```mermaid
graph TD
    A["阿里云消息队列 Kafka 服务"] --> B["Kafka 版实例"]
    A --> C["Confluent 版实例"]

    %% Kafka 版
    B --> B1["标准 Apache Kafka 协议"]
    B --> B2["兼容开源生态"]
    B --> B3["阿里云维护、优化性能"]
    B --> B4["适合普通消息队列场景"]

    %% Confluent 版
    C --> C1["由 Confluent 官方提供"]
    C --> C2["支持 Schema Registry"]
    C --> C3["支持 ksqlDB、Kafka Connect 等高级功能"]
    C --> C4["企业级安全与可观测性"]
```

| 对比项   | Kafka 版实例         | Confluent 版实例                 |
| -------- | -------------------- | -------------------------------- |
| 提供方   | 阿里云               | Confluent 官方                   |
| 协议兼容 | Apache Kafka         | Apache Kafka + 扩展功能          |
| 生态支持 | 基础 Kafka 客户端    | Schema Registry、ksqlDB、Connect |
| 使用场景 | 通用消息队列、日志流 | 企业级数据流平台、实时分析       |
| 成本     | 较低                 | 较高                             |
| 维护方   | 阿里云               | Confluent 官方（由阿里云托管）   |