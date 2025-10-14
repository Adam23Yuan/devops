# 私有部署EMQX

### 参考连接 [离线安装]([下载 EMQX](https://www.emqx.com/zh/downloads-and-install/enterprise?os=RHEL))

#### 1.安装步骤

```bash


# cd 至下载目录 
cd /opt
# 下载 emqx-enterprise-5.10.0-docker-amd64.tar.gz
wget https://www.emqx.com/zh/downloads/enterprise/5.10.0/emqx-enterprise-5.10.0-el7-amd64.tar.gz
# 解压
mkdir -p emqx && tar -zxvf emqx-enterprise-5.10.0-el7-amd64.tar.gz -C emqx
# 启动


用户名密码：
emqx dashboard: http://115.190.43.101:18083/
emqx 平台登录账号
admin/emqx@admin

```

#### 2.做成服务开机启动

```bash
# 创建服务文件
touch /etc/systemd/system/emqx.service
# vim 编辑器 输入以下内容
[Unit]
Description=EMQ X Broker
After=network.target

[Service]
Type=forking
Environment="EMQX_NAME=emqx@127.0.0.1"
ExecStart=/opt/emqx/bin/emqx start
ExecStop=/opt/emqx/bin/emqx stop
Restart=on-failure

[Install]
WantedBy=multi-user.target

# :wq 保存退出

# 重新加载 systemd 服务
systemctl daemon-reload
# 设置开机启动
systemctl enable emqx
# 启动，停止，查看
systemctl start emqx
systemctl stop emqx
systemctl status emqx
```



#### 离线 RPM包的 安装方式 社区版本

```bash
# https://www.emqx.cn/
# 从emqx官方下载 需要安装的安装包
# 当前版本 https://www.emqx.com/zh/downloads-and-install/broker?os=RHEL
# 历史版本 https://www.emqx.com/zh/downloads/enterprise
# 通过查看 Linux 系统 CPU 架构选择对应的 RPM 包
uname -m
# 输出
# 输出	架构说明	对应 EMQX 包
# x86_64	64位 Intel/AMD CPU	emqx-enterprise-5.1.0-el7-amd64.rpm
# aarch64	64位 ARM CPU	emqx-enterprise-5.1.0-el7-arm64.rpm
# i686 / i386	32位 Intel/AMD CPU	不支持 64 位包，需要 32 位版本（通常 EMQX 不提供）

# 创建软件包目录 
mkdir /opt/software
# 下载 emqx 安装包
wget https://www.emqx.com/zh/downloads/broker/v5.1.0/emqx-5.1.0-el7-amd64.rpm
# 安装 emqx
yum install emqx-5.1.0-el7-amd64.rpm -y

# 启动emqx
sudo systemctl start emqx
# 查看 emqx 状态
sudo systemctl status emqx

# 服务启动文件
/usr/lib/systemd/system/emqx.service


# 查看安装的所有文件
查看包安装的所有文件
# 查看主可执行文件位置
which emqx
# 直接查询 RPM 包信息
rpm -qi emqx-enterprise

# 一条命令，可以直接输出 EMQX 的安装根目录和配置文件目录
echo "EMQX 根目录: $(rpm -ql emqx | grep '/bin/emqx$' | xargs dirname | xargs dirname)"; \
echo "配置文件目录: $(rpm -ql emqx | grep 'emqx.conf' | xargs dirname)"


```

