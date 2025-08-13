# minio 文件服务器部署

### 一、docker-compose 部署

1. 创建docker-compose.yml 文件

   ```yaml
   version: "3.8"
   
   services:
     minio:
       image: quay.io/minio/minio:latest
       container_name: minio
       restart: always
       ports:
         - "9000:9000"   # API
         - "9001:9001"   # 控制台
       environment:
         MINIO_ROOT_USER: adminuser            # 自定义用户名
         MINIO_ROOT_PASSWORD: strongpassword123 # 自定义密码 (>=8位)
       volumes:
         - ./data:/data  # 持久化数据
       command: server /data --console-address ":9001"
   
   ```

   说明：

   9000 minio api端口

   9001 minio 控制台端口

1. 启动 和管理 命令

   ```dockerfile
   # 启动后台运行
   docker-compose up -d
   
   # 查看容器状态
   docker-compose ps
   
   # 查看日志
   docker-compose logs -f
   
   # 停止服务
   docker-compose down
   
   ```

2. 运行效果

   ![image-20250730162443115](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20250730162443115.png)

1. 以控制台 创建 buckets
2. postman 上传和下载 文件示例
   1. 填写上传信息，请求方式 put，上传地块，认证信息等
      1. ![image-20250730162718668](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20250730162718668.png)
   2. 上传文件
      1. ![image-20250730162804293](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20250730162804293.png)
   3. 下载文件
      1. ![image-20250730162957443](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20250730162957443.png)