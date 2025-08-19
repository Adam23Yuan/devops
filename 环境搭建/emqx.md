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

