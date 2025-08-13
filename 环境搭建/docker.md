# Centos 服务器上安装 docker

###  安装步骤

1. 卸载旧的docker程序

   ```bash
   sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine  
   ```

   

2. 安装依赖

   ```bash
   sudo yum install -y yum-utils device-mapper-persistent-data lvm2
   ```

   

3. 添加 docker 源

   ```bash
   sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
   ```

   国内加速可用阿里源（可选）

   ```bash
   sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo 
   ```

   

4. 安装 Docker

   ```bash
   sudo yum install -y docker-ce docker-ce-cli containerd.io
   ```

   

5. 使用国内镜像加速器（例如阿里云）	

   ```bash
   sudo mkdir -p /etc/docker
   cat <<EOF | sudo tee /etc/docker/daemon.json
   {
     "registry-mirrors": [
       "https://miyaycjt.mirror.aliyuncs.com"
     ]
   }
   EOF
   ```

6. 启动 Docker 并设置开机启动

   1. ```
      sudo systemctl start docker
      sudo systemctl enable docker
      ```

7. 验证 Docker 安装

   1. ```bash
      docker version	
      ```

8. 安装docker-compose,

   1. ```bash
      安装docker-compose v2 推荐
      sudo curl -L "https://github.com/docker/compose/releases/download/2.38.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
      
      ```

   2. ```bash
      修改权限
      sudo chmod +x /usr/local/bin/docker-compose
      
      查看版本
      docker-compose version
      
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# echo $(uname -s)
      Linux
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# echo $(uname -m)
      x86_64
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# 
      
      ```

      

9. 运行窗口

   1. ```
      docker run hello-world
      ```

10. 完整安装步骤 安装命令执行

   1. ```bash
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# cat /etc/os-release
      NAME="CentOS Stream"
      VERSION="9"
      ID="centos"
      ID_LIKE="rhel fedora"
      VERSION_ID="9"
      PLATFORM_ID="platform:el9"
      PRETTY_NAME="CentOS Stream 9"
      ANSI_COLOR="0;31"
      LOGO="fedora-logo-icon"
      CPE_NAME="cpe:/o:centos:centos:9"
      HOME_URL="https://centos.org/"
      BUG_REPORT_URL="https://bugzilla.redhat.com/"
      REDHAT_SUPPORT_PRODUCT="Red Hat Enterprise Linux 9"
      REDHAT_SUPPORT_PRODUCT_VERSION="CentOS Stream"
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# uname -r
      5.14.0-312.el9.x86_64
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
      No match for argument: docker
      No match for argument: docker-client
      No match for argument: docker-client-latest
      No match for argument: docker-common
      No match for argument: docker-latest
      No match for argument: docker-latest-logrotate
      No match for argument: docker-logrotate
      No match for argument: docker-engine
      No packages marked for removal.
      Dependencies resolved.
      Nothing to do.
      Complete!
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# yum install -y yum-utils device-mapper-persistent-data lvm2
      Last metadata expiration check: 1:20:30 ago on Mon 14 Jul 2025 09:23:17 AM CST.
      Dependencies resolved.
      =========================================================================================================================================================================================================================================================================
       Package                                                                        Architecture                                            Version                                                            Repository                                               Size
      =========================================================================================================================================================================================================================================================================
      Installing:
       device-mapper-persistent-data                                                  x86_64                                                  1.1.0-1.el9                                                        baseos                                                  1.1 M
       lvm2                                                                           x86_64                                                  9:2.03.32-2.el9                                                    baseos                                                  1.6 M
       yum-utils                                                                      noarch                                                  4.3.0-21.el9                                                       baseos                                                   40 k
      Upgrading:
       device-mapper                                                                  x86_64                                                  9:1.02.206-2.el9                                                   baseos                                                  140 k
       device-mapper-libs                                                             x86_64                                                  9:1.02.206-2.el9                                                   baseos                                                  180 k
       dnf-plugins-core                                                               noarch                                                  4.3.0-21.el9                                                       baseos                                                   37 k
       python3-dnf-plugins-core                                                       noarch                                                  4.3.0-21.el9                                                       baseos                                                  264 k
      Installing dependencies:
       device-mapper-event                                                            x86_64                                                  9:1.02.206-2.el9                                                   baseos                                                   34 k
       device-mapper-event-libs                                                       x86_64                                                  9:1.02.206-2.el9                                                   baseos                                                   32 k
       libaio                                                                         x86_64                                                  0.3.111-13.el9                                                     baseos                                                   24 k
       libnvme                                                                        x86_64                                                  1.13-1.el9                                                         baseos                                                  111 k
       lvm2-libs                                                                      x86_64                                                  9:2.03.32-2.el9                                                    baseos                                                  1.0 M
       python3-systemd                                                                x86_64                                                  234-19.el9                                                         baseos                                                   89 k
      
      Transaction Summary
      =========================================================================================================================================================================================================================================================================
      Install  9 Packages
      Upgrade  4 Packages
      
      Total download size: 4.6 M
      Downloading Packages:
      (1/13): device-mapper-event-1.02.206-2.el9.x86_64.rpm                                                                                                                                                                                    6.8 MB/s |  34 kB     00:00    
      (2/13): device-mapper-event-libs-1.02.206-2.el9.x86_64.rpm                                                                                                                                                                               5.0 MB/s |  32 kB     00:00    
      (3/13): libaio-0.3.111-13.el9.x86_64.rpm                                                                                                                                                                                                 5.8 MB/s |  24 kB     00:00    
      (4/13): device-mapper-persistent-data-1.1.0-1.el9.x86_64.rpm                                                                                                                                                                              38 MB/s | 1.1 MB     00:00    
      (5/13): libnvme-1.13-1.el9.x86_64.rpm                                                                                                                                                                                                    4.4 MB/s | 111 kB     00:00    
      (6/13): lvm2-2.03.32-2.el9.x86_64.rpm                                                                                                                                                                                                     47 MB/s | 1.6 MB     00:00    
      (7/13): python3-systemd-234-19.el9.x86_64.rpm                                                                                                                                                                                            6.3 MB/s |  89 kB     00:00    
      (8/13): yum-utils-4.3.0-21.el9.noarch.rpm                                                                                                                                                                                                 10 MB/s |  40 kB     00:00    
      (9/13): lvm2-libs-2.03.32-2.el9.x86_64.rpm                                                                                                                                                                                                46 MB/s | 1.0 MB     00:00    
      (10/13): device-mapper-libs-1.02.206-2.el9.x86_64.rpm                                                                                                                                                                                     27 MB/s | 180 kB     00:00    
      (11/13): dnf-plugins-core-4.3.0-21.el9.noarch.rpm                                                                                                                                                                                         12 MB/s |  37 kB     00:00    
      (12/13): device-mapper-1.02.206-2.el9.x86_64.rpm                                                                                                                                                                                          12 MB/s | 140 kB     00:00    
      (13/13): python3-dnf-plugins-core-4.3.0-21.el9.noarch.rpm                                                                                                                                                                                 50 MB/s | 264 kB     00:00    
      -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
      Total                                                                                                                                                                                                                                     76 MB/s | 4.6 MB     00:00     
      Running transaction check
      Transaction check succeeded.
      Running transaction test
      Transaction test succeeded.
      Running transaction
        Preparing        :                                                                                                                                                                                                                                                 1/1 
        Upgrading        : device-mapper-libs-9:1.02.206-2.el9.x86_64                                                                                                                                                                                                     1/17 
        Upgrading        : device-mapper-9:1.02.206-2.el9.x86_64                                                                                                                                                                                                          2/17 
        Installing       : device-mapper-event-libs-9:1.02.206-2.el9.x86_64                                                                                                                                                                                               3/17 
        Installing       : libnvme-1.13-1.el9.x86_64                                                                                                                                                                                                                      4/17 
        Installing       : libaio-0.3.111-13.el9.x86_64                                                                                                                                                                                                                   5/17 
        Installing       : device-mapper-event-9:1.02.206-2.el9.x86_64                                                                                                                                                                                                    6/17 
        Running scriptlet: device-mapper-event-9:1.02.206-2.el9.x86_64                                                                                                                                                                                                    6/17 
      Created symlink /etc/systemd/system/sockets.target.wants/dm-event.socket → /usr/lib/systemd/system/dm-event.socket.
      
        Installing       : lvm2-libs-9:2.03.32-2.el9.x86_64                                                                                                                                                                                                               7/17 
        Installing       : python3-systemd-234-19.el9.x86_64                                                                                                                                                                                                              8/17 
        Upgrading        : python3-dnf-plugins-core-4.3.0-21.el9.noarch                                                                                                                                                                                                   9/17 
        Upgrading        : dnf-plugins-core-4.3.0-21.el9.noarch                                                                                                                                                                                                          10/17 
        Installing       : device-mapper-persistent-data-1.1.0-1.el9.x86_64                                                                                                                                                                                              11/17 
        Installing       : lvm2-9:2.03.32-2.el9.x86_64                                                                                                                                                                                                                   12/17 
        Running scriptlet: lvm2-9:2.03.32-2.el9.x86_64                                                                                                                                                                                                                   12/17 
      Created symlink /etc/systemd/system/sysinit.target.wants/lvm2-monitor.service → /usr/lib/systemd/system/lvm2-monitor.service.
      Created symlink /etc/systemd/system/sysinit.target.wants/lvm2-lvmpolld.socket → /usr/lib/systemd/system/lvm2-lvmpolld.socket.
      
        Installing       : yum-utils-4.3.0-21.el9.noarch                                                                                                                                                                                                                 13/17 
        Cleanup          : device-mapper-9:1.02.185-3.el9.x86_64                                                                                                                                                                                                         14/17 
        Cleanup          : dnf-plugins-core-4.1.0-2.el9.noarch                                                                                                                                                                                                           15/17 
        Cleanup          : python3-dnf-plugins-core-4.1.0-2.el9.noarch                                                                                                                                                                                                   16/17 
        Cleanup          : device-mapper-libs-9:1.02.185-3.el9.x86_64                                                                                                                                                                                                    17/17 
        Running scriptlet: device-mapper-libs-9:1.02.185-3.el9.x86_64                                                                                                                                                                                                    17/17 
        Verifying        : device-mapper-event-9:1.02.206-2.el9.x86_64                                                                                                                                                                                                    1/17 
        Verifying        : device-mapper-event-libs-9:1.02.206-2.el9.x86_64                                                                                                                                                                                               2/17 
        Verifying        : device-mapper-persistent-data-1.1.0-1.el9.x86_64                                                                                                                                                                                               3/17 
        Verifying        : libaio-0.3.111-13.el9.x86_64                                                                                                                                                                                                                   4/17 
        Verifying        : libnvme-1.13-1.el9.x86_64                                                                                                                                                                                                                      5/17 
        Verifying        : lvm2-9:2.03.32-2.el9.x86_64                                                                                                                                                                                                                    6/17 
        Verifying        : lvm2-libs-9:2.03.32-2.el9.x86_64                                                                                                                                                                                                               7/17 
        Verifying        : python3-systemd-234-19.el9.x86_64                                                                                                                                                                                                              8/17 
        Verifying        : yum-utils-4.3.0-21.el9.noarch                                                                                                                                                                                                                  9/17 
        Verifying        : device-mapper-9:1.02.206-2.el9.x86_64                                                                                                                                                                                                         10/17 
        Verifying        : device-mapper-9:1.02.185-3.el9.x86_64                                                                                                                                                                                                         11/17 
        Verifying        : device-mapper-libs-9:1.02.206-2.el9.x86_64                                                                                                                                                                                                    12/17 
        Verifying        : device-mapper-libs-9:1.02.185-3.el9.x86_64                                                                                                                                                                                                    13/17 
        Verifying        : dnf-plugins-core-4.3.0-21.el9.noarch                                                                                                                                                                                                          14/17 
        Verifying        : dnf-plugins-core-4.1.0-2.el9.noarch                                                                                                                                                                                                           15/17 
        Verifying        : python3-dnf-plugins-core-4.3.0-21.el9.noarch                                                                                                                                                                                                  16/17 
        Verifying        : python3-dnf-plugins-core-4.1.0-2.el9.noarch                                                                                                                                                                                                   17/17 
      
      Upgraded:
        device-mapper-9:1.02.206-2.el9.x86_64                          device-mapper-libs-9:1.02.206-2.el9.x86_64                          dnf-plugins-core-4.3.0-21.el9.noarch                          python3-dnf-plugins-core-4.3.0-21.el9.noarch                         
      Installed:
        device-mapper-event-9:1.02.206-2.el9.x86_64 device-mapper-event-libs-9:1.02.206-2.el9.x86_64 device-mapper-persistent-data-1.1.0-1.el9.x86_64 libaio-0.3.111-13.el9.x86_64 libnvme-1.13-1.el9.x86_64 lvm2-9:2.03.32-2.el9.x86_64 lvm2-libs-9:2.03.32-2.el9.x86_64
        python3-systemd-234-19.el9.x86_64           yum-utils-4.3.0-21.el9.noarch                   
      
      Complete!
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
      Adding repo from: https://download.docker.com/linux/centos/docker-ce.repo
      Curl error (35): SSL connect error for https://download.docker.com/linux/centos/docker-ce.repo [OpenSSL SSL_connect: Connection reset by peer in connection to download.docker.com:443 ]
      Error: Configuration of repo failed
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# 
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
      Adding repo from: https://download.docker.com/linux/centos/docker-ce.repo
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
      Adding repo from: https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# yum install -y docker-ce docker-ce-cli containerd.io
      Docker CE Stable - x86_64                                                                                                                                                                                                                198 kB/s |  76 kB     00:00    
      Dependencies resolved.
      =========================================================================================================================================================================================================================================================================
       Package                                                                   Architecture                                           Version                                                         Repository                                                        Size
      =========================================================================================================================================================================================================================================================================
      Installing:
       containerd.io                                                             x86_64                                                 1.7.27-3.1.el9                                                  docker-ce-stable                                                  44 M
       docker-ce                                                                 x86_64                                                 3:28.3.2-1.el9                                                  docker-ce-stable                                                  21 M
       docker-ce-cli                                                             x86_64                                                 1:28.3.2-1.el9                                                  docker-ce-stable                                                 8.6 M
      Upgrading:
       selinux-policy                                                            noarch                                                 38.1.60-1.el9                                                   baseos                                                            44 k
       selinux-policy-targeted                                                   noarch                                                 38.1.60-1.el9                                                   baseos                                                           6.9 M
      Installing dependencies:
       container-selinux                                                         noarch                                                 4:2.237.0-1.el9                                                 appstream                                                         61 k
       fuse-common                                                               x86_64                                                 3.10.2-9.el9                                                    baseos                                                           8.3 k
       fuse-overlayfs                                                            x86_64                                                 1.15-1.el9                                                      appstream                                                         67 k
       fuse3                                                                     x86_64                                                 3.10.2-9.el9                                                    appstream                                                         54 k
       fuse3-libs                                                                x86_64                                                 3.10.2-9.el9                                                    appstream                                                         91 k
       libslirp                                                                  x86_64                                                 4.4.0-8.el9                                                     appstream                                                         68 k
       slirp4netns                                                               x86_64                                                 1.3.3-1.el9                                                     appstream                                                         47 k
      Installing weak dependencies:
       docker-buildx-plugin                                                      x86_64                                                 0.25.0-1.el9                                                    docker-ce-stable                                                  16 M
       docker-ce-rootless-extras                                                 x86_64                                                 28.3.2-1.el9                                                    docker-ce-stable                                                 3.4 M
       docker-compose-plugin                                                     x86_64                                                 2.38.2-1.el9                                                    docker-ce-stable                                                  15 M
      
      Transaction Summary
      =========================================================================================================================================================================================================================================================================
      Install  13 Packages
      Upgrade   2 Packages
      
      Total download size: 115 M
      Downloading Packages:
      (1/15): fuse-overlayfs-1.15-1.el9.x86_64.rpm                                                                                                                                                                                              12 MB/s |  67 kB     00:00    
      (2/15): fuse-common-3.10.2-9.el9.x86_64.rpm                                                                                                                                                                                              1.2 MB/s | 8.3 kB     00:00    
      (3/15): fuse3-libs-3.10.2-9.el9.x86_64.rpm                                                                                                                                                                                                45 MB/s |  91 kB     00:00    
      (4/15): fuse3-3.10.2-9.el9.x86_64.rpm                                                                                                                                                                                                     10 MB/s |  54 kB     00:00    
      (5/15): container-selinux-2.237.0-1.el9.noarch.rpm                                                                                                                                                                                       4.5 MB/s |  61 kB     00:00    
      (6/15): libslirp-4.4.0-8.el9.x86_64.rpm                                                                                                                                                                                                   12 MB/s |  68 kB     00:00    
      (7/15): slirp4netns-1.3.3-1.el9.x86_64.rpm                                                                                                                                                                                               8.7 MB/s |  47 kB     00:00    
      
      连接断开
      连接主机...
      连接主机成功
      Last failed login: Mon Jul 14 10:42:15 CST 2025 from 45.135.232.92 on ssh:notty
      There were 11 failed login attempts since the last successful login.
      Last login: Mon Jul 14 10:28:24 2025 from 111.18.133.112
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# yum install -y docker-ce docker-ce-cli containerd.io
      Last metadata expiration check: 0:01:05 ago on Mon 14 Jul 2025 10:45:03 AM CST.
      Dependencies resolved.
      =========================================================================================================================================================================================================================================================================
       Package                                                                   Architecture                                           Version                                                         Repository                                                        Size
      =========================================================================================================================================================================================================================================================================
      Installing:
       containerd.io                                                             x86_64                                                 1.7.27-3.1.el9                                                  docker-ce-stable                                                  44 M
       docker-ce                                                                 x86_64                                                 3:28.3.2-1.el9                                                  docker-ce-stable                                                  21 M
       docker-ce-cli                                                             x86_64                                                 1:28.3.2-1.el9                                                  docker-ce-stable                                                 8.6 M
      Upgrading:
       selinux-policy                                                            noarch                                                 38.1.60-1.el9                                                   baseos                                                            44 k
       selinux-policy-targeted                                                   noarch                                                 38.1.60-1.el9                                                   baseos                                                           6.9 M
      Installing dependencies:
       container-selinux                                                         noarch                                                 4:2.237.0-1.el9                                                 appstream                                                         61 k
       fuse-common                                                               x86_64                                                 3.10.2-9.el9                                                    baseos                                                           8.3 k
       fuse-overlayfs                                                            x86_64                                                 1.15-1.el9                                                      appstream                                                         67 k
       fuse3                                                                     x86_64                                                 3.10.2-9.el9                                                    appstream                                                         54 k
       fuse3-libs                                                                x86_64                                                 3.10.2-9.el9                                                    appstream                                                         91 k
       libslirp                                                                  x86_64                                                 4.4.0-8.el9                                                     appstream                                                         68 k
       slirp4netns                                                               x86_64                                                 1.3.3-1.el9                                                     appstream                                                         47 k
      Installing weak dependencies:
       docker-buildx-plugin                                                      x86_64                                                 0.25.0-1.el9                                                    docker-ce-stable                                                  16 M
       docker-ce-rootless-extras                                                 x86_64                                                 28.3.2-1.el9                                                    docker-ce-stable                                                 3.4 M
       docker-compose-plugin                                                     x86_64                                                 2.38.2-1.el9                                                    docker-ce-stable                                                  15 M
      
      Transaction Summary
      =========================================================================================================================================================================================================================================================================
      Install  13 Packages
      Upgrade   2 Packages
      
      Total size: 115 M
      Total download size: 115 M
      Downloading Packages:
      [SKIPPED] fuse-common-3.10.2-9.el9.x86_64.rpm: Already downloaded                                                                                                                                                                                                       
      [SKIPPED] container-selinux-2.237.0-1.el9.noarch.rpm: Already downloaded                                                                                                                                                                                                
      [SKIPPED] fuse-overlayfs-1.15-1.el9.x86_64.rpm: Already downloaded                                                                                                                                                                                                      
      [SKIPPED] fuse3-3.10.2-9.el9.x86_64.rpm: Already downloaded                                                                                                                                                                                                             
      [SKIPPED] fuse3-libs-3.10.2-9.el9.x86_64.rpm: Already downloaded                                                                                                                                                                                                        
      [SKIPPED] libslirp-4.4.0-8.el9.x86_64.rpm: Already downloaded                                                                                                                                                                                                           
      [SKIPPED] slirp4netns-1.3.3-1.el9.x86_64.rpm: Already downloaded                                                                                                                                                                                                        
      (8/15): docker-buildx-plugin-0.25.0-1.el9.x86_64.rpm                                                                                                                                                                                     576 kB/s | 4.9 MB     00:08    
      (9/15): docker-ce-28.3.2-1.el9.x86_64.rpm                                                                                                                                                                                                471 kB/s | 9.4 MB     00:20    
      (10/15): docker-ce-rootless-extras-28.3.2-1.el9.x86_64.rpm                                                                                                                                                                               372 kB/s | 3.4 MB     00:09    
      (11/15): docker-ce-cli-28.3.2-1.el9.x86_64.rpm                                                                                                                                                                                           330 kB/s | 8.6 MB     00:26    
      (12/15): selinux-policy-38.1.60-1.el9.noarch.rpm                                                                                                                                                                                         6.5 MB/s |  44 kB     00:00    
      (13/15): selinux-policy-targeted-38.1.60-1.el9.noarch.rpm                                                                                                                                                                                 83 MB/s | 6.9 MB     00:00    
      (14/15): containerd.io-1.7.27-3.1.el9.x86_64.rpm                                                                                                                                                                                         447 kB/s |  23 MB     00:53    
      (15/15): docker-compose-plugin-2.38.2-1.el9.x86_64.rpm                                                                                                                                                                                   586 kB/s |  15 MB     00:26    
      -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
      Total                                                                                                                                                                                                                                    2.1 MB/s | 115 MB     00:55     
      Docker CE Stable - x86_64                                                                                                                                                                                                                 12 kB/s | 1.6 kB     00:00    
      Importing GPG key 0x621E9F35:
       Userid     : "Docker Release (CE rpm) <docker@docker.com>"
       Fingerprint: 060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35
       From       : https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
      Key imported successfully
      Running transaction check
      Transaction check succeeded.
      Running transaction test
      Transaction test succeeded.
      Running transaction
        Running scriptlet: selinux-policy-targeted-38.1.60-1.el9.noarch                                                                                                                                                                                                    1/1 
        Preparing        :                                                                                                                                                                                                                                                 1/1 
        Upgrading        : selinux-policy-38.1.60-1.el9.noarch                                                                                                                                                                                                            1/17 
        Running scriptlet: selinux-policy-38.1.60-1.el9.noarch                                                                                                                                                                                                            1/17 
        Running scriptlet: selinux-policy-targeted-38.1.60-1.el9.noarch                                                                                                                                                                                                   2/17 
        Upgrading        : selinux-policy-targeted-38.1.60-1.el9.noarch                                                                                                                                                                                                   2/17 
        Running scriptlet: selinux-policy-targeted-38.1.60-1.el9.noarch                                                                                                                                                                                                   2/17 
        Running scriptlet: container-selinux-4:2.237.0-1.el9.noarch                                                                                                                                                                                                       3/17 
        Installing       : container-selinux-4:2.237.0-1.el9.noarch                                                                                                                                                                                                       3/17 
        Running scriptlet: container-selinux-4:2.237.0-1.el9.noarch                                                                                                                                                                                                       3/17 
        Installing       : docker-buildx-plugin-0.25.0-1.el9.x86_64                                                                                                                                                                                                       4/17 
        Running scriptlet: docker-buildx-plugin-0.25.0-1.el9.x86_64                                                                                                                                                                                                       4/17 
        Installing       : docker-compose-plugin-2.38.2-1.el9.x86_64                                                                                                                                                                                                      5/17 
        Running scriptlet: docker-compose-plugin-2.38.2-1.el9.x86_64                                                                                                                                                                                                      5/17 
        Installing       : fuse3-libs-3.10.2-9.el9.x86_64                                                                                                                                                                                                                 6/17 
        Installing       : docker-ce-cli-1:28.3.2-1.el9.x86_64                                                                                                                                                                                                            7/17 
        Running scriptlet: docker-ce-cli-1:28.3.2-1.el9.x86_64                                                                                                                                                                                                            7/17 
        Installing       : containerd.io-1.7.27-3.1.el9.x86_64                                                                                                                                                                                                            8/17 
        Running scriptlet: containerd.io-1.7.27-3.1.el9.x86_64                                                                                                                                                                                                            8/17 
        Installing       : libslirp-4.4.0-8.el9.x86_64                                                                                                                                                                                                                    9/17 
        Installing       : slirp4netns-1.3.3-1.el9.x86_64                                                                                                                                                                                                                10/17 
        Installing       : fuse-common-3.10.2-9.el9.x86_64                                                                                                                                                                                                               11/17 
        Installing       : fuse3-3.10.2-9.el9.x86_64                                                                                                                                                                                                                     12/17 
        Installing       : fuse-overlayfs-1.15-1.el9.x86_64                                                                                                                                                                                                              13/17 
        Running scriptlet: fuse-overlayfs-1.15-1.el9.x86_64                                                                                                                                                                                                              13/17 
        Installing       : docker-ce-3:28.3.2-1.el9.x86_64                                                                                                                                                                                                               14/17 
        Running scriptlet: docker-ce-3:28.3.2-1.el9.x86_64                                                                                                                                                                                                               14/17 
        Installing       : docker-ce-rootless-extras-28.3.2-1.el9.x86_64                                                                                                                                                                                                 15/17 
        Running scriptlet: docker-ce-rootless-extras-28.3.2-1.el9.x86_64                                                                                                                                                                                                 15/17 
        Cleanup          : selinux-policy-34.1.40-1.el9.noarch                                                                                                                                                                                                           16/17 
        Running scriptlet: selinux-policy-34.1.40-1.el9.noarch                                                                                                                                                                                                           16/17 
        Cleanup          : selinux-policy-targeted-34.1.40-1.el9.noarch                                                                                                                                                                                                  17/17 
        Running scriptlet: selinux-policy-targeted-34.1.40-1.el9.noarch                                                                                                                                                                                                  17/17 
        Running scriptlet: selinux-policy-targeted-38.1.60-1.el9.noarch                                                                                                                                                                                                  17/17 
        Running scriptlet: container-selinux-4:2.237.0-1.el9.noarch                                                                                                                                                                                                      17/17 
        Running scriptlet: selinux-policy-targeted-34.1.40-1.el9.noarch                                                                                                                                                                                                  17/17 
        Verifying        : fuse-common-3.10.2-9.el9.x86_64                                                                                                                                                                                                                1/17 
        Verifying        : container-selinux-4:2.237.0-1.el9.noarch                                                                                                                                                                                                       2/17 
        Verifying        : fuse-overlayfs-1.15-1.el9.x86_64                                                                                                                                                                                                               3/17 
        Verifying        : fuse3-3.10.2-9.el9.x86_64                                                                                                                                                                                                                      4/17 
        Verifying        : fuse3-libs-3.10.2-9.el9.x86_64                                                                                                                                                                                                                 5/17 
        Verifying        : libslirp-4.4.0-8.el9.x86_64                                                                                                                                                                                                                    6/17 
        Verifying        : slirp4netns-1.3.3-1.el9.x86_64                                                                                                                                                                                                                 7/17 
        Verifying        : containerd.io-1.7.27-3.1.el9.x86_64                                                                                                                                                                                                            8/17 
        Verifying        : docker-buildx-plugin-0.25.0-1.el9.x86_64                                                                                                                                                                                                       9/17 
        Verifying        : docker-ce-3:28.3.2-1.el9.x86_64                                                                                                                                                                                                               10/17 
        Verifying        : docker-ce-cli-1:28.3.2-1.el9.x86_64                                                                                                                                                                                                           11/17 
        Verifying        : docker-ce-rootless-extras-28.3.2-1.el9.x86_64                                                                                                                                                                                                 12/17 
        Verifying        : docker-compose-plugin-2.38.2-1.el9.x86_64                                                                                                                                                                                                     13/17 
        Verifying        : selinux-policy-38.1.60-1.el9.noarch                                                                                                                                                                                                           14/17 
        Verifying        : selinux-policy-34.1.40-1.el9.noarch                                                                                                                                                                                                           15/17 
        Verifying        : selinux-policy-targeted-38.1.60-1.el9.noarch                                                                                                                                                                                                  16/17 
        Verifying        : selinux-policy-targeted-34.1.40-1.el9.noarch                                                                                                                                                                                                  17/17 
      
      Upgraded:
        selinux-policy-38.1.60-1.el9.noarch                                                                                            selinux-policy-targeted-38.1.60-1.el9.noarch                                                                                           
      Installed:
        container-selinux-4:2.237.0-1.el9.noarch       containerd.io-1.7.27-3.1.el9.x86_64      docker-buildx-plugin-0.25.0-1.el9.x86_64      docker-ce-3:28.3.2-1.el9.x86_64      docker-ce-cli-1:28.3.2-1.el9.x86_64      docker-ce-rootless-extras-28.3.2-1.el9.x86_64     
        docker-compose-plugin-2.38.2-1.el9.x86_64      fuse-common-3.10.2-9.el9.x86_64          fuse-overlayfs-1.15-1.el9.x86_64              fuse3-3.10.2-9.el9.x86_64            fuse3-libs-3.10.2-9.el9.x86_64           libslirp-4.4.0-8.el9.x86_64                       
        slirp4netns-1.3.3-1.el9.x86_64                
      
      Complete!
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# docker info
      Client: Docker Engine - Community
       Version:    28.3.2
       Context:    default
       Debug Mode: false
       Plugins:
        buildx: Docker Buildx (Docker Inc.)
          Version:  v0.25.0
          Path:     /usr/libexec/docker/cli-plugins/docker-buildx
        compose: Docker Compose (Docker Inc.)
          Version:  v2.38.2
          Path:     /usr/libexec/docker/cli-plugins/docker-compose
      
      Server:
      Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# mkdir -p /etc/docker
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# tee /etc/docker/daemon.json <<-'EOF'
      > {
      >   "registry-mirrors": ["https://p41kkfta.mirror.aliyuncs.com"]
      > }
      > EOF
      {
        "registry-mirrors": ["https://p41kkfta.mirror.aliyuncs.com"]
      }
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# system start docker
      -bash: system: command not found
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# systemctl start docker
      [root@iv-ye033mbbb4qc6il0bmy4 ~]# docker info
      Client: Docker Engine - Community
       Version:    28.3.2
       Context:    default
       Debug Mode: false
       Plugins:
        buildx: Docker Buildx (Docker Inc.)
          Version:  v0.25.0
          Path:     /usr/libexec/docker/cli-plugins/docker-buildx
        compose: Docker Compose (Docker Inc.)
          Version:  v2.38.2
          Path:     /usr/libexec/docker/cli-plugins/docker-compose
      
      Server:
       Containers: 0
        Running: 0
        Paused: 0
        Stopped: 0
       Images: 0
       Server Version: 28.3.2
       Storage Driver: overlay2
        Backing Filesystem: extfs
        Supports d_type: true
        Using metacopy: false
        Native Overlay Diff: true
        userxattr: false
       Logging Driver: json-file
       Cgroup Driver: systemd
       Cgroup Version: 2
       Plugins:
        Volume: local
        Network: bridge host ipvlan macvlan null overlay
        Log: awslogs fluentd gcplogs gelf journald json-file local splunk syslog
       CDI spec directories:
        /etc/cdi
        /var/run/cdi
       Swarm: inactive
       Runtimes: io.containerd.runc.v2 runc
       Default Runtime: runc
       Init Binary: docker-init
       containerd version: 05044ec0a9a75232cad458027ca83437aae3f4da
       runc version: v1.2.5-0-g59923ef
       init version: de40ad0
       Security Options:
        seccomp
         Profile: builtin
        cgroupns
       Kernel Version: 5.14.0-312.el9.x86_64
       Operating System: CentOS Stream 9
       OSType: linux
       Architecture: x86_64
       CPUs: 2
       Total Memory: 1.834GiB
       Name: iv-ye033mbbb4qc6il0bmy4
       ID: 1799b560-5e90-456a-8565-6dc16748b19a
       Docker Root Dir: /var/lib/docker
       Debug Mode: false
       Experimental: false
       Insecure Registries:
        ::1/128
        127.0.0.0/8
       Registry Mirrors:
        https://p41kkfta.mirror.aliyuncs.com/
       Live Restore Enabled: false
      
      ```

      