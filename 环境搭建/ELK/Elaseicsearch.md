# ELK 生态圈

[下载地址Past Releases | Elastic](https://www.elastic.co/downloads/past-releases?product=kibana)

## Elasticsearch   8.19.2  tar.gz 方式
1. 安装elasticsearch

   1. ```bash
      # 跳转至资源目录 
      cd /opt
      # 下载安装包
      wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.19.2-linux-x86_64.tar.gz
      # 解压elaseicsearch
      tar -zxvf elasticsearch-8.19.2-linux-x86_64.tar.gz
      # 移动程序目录
      mv elasticsearch-8.19.2 elasticsearch
      # 创建专用用户
      useradd -r -s /bin/false esuser
      chown -R esuser:esuser /opt/elasticsearch
      
      # 命令查看
      [root@VM-8-13-opencloudos opt]# id esuser
      uid=993(esuser) gid=993(esuser) groups=993(esuser)
      [root@VM-8-13-opencloudos opt]# ls -ld elasticsearch
      drwxr-xr-x 9 root root 155 Aug 11 18:14 elasticsearch
      [root@VM-8-13-opencloudos opt]# chown -R esuser:esuser /opt/elasticsearch
      [root@VM-8-13-opencloudos opt]# ls -ld elasticsearch
      drwxr-xr-x 9 esuser esuser 155 Aug 11 18:14 elasticsearch
      [root@VM-8-13-opencloudos opt]# 
      # 系统参数优化
      # 打开文件描述符数：
      echo "esuser soft nofile 65536" >> /etc/security/limits.conf
      echo "esuser hard nofile 65536" >> /etc/security/limits.conf
      # 设置 vm.max_map_count：
      echo "vm.max_map_count=262144" >> /etc/sysctl.conf
      sysctl -p
      
      ```
      
   2. es 配置文件	

      基础配置文件
   
      ```yaml
      # ======================== Elasticsearch Configuration =========================
      #
      # NOTE: Elasticsearch comes with reasonable defaults for most settings.
      #       Before you set out to tweak and tune the configuration, make sure you
      #       understand what are you trying to accomplish and the consequences.
      #
      # The primary way of configuring a node is via this file. This template lists
      # the most important settings you may want to configure for a production cluster.
      #
      # Please consult the documentation for further information on configuration options:
      # https://www.elastic.co/guide/en/elasticsearch/reference/index.html
      #
      # ---------------------------------- Cluster -----------------------------------
      #
      # Use a descriptive name for your cluster:
      #
      #cluster.name: my-application
      cluster.name: es-cluster
      discovery.type: single-node
      #
      # ------------------------------------ Node ------------------------------------
      #
      # Use a descriptive name for the node:
      #
      #node.name: node-1
      node.name: node-1
      #
      # Add custom attributes to the node:
      #
      #node.attr.rack: r1
      #
      # ----------------------------------- Paths ------------------------------------
      #
      # Path to directory where to store the data (separate multiple locations by comma):
      #
      #path.data: /path/to/data
      path.data: /opt/elasticsearch/data
      #
      # Path to log files:
      #
      #path.logs: /path/to/logs
      path.logs: /opt/elasticsearch/logs
      #
      # ----------------------------------- Memory -----------------------------------
      #
      # Lock the memory on startup:
      #
      #bootstrap.memory_lock: true
      #
      # Make sure that the heap size is set to about half the memory available
      # on the system and that the owner of the process is allowed to use this
      # limit.
      #
      # Elasticsearch performs poorly when the system is swapping the memory.
      #
      # ---------------------------------- Network -----------------------------------
      #
      # By default Elasticsearch is only accessible on localhost. Set a different
      # address here to expose this node on the network:
      #
      #network.host: 192.168.0.1
      network.host: 0.0.0.0   # 允许远程访问
      #
      # By default Elasticsearch listens for HTTP traffic on the first free port it
      # finds starting at 9200. Set a specific HTTP port here:
      #
      #http.port: 9200
      http.port: 9200
      #
      # For more information, consult the network module documentation.
      #
      # --------------------------------- Discovery ----------------------------------
      #
      # Pass an initial list of hosts to perform discovery when this node is started:
      # The default list of hosts is ["127.0.0.1", "[::1]"]
      #
      #discovery.seed_hosts: ["host1", "host2"]
      #
      # Bootstrap the cluster using an initial set of master-eligible nodes:
      #
      #cluster.initial_master_nodes: ["node-1", "node-2"]
      #
      # For more information, consult the discovery and cluster formation module documentation.
      #
      # ---------------------------------- Various -----------------------------------
      #
      # Allow wildcard deletion of indices:
      #
      #action.destructive_requires_name: false
      ```
   
   3. 启动elasticsearch
   
      1. 启动es时如果发生以下错误，则需要设置aws region 信息
   
         ```tex
         failed to obtain region from default provider chain
         software.amazon.awssdk.core.exception.SdkClientException: Unable to load region from any of the providers in the chain software.amazon.awssdk.regions.providers.DefaultAwsRegionProviderChain@5ae09ff: [software.amazon.awssdk.regions.providers.SystemSettingsRegionProvider@3b6c712a: Unable to load region from system settings. Region must be specified either via environment variable (AWS_REGION) or  system property (aws.region)., software.amazon.awssdk.regions.providers.AwsProfileRegionProvider@60144454: No region provided in profile: default, software.amazon.awssdk.regions.providers.InstanceProfileRegionProvider@57c4738e: Unable to contact EC2 metadata service.]
         	at software.amazon.awssdk.core.exception.SdkClientException$BuilderImpl.build(SdkClientException.java:130) ~[?:?]
         	at software.amazon.awssdk.regions.providers.AwsRegionProviderChain.getRegion(AwsRegionProviderChain.java:70) ~[?:?]
         	at org.elasticsearch.repositories.s3.S3RepositoryPlugin.getDefaultRegion(S3RepositoryPlugin.java:83) ~[?:?]
         	at org.elasticsearch.repositories.s3.S3Service.lambda$new$0(S3Service.java:144) ~[?:?]
         	at org.elasticsearch.common.util.concurrent.RunOnce.run(RunOnce.java:41) ~[elasticsearch-8.19.2.jar:?]
         	at org.elasticsearch.repositories.s3.S3Service.doStart(S3Service.java:467) ~[?:?]
         	at org.elasticsearch.common.component.AbstractLifecycleComponent.start(AbstractLifecycleComponent.java:51) ~[elasticsearch-8.19.2.jar:?]
         	at java.lang.Iterable.forEach(Iterable.java:75) ~[?:?]
         	at org.elasticsearch.node.Node.start(Node.java:294) ~[elasticsearch-8.19.2.jar:?]
         	at org.elasticsearch.bootstrap.Elasticsearch.start(Elasticsearch.java:617) ~[elasticsearch-8.19.2.jar:?]
         	at org.elasticsearch.bootstrap.Elasticsearch.initPhase3(Elasticsearch.java:417) ~[elasticsearch-8.19.2.jar:?]
         	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:99) ~[elasticsearch-8.19.2.jar:?]
         ```
   
         设置 aws region
   
         ```
         
         # 设置 aws region
         touch /opt/elasticsearch/config/jvm.options.d/aws-region.options
         # 输入以下内容
         -Daws.region=cn-north-1
         # 保存 并修改文件所属用户
         chown -R esuser:esuser /opt/elasticsearch
         ```
   
         
   
      2. ```
         # 切换用户专用用户启动
         su - esuser -s /bin/bash
         cd /opt/elasticsearch
         ./bin/elasticsearch -d
         # 验证启动是否成功
         curl http://localhost:9200
         # 出现以下内容代表启动成功
         {
           "name":"node-1",
           "cluster_name":"elasticsearch",
           "version":{"number":"8.19.2",...}
         }
         
         ```
   
      3. 设置systemd服务 开启启动
   
         1. ```bash
            # 创建开机启动服务文件
            touch /etc/systemd/system/elasticsearch.service
            # 通过vim 编辑上一步的文件，并复制以下内容
            [Unit]
            Description=Elasticsearch 8.19.2
            After=network.target
            
            [Service]
            Type=simple
            User=esuser
            ExecStart=/opt/elasticsearch/bin/elasticsearch
            Restart=on-failure
            LimitNOFILE=65536
            
            [Install]
            WantedBy=multi-user.target
            
            # 输入:wq 保存并退出
            # 启用并设置开机启动 elasticsearch
            # 重新加载开机启动服务
            sudo systemctl daemon-reload
            # 开启开机启动配置
            sudo systemctl enable elasticsearch
            # 启动 elasticsearch
            sudo systemctl start elasticsearch
            # 查看 elasticsearch 运行状态
            sudo systemctl status elasticsearch
            ```
   
         2.  防火墙配置（如远程访问）
   
            1. ```bash
               # 防火墙 添加入站规则 9200 端口
               firewall-cmd --add-port=9200/tcp --permanent
               firewall-cmd --reload
               # 防火墙 查询某个端口状态
               [root@VM-8-13-opencloudos ~]# firewall-cmd --query-port=9200/tcp
               yes
               # 查看完整规则
               [root@VM-8-13-opencloudos ~]# firewall-cmd --list-all
               public (active)
                 target: default
                 icmp-block-inversion: no
                 interfaces: eth0
                 sources: 
                 services: dhcpv6-client mdns ssh
                 ports: 20/tcp 21/tcp 22/tcp 80/tcp 443/tcp 8888/tcp 39000-40000/tcp 9200/tcp
                 protocols: 
                 forward: yes
                 masquerade: no
                 forward-ports: 
                 source-ports: 
                 icmp-blocks: 
                 rich rules: 
               
               
               ```
   
            2. ​	修改或者设置 用户密码 elastic  kibana_system
   
               1. ```
                  # 跳转至指定目录 
                  [root@VM-8-13-opencloudos ~]# cd /opt/elasticsearch/bin
                  # 执行命令 修改密码
                  [root@VM-8-13-opencloudos bin]# ./elasticsearch-reset-password -u elastic
                  This tool will reset the password of the [elastic] user to an autogenerated value.
                  The password will be printed in the console.
                  Please confirm that you would like to continue [y/N]y
                  
                  
                  Password for the [elastic] user successfully reset.
                  New value: H9UqA+JN__nbvYHuku=B
                  # 查看 es结点状态
                  curl --cacert /opt/elasticsearch/certs/http_ca.crt -u elastic:H9UqA+JN__nbvYHuku=B https://localhost:9200
                  [root@VM-8-13-opencloudos config]# curl --cacert /opt/elasticsearch/config/certs/http_ca.crt -u elastic:H9UqA+JN__nbvYHuku=B https://localhost:9200
                  {
                    "name" : "node-1",
                    "cluster_name" : "es-cluster",
                    "cluster_uuid" : "G0JuRpZWRECAmGn5wJX6Pg",
                    "version" : {
                      "number" : "8.19.2",
                      "build_flavor" : "default",
                      "build_type" : "tar",
                      "build_hash" : "c1c00e18ef14acd650768ff01f037eaede0c1f7b",
                      "build_date" : "2025-08-11T10:07:54.721189302Z",
                      "build_snapshot" : false,
                      "lucene_version" : "9.12.2",
                      "minimum_wire_compatibility_version" : "7.17.0",
                      "minimum_index_compatibility_version" : "7.0.0"
                    },
                    "tagline" : "You Know, for Search"
                  }
                  [root@VM-8-13-opencloudos config]# 
                  
                  # 更新 kibana_system 用户的密码
                  [root@VM-8-13-opencloudos bin]# ./elasticsearch-reset-password -u kibana_system
                  This tool will reset the password of the [kibana_system] user to an autogenerated value.
                  The password will be printed in the console.
                  Please confirm that you would like to continue [y/N]y
                  
                  
                  Password for the [kibana_system] user successfully reset.
                  New value: _Gz5w-1ujLJO0TAuWD63
                  [root@VM-8-13-opencloudos bin]# 
                  ```
   
                  

### 镜像源 安装 elasticsearch

#### 镜像源添加

1. 镜像源添加

   1. ```bash
      # 导入 Elasticsearch GPG 公钥
      sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
      
      # 添加 elasticsearch YUM 仓库
      cat > /etc/yum.repos.d/elasticsearch.repo <<EOF
      [elasticsearch-8.x]
      name=Elasticsearch repository for 8.x packages
      baseurl=https://artifacts.elastic.co/packages/8.x/yum
      gpgcheck=1
      gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
      enabled=1
      autorefresh=1
      type=rpm-md
      EOF
      
      # 更新缓存
      yum makecache 
      yum makecache fast
      
      ```

      

##### elasticsearch 安装

1. elasticsearch 安装

   1. ```bash
      # 查看版本
      yum --showduplicates list elasticsearch
      # 安装指定版本
      yum install elasticsearch-8.19.2-1
      ```

      

### RPM 包的形式安装

#### elasticsearch

```bash
rpm --install elasticsearch-8.19.2-x86_64.rpm
Creating elasticsearch group... OK
Creating elasticsearch user... OK
--------------------------- Security autoconfiguration information ------------------------------

Authentication and authorization are enabled.
TLS for the transport and HTTP layers is enabled and configured.

The generated password for the elastic built-in superuser is : AAc7KOmQLKcVSVwV9eNI

If this node should join an existing cluster, you can reconfigure this with
'/usr/share/elasticsearch/bin/elasticsearch-reconfigure-node --enrollment-token <token-here>'
after creating an enrollment token on your existing cluster.

You can complete the following actions at any time:

Reset the password of the elastic built-in superuser with 
'/usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic'.

Generate an enrollment token for Kibana instances with 
 '/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana'.

Generate an enrollment token for Elasticsearch nodes with 
'/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s node'.

-------------------------------------------------------------------------------------------------
### NOT starting on installation, please execute the following statements to configure elasticsearch service to start automatically using systemd
 sudo systemctl daemon-reload
 sudo systemctl enable elasticsearch.service
### You can start elasticsearch service by executing
 sudo systemctl start elasticsearch.service


[root@iZ2ze8q9293ytn6mlia8fuZ software]# systemctl daemon-reload
[root@iZ2ze8q9293ytn6mlia8fuZ software]# systemctl enable elasticsearch.service
Created symlink from /etc/systemd/system/multi-user.target.wants/elasticsearch.service to /usr/lib/systemd/system/elasticsearch.service.
[root@iZ2ze8q9293ytn6mlia8fuZ software]# systemctl start elasticsearch.service

## 验证 elasticsearch 是否安装成功

curl -u elastic:AAc7KOmQLKcVSVwV9eNI https://localhost:9200 --insecure


## 创建数据目录 
mkdir /mnt/vdb1/elasticsearch

# 查看用户列表
cat /etc/passwd
[root@iZ2ze8q9293ytn6mlia8fuZ elasticsearch]# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
chrony:x:998:996::/var/lib/chrony:/sbin/nologin
nscd:x:28:28:NSCD Daemon:/:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
nfsnobody:x:65534:65534:Anonymous NFS User:/var/lib/nfs:/sbin/nologin
elasticsearch:x:997:995:elasticsearch user:/nonexistent:/sbin/nologin


# 查看创建的目录 权限
[root@iZ2ze8q9293ytn6mlia8fuZ elasticsearch]# ls -ld /mnt/vdb1/elasticsearch
drwxr-xr-x 4 root root 4096 10月 20 14:04 /mnt/vdb1/elasticsearch
# 变更目录 权限，并查看
[root@iZ2ze8q9293ytn6mlia8fuZ elasticsearch]# sudo chown -R elasticsearch:elasticsearch /mnt/vdb1/elasticsearch
[root@iZ2ze8q9293ytn6mlia8fuZ elasticsearch]# ls -ld /mnt/vdb1/elasticsearch
drwxr-xr-x 4 elasticsearch elasticsearch 4096 10月 20 14:04 /mnt/vdb1/elasticsearch

#  启动 elasticsearch
systemctl start elasticsearch

```

##### Kibana

```
[root@iZ2ze8q9293ytn6mlia8fuZ ~]# cd /opt/software
[root@iZ2ze8q9293ytn6mlia8fuZ software]# rpm --install /opt/software/kibana-8.19.2-x86_64.rpm
Creating kibana group... OK
Creating kibana user... OK
Kibana is currently running with legacy OpenSSL providers enabled! For details and instructions on how to disable see https://www.elastic.co/guide/en/kibana/8.19/production.html#openssl-legacy-provider
Created Kibana keystore in /etc/kibana/kibana.keystore
[root@iZ2ze8q9293ytn6mlia8fuZ software]# 

[root@iZ2ze8q9293ytn6mlia8fuZ software]# systemctl status kibana
● kibana.service - Kibana
   Loaded: loaded (/usr/lib/systemd/system/kibana.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: https://www.elastic.co
[root@iZ2ze8q9293ytn6mlia8fuZ software]# systemctl daemon-reload
[root@iZ2ze8q9293ytn6mlia8fuZ software]# systemctl status kibana
● kibana.service - Kibana
   Loaded: loaded (/usr/lib/systemd/system/kibana.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: https://www.elastic.co
[root@iZ2ze8q9293ytn6mlia8fuZ software]# systemctl enable kibana
Created symlink from /etc/systemd/system/multi-user.target.wants/kibana.service to /usr/lib/systemd/system/kibana.service.
[root@iZ2ze8q9293ytn6mlia8fuZ software]# systemctl start kibana


```

##### Logstash

```bash

[root@iZ2ze8q9293ytn6mlia8fuZ ~]# cd /opt/software
[root@iZ2ze8q9293ytn6mlia8fuZ software]# rpm --install /opt/software/logstash-8.19.2-x86_64.rpm
[root@iZ2ze8q9293ytn6mlia8fuZ software]# systemctl status logstash
● logstash.service - logstash
   Loaded: loaded (/usr/lib/systemd/system/logstash.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
   
# 验证 配置文件
[root@iZ2ze8q9293ytn6mlia8fuZ software]# sudo /usr/share/logstash/bin/logstash -f /etc/logstash/conf.d/emqx_cloud_rabbitmq_to_es.conf --config.test_and_exit
Using bundled JDK: /usr/share/logstash/jdk
WARNING: Could not find logstash.yml which is typically located in $LS_HOME/config or /etc/logstash. You can specify the path using --path.settings. Continuing using the defaults
Could not find log4j2 configuration at path /usr/share/logstash/config/log4j2.properties. Using default config which logs errors to the console
[WARN ] 2025-10-20 15:34:44.823 [main] runner - Starting from version 9.0, running with superuser privileges is not permitted unless you explicitly set 'allow_superuser' to true, thereby acknowledging the possible security risks
[WARN ] 2025-10-20 15:34:44.828 [main] runner - NOTICE: Running Logstash as a superuser is strongly discouraged as it poses a security risk. Set 'allow_superuser' to false for better security.
[WARN ] 2025-10-20 15:34:44.836 [main] runner - 'pipeline.buffer.type' setting is not explicitly defined.Before moving to 9.x set it to 'heap' and tune heap size upward, or set it to 'direct' to maintain existing behavior.
[INFO ] 2025-10-20 15:34:44.837 [main] runner - Starting Logstash {"logstash.version"=>"8.19.2", "jruby.version"=>"jruby 9.4.9.0 (3.1.4) 2024-11-04 547c6b150e OpenJDK 64-Bit Server VM 21.0.7+6-LTS on 21.0.7+6-LTS +indy +jit [x86_64-linux]"}
[INFO ] 2025-10-20 15:34:44.839 [main] runner - JVM bootstrap flags: [-Xms1g, -Xmx1g, -Djava.awt.headless=true, -Dfile.encoding=UTF-8, -Djruby.compile.invokedynamic=true, -XX:+HeapDumpOnOutOfMemoryError, -Djava.security.egd=file:/dev/urandom, -Dlog4j2.isThreadContextMapInheritable=true, -Djruby.regexp.interruptible=true, -Djdk.io.File.enableADS=true, --add-exports=jdk.compiler/com.sun.tools.javac.api=ALL-UNNAMED, --add-exports=jdk.compiler/com.sun.tools.javac.file=ALL-UNNAMED, --add-exports=jdk.compiler/com.sun.tools.javac.parser=ALL-UNNAMED, --add-exports=jdk.compiler/com.sun.tools.javac.tree=ALL-UNNAMED, --add-exports=jdk.compiler/com.sun.tools.javac.util=ALL-UNNAMED, --add-opens=java.base/java.security=ALL-UNNAMED, --add-opens=java.base/java.io=ALL-UNNAMED, --add-opens=java.base/java.nio.channels=ALL-UNNAMED, --add-opens=java.base/sun.nio.ch=ALL-UNNAMED, --add-opens=java.management/sun.management=ALL-UNNAMED, -Dio.netty.allocator.maxOrder=11]
[INFO ] 2025-10-20 15:34:44.988 [main] StreamReadConstraintsUtil - Jackson default value override `logstash.jackson.stream-read-constraints.max-string-length` configured to `200000000` (logstash default)
[INFO ] 2025-10-20 15:34:44.989 [main] StreamReadConstraintsUtil - Jackson default value override `logstash.jackson.stream-read-constraints.max-number-length` configured to `10000` (logstash default)
[INFO ] 2025-10-20 15:34:44.989 [main] StreamReadConstraintsUtil - Jackson default value override `logstash.jackson.stream-read-constraints.max-nesting-depth` configured to `1000` (logstash default)
[INFO ] 2025-10-20 15:34:44.997 [main] settings - Creating directory {:setting=>"path.queue", :path=>"/usr/share/logstash/data/queue"}
[INFO ] 2025-10-20 15:34:44.998 [main] settings - Creating directory {:setting=>"path.dead_letter_queue", :path=>"/usr/share/logstash/data/dead_letter_queue"}
[WARN ] 2025-10-20 15:34:45.139 [LogStash::Runner] multilocal - Ignoring the 'pipelines.yml' file because modules or command line options are specified
[INFO ] 2025-10-20 15:34:46.409 [LogStash::Runner] Reflections - Reflections took 155 ms to scan 1 urls, producing 150 keys and 530 values
[INFO ] 2025-10-20 15:34:46.463 [LogStash::Runner] json - ECS compatibility is enabled but `target` option was not specified. This may cause fields to be set at the top-level of the event where they are likely to clash with the Elastic Common Schema. It is recommended to set the `target` option to avoid potential schema conflicts (if your data is ECS compliant or non-conflicting, feel free to ignore this message)
[INFO ] 2025-10-20 15:34:46.747 [LogStash::Runner] javapipeline - Pipeline `main` is configured with `pipeline.ecs_compatibility: v8` setting. All plugins in this pipeline will default to `ecs_compatibility => v8` unless explicitly configured otherwise.
Configuration OK
[INFO ] 2025-10-20 15:34:46.748 [LogStash::Runner] runner - Using config.test_and_exit mode. Config Validation Result: OK. Exiting Logstash
[root@iZ2ze8q9293ytn6mlia8fuZ software]# 
 # 开机启动
 [root@iZ2ze8q9293ytn6mlia8fuZ software]# systemctl enable logstash
Created symlink from /etc/systemd/system/multi-user.target.wants/logstash.service to /usr/lib/systemd/system/logstash.service.
[root@iZ2ze8q9293ytn6mlia8fuZ software]# systemctl start logstash
[root@iZ2ze8q9293ytn6mlia8fuZ software]# systemctl status logstash
● logstash.service - logstash
   Loaded: loaded (/usr/lib/systemd/system/logstash.service; enabled; vendor preset: disabled)
   Active: active (running) since 一 2025-10-20 15:36:29 CST; 3s ago
 Main PID: 29654 (java)
   CGroup: /system.slice/logstash.service
           └─29654 /usr/share/logstash/jdk/bin/java -Xms1g -Xmx1g -Djava.awt.headless=true -Dfile.encoding=UTF-8 -Djruby.compile.invokedynamic=true -XX:+HeapDumpOnOutOfMemoryError -Djava.security.egd=file:/dev/urandom -Dlog4j2.isThreadContextMapInherita...

10月 20 15:36:29 iZ2ze8q9293ytn6mlia8fuZ systemd[1]: Started logstash.
10月 20 15:36:29 iZ2ze8q9293ytn6mlia8fuZ logstash[29654]: Using bundled JDK: /usr/share/logstash/jdk

```

