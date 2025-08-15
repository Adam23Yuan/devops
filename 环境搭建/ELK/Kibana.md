# ELK 生态圈

## Kibana 8.19.2  tar.gz 方式


1. 安装kibana

   1. ```bash
      # 下载对应版本的安装包
      wget https://artifacts.elastic.co/downloads/kibana/kibana-8.19.2-linux-x86_64.tar.gz
      tar -xzf kibana-8.19.2-linux-x86_64.tar.gz
      mv kibana-8.19.2 kibana
      # 添加 运行用户
      useradd -r -s /bin/false kibana
      # 查看刚添加的用户
      [root@iZ2zeddamay9amikauf269Z opt]# id kibana
      uid=991(kibana) gid=987(kibana) 组=987(kibana)
      # 修改运行目录的权限
      chown -R kibana:kibana /opt/kibana
      ```

2. 修改配置文件

   1. ```yaml
      # 修改 Kibana 节点绑定的 IP和端口
      # 修改 es结点地址
      # 修改 es的账号和密码
      # 修改 es的公钥文件路径
      # 类似 以下配置文件
      
      # For more configuration options see the configuration guide for Kibana in
      # https://www.elastic.co/guide/index.html
      
      # =================== System: Kibana Server ===================
      # Kibana is served by a back end server. This setting specifies the port to use.
      #server.port: 5601
      server.port: 5601
      
      # Specifies the address to which the Kibana server will bind. IP addresses and host names are both valid values.
      # The default is 'localhost', which usually means remote machines will not be able to connect.
      # To allow connections from remote users, set this parameter to a non-loopback address.
      #server.host: "localhost"
      server.host: "0.0.0.0"
      
      # Enables you to specify a path to mount Kibana at if you are running behind a proxy.
      # Use the `server.rewriteBasePath` setting to tell Kibana if it should remove the basePath
      # from requests it receives, and to prevent a deprecation warning at startup.
      # This setting cannot end in a slash.
      #server.basePath: ""
      
      # Specifies whether Kibana should rewrite requests that are prefixed with
      # `server.basePath` or require that they are rewritten by your reverse proxy.
      # Defaults to `false`.
      #server.rewriteBasePath: false
      
      # Specifies the public URL at which Kibana is available for end users. If
      # `server.basePath` is configured this URL should end with the same basePath.
      #server.publicBaseUrl: ""
      
      # The maximum payload size in bytes for incoming server requests.
      #server.maxPayload: 1048576
      
      # The Kibana server's name. This is used for display purposes.
      #server.name: "your-hostname"
      
      # =================== System: Kibana Server (Optional) ===================
      # Enables SSL and paths to the PEM-format SSL certificate and SSL key files, respectively.
      # These settings enable SSL for outgoing requests from the Kibana server to the browser.
      #server.ssl.enabled: false
      #server.ssl.certificate: /path/to/your/server.crt
      #server.ssl.key: /path/to/your/server.key
      
      # =================== System: Elasticsearch ===================
      # The URLs of the Elasticsearch instances to use for all your queries.
      #elasticsearch.hosts: ["http://localhost:9200"]
      elasticsearch.hosts: ["http://IP:9200"]
      
      # If your Elasticsearch is protected with basic authentication, these settings provide
      # the username and password that the Kibana server uses to perform maintenance on the Kibana
      # index at startup. Your Kibana users still need to authenticate with Elasticsearch, which
      # is proxied through the Kibana server.
      #elasticsearch.username: "kibana_system"
      #elasticsearch.password: "pass"#
      elasticsearch.username: "kibana_system"
      elasticsearch.password: "*****"
      
      # Kibana can also authenticate to Elasticsearch via "service account tokens".
      # Service account tokens are Bearer style tokens that replace the traditional username/password based configuration.
      # Use this token instead of a username/password.
      # elasticsearch.serviceAccountToken: "my_token"
      
      # Time in milliseconds to wait for Elasticsearch to respond to pings. Defaults to the value of
      # the elasticsearch.requestTimeout setting.
      #elasticsearch.pingTimeout: 1500
      
      # Time in milliseconds to wait for responses from the back end or Elasticsearch. This value
      # must be a positive integer.
      #elasticsearch.requestTimeout: 30000
      
      # The maximum number of sockets that can be used for communications with elasticsearch.
      # Defaults to `800`.
      #elasticsearch.maxSockets: 1024
      
      # Specifies whether Kibana should use compression for communications with elasticsearch
      # Defaults to `false`.
      #elasticsearch.compression: false
      
      # List of Kibana client-side headers to send to Elasticsearch. To send *no* client-side
      # headers, set this value to [] (an empty list).
      #elasticsearch.requestHeadersWhitelist: [ authorization ]
      
      # Header names and values that are sent to Elasticsearch. Any custom headers cannot be overwritten
      # by client-side headers, regardless of the elasticsearch.requestHeadersWhitelist configuration.
      #elasticsearch.customHeaders: {}
      
      # Time in milliseconds for Elasticsearch to wait for responses from shards. Set to 0 to disable.
      #elasticsearch.shardTimeout: 30000
      
      # =================== System: Elasticsearch (Optional) ===================
      # These files are used to verify the identity of Kibana to Elasticsearch and are required when
      # xpack.security.http.ssl.client_authentication in Elasticsearch is set to required.
      #elasticsearch.ssl.certificate: /path/to/your/client.crt
      #elasticsearch.ssl.key: /path/to/your/client.key
      
      # Enables you to specify a path to the PEM file for the certificate
      # authority for your Elasticsearch instance.
      #elasticsearch.ssl.certificateAuthorities: [ "/path/to/your/CA.pem" ]
      elasticsearch.ssl.certificateAuthorities: [ "/opt/elasticsearch/config/certs/http_ca.crt" ]
      # To disregard the validity of SSL certificates, change this setting's value to 'none'.
      #elasticsearch.ssl.verificationMode: full
      
      # =================== System: Logging ===================
      # Set the value of this setting to off to suppress all logging output, or to debug to log everything. Defaults to 'info'
      #logging.root.level: debug
      
      # Enables you to specify a file where Kibana stores log output.
      #logging.appenders.default:
      #  type: file
      #  fileName: /var/logs/kibana.log
      #  layout:
      #    type: json
      
      # Example with size based log rotation
      #logging.appenders.default:
      #  type: rolling-file
      #  fileName: /var/logs/kibana.log
      #  policy:
      #    type: size-limit
      #    size: 256mb
      #  strategy:
      #    type: numeric
      #    max: 10
      #  layout:
      #    type: json
      
      # Logs queries sent to Elasticsearch.
      #logging.loggers:
      #  - name: elasticsearch.query
      #    level: debug
      
      # Logs http responses.
      #logging.loggers:
      #  - name: http.server.response
      #    level: debug
      
      # Logs system usage information.
      #logging.loggers:
      #  - name: metrics.ops
      #    level: debug
      
      # Enables debug logging on the browser (dev console)
      #logging.browser.root:
      #  level: debug
      
      # =================== System: Other ===================
      # The path where Kibana stores persistent data not saved in Elasticsearch. Defaults to data
      #path.data: data
      
      # Specifies the path where Kibana creates the process ID file.
      #pid.file: /run/kibana/kibana.pid
      
      # Set the interval in milliseconds to sample system and process performance
      # metrics. Minimum is 100ms. Defaults to 5000ms.
      #ops.interval: 5000
      
      # Specifies locale to be used for all localizable strings, dates and number formats.
      # Supported languages are the following: English (default) "en", Chinese "zh-CN", Japanese "ja-JP", French "fr-FR".
      #i18n.locale: "en"
      
      # =================== Frequently used (Optional)===================
      
      # =================== Saved Objects: Migrations ===================
      # Saved object migrations run at startup. If you run into migration-related issues, you might need to adjust these settings.
      
      # The number of documents migrated at a time.
      # If Kibana can't start up or upgrade due to an Elasticsearch `circuit_breaking_exception`,
      # use a smaller batchSize value to reduce the memory pressure. Defaults to 1000 objects per batch.
      #migrations.batchSize: 1000
      
      # The maximum payload size for indexing batches of upgraded saved objects.
      # To avoid migrations failing due to a 413 Request Entity Too Large response from Elasticsearch.
      # This value should be lower than or equal to your Elasticsearch cluster’s `http.max_content_length`
      # configuration option. Default: 100mb
      #migrations.maxBatchSizeBytes: 100mb
      
      # The number of times to retry temporary migration failures. Increase the setting
      # if migrations fail frequently with a message such as `Unable to complete the [...] step after
      # 15 attempts, terminating`. Defaults to 15
      #migrations.retryAttempts: 15
      
      # =================== Search Autocomplete ===================
      # Time in milliseconds to wait for autocomplete suggestions from Elasticsearch.
      # This value must be a whole number greater than zero. Defaults to 1000ms
      #unifiedSearch.autocomplete.valueSuggestions.timeout: 1000
      
      # Maximum number of documents loaded by each shard to generate autocomplete suggestions.
      # This value must be a whole number greater than zero. Defaults to 100_000
      #unifiedSearch.autocomplete.valueSuggestions.terminateAfter: 100000
      
      ```

3. 配置开机启动

   1. ```bash
      touch /etc/systemd/system/kibana.service
      [Unit]
      Description=Kibana
      After=network.target
      
      [Service]
      Type=simple
      User=kibana
      Group=kibana
      ExecStart=/opt/kibana/bin/kibana
      Restart=on-failure
      LimitNOFILE=65536
      
      [Install]
      WantedBy=multi-user.target
      
      # 输入:wq 保存并退出
      # 启用并设置开机启动 Kibana
      # 重新加载开机启动服务
      sudo systemctl daemon-reload
      # 开启开机启动配置
      sudo systemctl enable Kibana
      # 启动 logstash
      sudo systemctl start Kibana
      # 查看 logstash 运行状态
      sudo systemctl status Kibana
      ```

4. Kibana Console示例

   1. ```shell
      # Welcome to the Dev Tools Console!
      #
      # You can use Console to explore the Elasticsearch API. See the Elasticsearch API reference to learn more:
      # https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html
      #
      # Here are a few examples to get you started.
      
      PUT doc_index
      {
        "settings": {
          "number_of_shards": 1,
          "number_of_replicas": 0
        },
        "mappings": {
          "properties": {
            "name": { "type": "text" },
            "age": { "type": "integer" },
            "created_at": { "type": "date" }
          }
        }
      }
      
      
      POST doc_index/_doc/1
      {
        "name": "Alice",
        "age": 28,
        "created_at": "2025-08-15T10:00:00"
      }
      
      POST doc_index/_doc
      {
        "name": "Bob",
        "age": 32,
        "created_at": "2025-08-15T10:05:00"
      }
      
      GET doc_index/_search
      {
        "query": {
          "match_all": {}
        }
      }
      
      GET doc_index/_search
      {
        "query": {
          "range": {
            "age": { "gt": 30 }
          }
        }
      }
      
      
      GET doc_index/_search
      {
        "query": {
          "match": { "name": "Alice" }
        }
      }
      ```

      
