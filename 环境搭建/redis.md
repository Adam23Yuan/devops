### 安装redis 详细步骤

```bash
# 添加镜像源
cat <<'EOF' > /etc/yum.repos.d/redis.repo
[Redis]
name=Redis Repository
baseurl=http://rpms.remirepo.net/enterprise/7/remi/$basearch/
gpgcheck=0
enabled=1
EOF

# 查看redis 版本
yum --showduplicates list redis

# 安装 redis
yum install -y redis
# 开机启动
systemctl enable redis
# 启动 停止 查看 redis
systemctl start redis
systemctl stop redis
systemctl status redis


```

##### systemctl status redis 解释说明

```bash
systemctl status redis
● redis.service - Redis persistent key-value database
   Loaded: loaded (/usr/lib/systemd/system/redis.service; enabled; vendor preset: disabled)
  Drop-In: /etc/systemd/system/redis.service.d
           └─limit.conf
   Active: inactive (dead)
   
# 解释说明

******
● redis.service - Redis persistent key-value database
******
●：表示这是一个服务单元（unit），圆形符号通常用于终端显示，表示服务存在。
redis.service：这是 systemd 管理的服务名称，表示 Redis 服务。
Redis persistent key-value database：这是该服务的描述性说明，意思是“Redis 持久化键值数据库”，说明 Redis 是一个支持数据持久化的内存数据库。

******
Loaded: loaded (/usr/lib/systemd/system/redis.service; enabled; vendor preset: disabled)
******
Loaded：表示该服务已被 systemd 加载。
/usr/lib/systemd/system/redis.service：这是主服务配置文件的路径，是系统默认提供的服务定义文件。
enabled：表示该服务已设置为 开机自启。也就是说，系统重启后，Redis 会自动启动。
vendor preset: disabled：这是发行版预设状态。disabled 表示如果没有任何配置，该服务默认是“禁用”的，但当前实际状态是 enabled（被用户手动启用了）。

******
Drop-In: /etc/systemd/system/redis.service.d
           └─limit.conf
******
Drop-In：systemd 支持对服务进行“补丁式”配置覆盖，称为 “drop-in” 配置。这些配置不会修改原始服务文件，而是叠加在原始配置之上。
/etc/systemd/system/redis.service.d：这是一个目录，存放对 redis.service 的自定义覆盖配置。
└─limit.conf：这是该目录下的一个配置文件，名为 limit.conf，通常用于设置系统资源限制（如最大打开文件数、内存、进程数等）。

******
 Active: inactive (dead)
******
Active：表示服务当前的运行状态。
inactive (dead)：表示服务 当前没有运行，处于“非活动”状态。
inactive：未激活。
dead：没有进程在运行，也没有启动失败，只是单纯没启动
```

