# ELK 生态圈

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
   
                  
