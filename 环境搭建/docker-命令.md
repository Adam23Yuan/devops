### docker命令

##### docker-compose

1.  命令及说明

   1. 

   2. | 命令                              | 说明                                             |
      | --------------------------------- | ------------------------------------------------ |
      | `docker-compose up -d`            | 后台启动服务                                     |
      | `docker-compose down`             | 停止并删除容器和网络                             |
      | `docker-compose stop`             | 仅停止容器                                       |
      | `docker-compose start`            | 启动已停止的容器                                 |
      | `docker-compose restart`          | 重启容器                                         |
      | `docker-compose logs -f`          | 查看所有服务的实时日志                           |
      | `docker-compose exec 服务名 bash` | 进入容器内部终端，如 `logstash`, `elasticsearch` |