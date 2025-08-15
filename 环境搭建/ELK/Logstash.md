# ELK 生态圈

## Logstash  8.19.2  tar.gz 方式

1. 安装 logstash

   1. ```bash
      # 跳转至资源包目录
      cd /opt
      # 下载安装包
      wget https://artifacts.elastic.co/downloads/logstash/logstash-8.19.2-linux-x86_64.tar.gz
      # 解压logstash
      tar -xzf logstash-8.19.2-linux-x86_64.tar.gz 
      # 移动程序目录
      mv logstash-8.19.2 logstash
      # 添加 运行用户
      useradd -r -s /bin/false logstash
      # 查看刚添加的用户
      [root@iZ2zeddamay9amikauf269Z opt]# id logstash
      uid=991(logstash) gid=987(logstash) 组=987(logstash)
      # 修改运行目录的权限
      chown -R logstash:logstash /opt/logstash
      # 查看目录权限
      [root@iZ2zeddamay9amikauf269Z opt]# ls -ld /opt/logstash
      drwxr-xr-x 13 logstash logstash 4096 8月  14 12:19 /opt/logstash
      # 修改 logatash 的核心配置文件
      
      
      ```

2. 开启启动

   1. ```bash
      # 创建开机启动文件
      touch /etc/systemd/system/logstash.service
      # 通过vim 编辑上一步的文件，并复制以下内容
      [Unit]
      Description=Logstash
      After=network.target
      
      [Service]
      Type=simple
      User=logstash
      Group=logstash
      ExecStart=/opt/logstash/bin/logstash --path.settings /opt/logstash/config
      Restart=always
      LimitNOFILE=65536
      
      [Install]
      WantedBy=multi-user.target
      # 输入:wq 保存并退出
      # 启用并设置开机启动 logstash
      # 重新加载开机启动服务
      sudo systemctl daemon-reload
      # 开启开机启动配置
      sudo systemctl enable logstash
      # 启动 logstash
      sudo systemctl start logstash
      # 查看 logstash 运行状态
      sudo systemctl status logstash
      # 查看日志
      journalctl -u logstash -f
      ```

      

## 配置信息

