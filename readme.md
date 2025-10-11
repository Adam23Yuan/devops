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
