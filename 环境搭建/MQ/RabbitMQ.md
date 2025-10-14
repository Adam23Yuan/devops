### CentOS Linux 7安装 RabbitMQ 3.9.5

#### 安装erlang

#### 通过仓库脚本（推荐，自动选合适包）

1. 添加镜像源并，并安装额外常用工具

   1. ```bash
      # 添加 RabbitMQ 的 Erlang 包仓库（packagecloud 提供的脚本）
      curl -s https://packagecloud.io/install/repositories/rabbitmq/erlang/script.rpm.sh | sudo bash
      
      # 查看 镜像版本
      yum --showduplicates list erlang
      
      # 安装 erlang（会安装与仓库对应的推荐版本）
      sudo yum -y install erlang
      # 安装额外常用工具
      sudo yum -y install socat logrotate
      # 查看erlang 的版本
      rpm -qi erlang
      # 查看整个 erlang 的安装目录 
      rpm -ql erlang
      
      ```

#### 添加 RabbitMQ Yum 仓库

1. ​	添加镜像源

   1. ```bash
      # 添加 RabbitMQ 官方仓库
      sudo tee /etc/yum.repos.d/rabbitmq_rabbitmq-server.repo > /dev/null <<'EOF'
      [rabbitmq_rabbitmq-server]
      name=RabbitMQ Server
      baseurl=https://packagecloud.io/rabbitmq/rabbitmq-server/el/7/$basearch
      enabled=1
      repo_gpgcheck=1
      gpgcheck=1
      gpgkey=https://packagecloud.io/rabbitmq/rabbitmq-server/gpgkey
      sslverify=1
      sslcacert=/etc/pki/tls/certs/ca-bundle.crt
      metadata_expire=300
      EOF
      
      # 导入公钥
      sudo rpm --import https://packagecloud.io/rabbitmq/rabbitmq-server/gpgkey
      sudo rpm --import https://packagecloud.io/rabbitmq/erlang/gpgkey
      
      
      # 刷新缓存并安装
      
      sudo yum clean all
      sudo yum makecache
      # 查看 镜像版本
      yum --showduplicates list rabbitmq-server
      # 安装指定版本  临时关闭 GPG 校验
      sudo yum install -y rabbitmq-server-3.9.5-1.el7 --nogpgcheck
      
      # 验证安装
      rpm -qa | grep rabbitmq
      # 应输出：rabbitmq-server-3.9.5-1.el7.noarch
      
      # 设置开机启动，并启动
      sudo systemctl enable rabbitmq-server
      sudo systemctl start rabbitmq-server
      sudo systemctl status rabbitmq-server
      
      # 启动web管理界面
      sudo rabbitmq-plugins enable rabbitmq_management
      
      # 默认存在的账户：guest 密码：guest 只有在本机才能用，远程无法使用。
      # 创建管理账户
      # 1️⃣ 创建用户（用户名 admin，密码 123456）
      sudo rabbitmqctl add_user rabbitadmin jsy919
      
      # 2️⃣ 赋予管理员权限
      sudo rabbitmqctl set_user_tags rabbitadmin administrator
      
      # 3️⃣ 授权可访问所有 vhost（默认 /）
      sudo rabbitmqctl set_permissions -p / sudo rabbitmqctl set_user_tags rabbitadmin administrator ".*" ".*" ".*"
      
      # 配置端口  5672，15672
      # 5672 通讯端口
      # 15672 web控制台端口
      
      # 一条命令，可以直接输出 EMQX 的安装根目录和配置文件目录
      # 根目录（RPM 包内实际路径）
      RABBITMQ_ROOT=$(rpm -ql rabbitmq-server | grep '/sbin/rabbitmq-server$' | head -n1 | xargs dirname | xargs dirname)
      echo "RabbitMQ 根目录: $RABBITMQ_ROOT"
      
      # 配置文件目录（优先默认系统路径 /etc/rabbitmq）
      if [ -d /etc/rabbitmq ]; then
          RABBITMQ_CONF="/etc/rabbitmq"
      else
          # 如果不存在 /etc/rabbitmq，则尝试从包内找
          RABBITMQ_CONF=$(rpm -ql rabbitmq-server | grep -E 'rabbitmq.config|rabbitmq.conf' | head -n1 | xargs dirname)
      fi
      echo "配置文件目录: $RABBITMQ_CONF"
      
      # 输出
       # 根目录（RPM 包内实际路径）
      [root@iZ2ze8d2ahedv4d36gi3feZ software]# RABBITMQ_ROOT=$(rpm -ql rabbitmq-server | grep '/sbin/rabbitmq-server$' | head -n1 | xargs dirname | xargs dirname)
      [root@iZ2ze8d2ahedv4d36gi3feZ software]# echo "RabbitMQ 根目录: $RABBITMQ_ROOT"
      RabbitMQ 根目录: /usr/lib/rabbitmq/lib/rabbitmq_server-3.9.5
      [root@iZ2ze8d2ahedv4d36gi3feZ software]# 
      [root@iZ2ze8d2ahedv4d36gi3feZ software]# # 配置文件目录（优先默认系统路径 /etc/rabbitmq）
      [root@iZ2ze8d2ahedv4d36gi3feZ software]# if [ -d /etc/rabbitmq ]; then
      >     RABBITMQ_CONF="/etc/rabbitmq"
      > else
      >     # 如果不存在 /etc/rabbitmq，则尝试从包内找
      >     RABBITMQ_CONF=$(rpm -ql rabbitmq-server | grep -E 'rabbitmq.config|rabbitmq.conf' | head -n1 | xargs dirname)
      > fi
      [root@iZ2ze8d2ahedv4d36gi3feZ software]# 
      [root@iZ2ze8d2ahedv4d36gi3feZ software]# echo "配置文件目录: $RABBITMQ_CONF"
      配置文件目录: /etc/rabbitmq
      [root@iZ2ze8d2ahedv4d36gi3feZ software]# 
      
      
      
      
      ```

#### 安装延迟队列插件  [rabbitmq/rabbitmq-delayed-message-exchange: Delayed Messaging for RabbitMQ](https://github.com/rabbitmq/rabbitmq-delayed-message-exchange)

1. ​	安装延迟队列插件

   1. ```
      # 切换到插件目录 
      cd /usr/lib/rabbitmq/lib/rabbitmq_server-3.9.5/plugins/
      # 下载插件包
      # 可采用先下载包，再上传至 mq的插件目录 
      https://github.com/rabbitmq/rabbitmq-delayed-message-exchange/releases
      # 启用插件
      rabbitmq-plugins enable rabbitmq_delayed_message_exchange
      
      # 重启 rabbitmq 服务
      sudo systemctl restart rabbitmq-server
      sudo systemctl status rabbitmq-server
      
      ```

      



































#### script

```bash
[root@iZ2zeddamay9amikauf269Z ~]# rabbitmqctl version
3.9.5
[root@iZ2zeddamay9amikauf269Z ~]# systemctl enable rabbitmq-server
Created symlink from /etc/systemd/system/multi-user.target.wants/rabbitmq-server.service to /usr/lib/systemd/system/rabbitmq-server.service.
[root@iZ2zeddamay9amikauf269Z ~]# systemctl status rabbitmq-server
● rabbitmq-server.service - RabbitMQ broker
   Loaded: loaded (/usr/lib/systemd/system/rabbitmq-server.service; enabled; vendor preset: disabled)
   Active: inactive (dead)
[root@iZ2zeddamay9amikauf269Z ~]# systemctl start rabbitmq-server
[root@iZ2zeddamay9amikauf269Z ~]# systemctl status rabbitmq-server
● rabbitmq-server.service - RabbitMQ broker
   Loaded: loaded (/usr/lib/systemd/system/rabbitmq-server.service; enabled; vendor preset: disabled)
   Active: active (running) since 二 2025-10-14 09:50:46 CST; 7s ago
 Main PID: 16876 (beam.smp)
    Tasks: 23
   Memory: 77.4M
   CGroup: /system.slice/rabbitmq-server.service
           ├─16876 /usr/lib64/erlang/erts-11.2.2.10/bin/beam.smp -W w -MBas ageffcbf -MHas ageffcbf -MBlmbcs 512 -MHlmbcs 512 -MMmcs 30 -P 1048576 -t 5000000 -stbt db -zdbbl 128000 -sbwt none -sbwtdcpu none -sbwtdio none -- -root /usr/lib64/erlang -progname erl -- -hom...
           ├─16891 erl_child_setup 32768
           ├─16953 inet_gethost 4
           └─16954 inet_gethost 4

10月 14 09:50:42 iZ2zeddamay9amikauf269Z rabbitmq-server[16876]: Doc guides:  https://rabbitmq.com/documentation.html
10月 14 09:50:42 iZ2zeddamay9amikauf269Z rabbitmq-server[16876]: Support:     https://rabbitmq.com/contact.html
10月 14 09:50:42 iZ2zeddamay9amikauf269Z rabbitmq-server[16876]: Tutorials:   https://rabbitmq.com/getstarted.html
10月 14 09:50:42 iZ2zeddamay9amikauf269Z rabbitmq-server[16876]: Monitoring:  https://rabbitmq.com/monitoring.html
10月 14 09:50:42 iZ2zeddamay9amikauf269Z rabbitmq-server[16876]: Logs: /var/log/rabbitmq/rabbit@iZ2zeddamay9amikauf269Z.log
10月 14 09:50:42 iZ2zeddamay9amikauf269Z rabbitmq-server[16876]: /var/log/rabbitmq/rabbit@iZ2zeddamay9amikauf269Z_upgrade.log
10月 14 09:50:42 iZ2zeddamay9amikauf269Z rabbitmq-server[16876]: <stdout>
10月 14 09:50:42 iZ2zeddamay9amikauf269Z rabbitmq-server[16876]: Config file(s): (none)
10月 14 09:50:46 iZ2zeddamay9amikauf269Z rabbitmq-server[16876]: Starting broker... completed with 0 plugins.
10月 14 09:50:46 iZ2zeddamay9amikauf269Z systemd[1]: Started RabbitMQ broker.
[root@iZ2zeddamay9amikauf269Z ~]# rabbitmq-plugins enable rabbitmq_management
Enabling plugins on node rabbit@iZ2zeddamay9amikauf269Z:
rabbitmq_management
The following plugins have been configured:
  rabbitmq_management
  rabbitmq_management_agent
  rabbitmq_web_dispatch
Applying plugin configuration to rabbit@iZ2zeddamay9amikauf269Z...
The following plugins have been enabled:
  rabbitmq_management
  rabbitmq_management_agent
  rabbitmq_web_dispatch

started 3 plugins.
[root@iZ2zeddamay9amikauf269Z ~]# systemctl stop rabbitmq-server
[root@iZ2zeddamay9amikauf269Z ~]# systemctl status rabbitmq-server
● rabbitmq-server.service - RabbitMQ broker
   Loaded: loaded (/usr/lib/systemd/system/rabbitmq-server.service; enabled; vendor preset: disabled)
   Active: inactive (dead) since 二 2025-10-14 09:52:03 CST; 1min 20s ago
  Process: 18518 ExecStop=/usr/sbin/rabbitmqctl shutdown (code=exited, status=0/SUCCESS)
  Process: 16876 ExecStart=/usr/sbin/rabbitmq-server (code=exited, status=0/SUCCESS)
 Main PID: 16876 (code=exited, status=0/SUCCESS)
   Status: "Standing by"


rabbitmqctl list_users
Listing users ...
user    tags
guest   [administrator]
[root@iZ2zeddamay9amikauf269Z ~]# rabbitmqctl add_user admin admin
Adding user "admin" ...
Done. Don't forget to grant the user permissions to some virtual hosts! See 'rabbitmqctl help set_permissions' to learn more.
[root@iZ2zeddamay9amikauf269Z ~]# rabbitmqctl set_user_tags admin administrator
Setting tags for user "admin" to [administrator] ...
[root@iZ2zeddamay9amikauf269Z ~]# rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
Error (argument validation): too many arguments.
Arguments given:
        set_permissions -p / admin .* .* .*rabbitmqctl set_permissions -p / admin .* .* .*

Usage

rabbitmqctl [--node <node>] [--longnames] [--quiet] set_permissions [--vhost <vhost>] <username> <conf> <write> <read>
[root@iZ2zeddamay9amikauf269Z ~]# rabbitmqctl list_users
Listing users ...
user    tags
admin   [administrator]
guest   [administrator]



```

