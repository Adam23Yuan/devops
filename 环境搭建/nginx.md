# Nginx 安装 

## 安装步骤

1. 安装步骤

   1. ```bash
      # 下载官方手进制包
      https://nginx.org/en/download.html
      
      Stable version
      CHANGES-1.28	nginx-1.28.0  pgp	nginx/Windows-1.28.0  pgp
      
      # 下载需要安装nginx 版本的二进制包，并上传至Linux服务器
      cd /opt
      文件
      /opt/nginx-1.28.0.tar.gz
      
      # 解压文件
      tar -zxvf nginx-1.28.0.tar.gz
      cd nginx-1.28.0
      # 配置安装路径（默认会在 objs 目录生成二进制）
      ./configure --prefix=/usr/local/nginx
      # 编译
      make
      # 安装
      make install
      
      ```

2. 设置开机启动

   1. ```
      # 创建服务文件
      touch /etc/systemd/system/nginx.service
      # 复制以下内容 至 /etc/systemd/system/nginx.service 文件中
      [Unit]
      Description=The NGINX HTTP and reverse proxy server
      After=network.target
      
      [Service]
      Type=forking
      ExecStart=/usr/local/nginx/sbin/nginx
      ExecReload=/usr/local/nginx/sbin/nginx -s reload
      ExecStop=/usr/local/nginx/sbin/nginx -s quit
      PrivateTmp=true
      
      [Install]
      WantedBy=multi-user.target
      
      
      # 开机启动
      systemctl daemon-reload
      # 设置开机启动 nginx
      systemctl enable nginx
      # 启动nginx
      systemctl start nginx
      ```

      

