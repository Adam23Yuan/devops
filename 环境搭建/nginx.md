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
      
      
      # 注 # 如果是  CentOS Stream 9 操作系统 执行以下命令 安装相关编译依赖库
      dnf install pcre pcre-devel zlib zlib-devel openssl openssl-devel wget -y
      # 安装gcc环境 
      yum -y install gcc-c++ make openssl openssl-devel libxml2 libxml2-devel libxslt-devel gd gd-devel GeoIP GeoIP-devel GeoIP-data
      cd nginx-1.28.0
      # 配置安装路径（默认会在 objs 目录生成二进制）
      ./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-http_v2_module --with-http_sub_module --with-http_gzip_static_module --with-pcre
      # 编译
      make
      # 安装
      make install
      
      # 查看nginx 版本
      [root@iZ2zeddamay9amikauf269Z cmd]# /usr/local/nginx/sbin/nginx -v
      nginx version: nginx/1.28.0
      [root@iZ2zeddamay9amikauf269Z cmd]# /usr/local/nginx/sbin/nginx -V
      nginx version: nginx/1.28.0
      built by gcc 4.8.5 20150623 (Red Hat 4.8.5-44) (GCC) 
      built with OpenSSL 1.0.2k-fips  26 Jan 2017
      TLS SNI support enabled
      configure arguments: --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-http_v2_module --with-http_sub_module --with-http_gzip_static_module --with-pcre
      
      
      ```

2. 设置开机启动

   1. ```
      # 创建服务文件
      touch /etc/systemd/system/nginx.service
      
      vim /etc/systemd/system/nginx.service
      # 复制以下内容 至 /etc/systemd/system/nginx.service 文件中
      [Unit]
      Description=The NGINX HTTP and reverse proxy server
      After=network.target
      
      [Service]
      Type=forking
      ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
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
      
      

### linux 7 镜像源的形式安装 nginx

##### 安装步骤

1. 安装

   1. ```bash
      # 创建repo文件
      sudo tee /etc/yum.repos.d/nginx.repo <<'EOF'
      [nginx-stable]
      name=nginx stable repo
      baseurl=http://nginx.org/packages/centos/7/$basearch/
      gpgcheck=1
      enabled=1
      gpgkey=https://nginx.org/keys/nginx_signing.key
      
      [nginx-mainline]
      name=nginx mainline repo
      baseurl=http://nginx.org/packages/mainline/centos/7/$basearch/
      gpgcheck=1
      enabled=0
      gpgkey=https://nginx.org/keys/nginx_signing.key
      EOF
      
      # 查看nginx版本
      yum --showduplicates list nginx
      已加载插件：fastestmirror
      Loading mirror speeds from cached hostfile
      可安装的软件包
      nginx.x86_64                   1:1.8.0-1.el7.ngx                    nginx-stable
      
      # 安装指定版本的 nginx
      # 安装 1.22.1版本
      sudo yum install -y nginx-1.22.1-1.el7.ngx
      
      ```

2. nginx 启动配置

   1. ```bash
      # 设置开机自动启动
      systemctl enable nginx
      Created symlink from /etc/systemd/system/multi-user.target.wants/nginx.service to /usr/lib/systemd/system/nginx.service.
      # 启动并查看 nginx
      systemctl start nginx
      [root@iZ2ze8d2ahedv4d36gi3feZ system]# systemctl status nginx
      ● nginx.service - nginx - high performance web server
         Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; vendor preset: disabled)
         Active: active (running) since 四 2025-10-16 17:53:53 CST; 2s ago
           Docs: http://nginx.org/en/docs/
        Process: 12203 ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf (code=exited, status=0/SUCCESS)
       Main PID: 12204 (nginx)
         CGroup: /system.slice/nginx.service
                 ├─12204 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
                 ├─12205 nginx: worker process
                 ├─12206 nginx: worker process
                 ├─12207 nginx: worker process
                 ├─12208 nginx: worker process
                 ├─12209 nginx: worker process
                 ├─12210 nginx: worker process
                 ├─12211 nginx: worker process
                 ├─12212 nginx: worker process
                 ├─12213 nginx: worker process
                 ├─12214 nginx: worker process
                 ├─12215 nginx: worker process
                 ├─12216 nginx: worker process
                 ├─12217 nginx: worker process
                 ├─12218 nginx: worker process
                 ├─12219 nginx: worker process
                 └─12220 nginx: worker process
      
      10月 16 17:53:53 iZ2ze8d2ahedv4d36gi3feZ systemd[1]: Starting nginx - high performance web server...
      10月 16 17:53:53 iZ2ze8d2ahedv4d36gi3feZ systemd[1]: Started nginx - high performance web server.
      
      # 取消开机自启
      
      systemctl disable nginx
      ```

      
