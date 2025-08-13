## Centos服务器上 安装Filebeat

### 安装步骤

1. 导入GPG公钥

   1. ```
      sudo rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch
      ```

2. 配置YUM仓库(一次性复制下面整个内容)

   1. ```bash
      sudo tee /etc/yum.repos.d/elastic.repo > /dev/null <<EOF
      [elastic-8.x]
      name=Elastic repository for 8.x packages
      baseurl=https://artifacts.elastic.co/packages/8.x/yum
      gpgcheck=1
      gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
      enabled=1
      autorefresh=1
      type=rpm-md
      EOF
      ```

3. 安装Filebeat

   1. ```bash
      sudo yum install filebeat -y
      
      ```

4. 修改配置文件

   1. ```bash
      sudo vi /etc/filebeat/filebeat.yml
      
      ```

   2. ```yml
      filebeat.inputs:
        - type: log
          enabled: true
          paths:
            - /var/log/*.log         # 改为你要采集的日志路径
      
      output.logstash:
        hosts: ["192.168.1.100:5044"]   # 改成你的 Logstash 地址和端口
      
      ```

5. 启动Filebeat

   1. ```bash
      # 开启开机启动
      sudo systemctl enable filebeat
      # 启动Filebeat
      sudo systemctl start filebeat
      # 检查运行状态
      sudo systemctl status filebeat
      # 测试连接
      sudo filebeat test output
      
      ```

6. 命令速查表

   1. 

   2. | 功能 | 命令 |
      | ---- | ---- |

      | 重启 Filebeat | `sudo systemctl restart filebeat` |
      | ------------- | --------------------------------- |

      | 停止 Filebeat | `sudo systemctl stop filebeat` |
      | ------------- | ------------------------------ |

      | 查看日志 | `sudo journalctl -u filebeat -f` |
      | -------- | -------------------------------- |

      | 测试配置是否正确 | `sudo filebeat test config` |
      | ---------------- | --------------------------- |

