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

      

## 配置文件说明

### logstash.yml

说明：放全局设置：`path.data`、`path.logs`、默认的 `pipeline.workers` / `pipeline.batch.size`、队列类型（`queue.type`）等。它不包含 input/filter/output 的具体管道逻辑，而是控制 Logstash 进程行为的“开关”和默认值。

```yaml
# Settings file in YAML
#
# Settings can be specified either in hierarchical form, e.g.:
#
#   pipeline:
#     batch:
#       size: 125
#       delay: 5
#
# Or as flat keys:
#
#   pipeline.batch.size: 125
#   pipeline.batch.delay: 5
#
# ------------  Node identity ------------
#
# Use a descriptive name for the node:
#
# node.name: test
node.name: logstash-01
#
# If omitted the node name will default to the machine's host name
#
# ------------ Data path ------------------
#
# Which directory should be used by logstash and its plugins
# for any persistent needs. Defaults to LOGSTASH_HOME/data
#
# path.data:
path.data: /opt/logstash/data
#
# ------------ Pipeline Settings --------------
#
# The ID of the pipeline.
#
# pipeline.id: main
#
# Set the number of workers that will, in parallel, execute the filters+outputs
# stage of the pipeline.
#
# This defaults to the number of the host's CPU cores.
#
# pipeline.workers: 2
pipeline.workers: 2
#
# How many events to retrieve from inputs before sending to filters+workers
#
# pipeline.batch.size: 125
pipeline.batch.size: 125
#
# How long to wait in milliseconds while polling for the next event
# before dispatching an undersized batch to filters+outputs
#
# pipeline.batch.delay: 50
#
# Force Logstash to exit during shutdown even if there are still inflight
# events in memory. By default, logstash will refuse to quit until all
# received events have been pushed to the outputs.
#
# WARNING: Enabling this can lead to data loss during shutdown
#
# pipeline.unsafe_shutdown: false
#
# Set the pipeline event ordering. Options are "auto" (the default), "true" or "false".
# "auto" automatically enables ordering if the 'pipeline.workers' setting
# is also set to '1', and disables otherwise.
# "true" enforces ordering on the pipeline and prevent logstash from starting
# if there are multiple workers.
# "false" disables any extra processing necessary for preserving ordering.
#
# pipeline.ordered: auto
#
# Sets the pipeline's default value for `ecs_compatibility`, a setting that is
# available to plugins that implement an ECS Compatibility mode for use with
# the Elastic Common Schema.
# Possible values are:
# - disabled
# - v1
# - v8 (default)
# Pipelines defined before Logstash 8 operated without ECS in mind. To ensure a
# migrated pipeline continues to operate as it did before your upgrade, opt-OUT
# of ECS for the individual pipeline in its `pipelines.yml` definition. Setting
# it here will set the default for _all_ pipelines, including new ones.
#
# pipeline.ecs_compatibility: v8
#
# ------------ Pipeline Configuration Settings --------------
#
# Where to fetch the pipeline configuration for the main pipeline
#
# path.config:
#
# Pipeline configuration string for the main pipeline
#
# config.string:
#
# At startup, test if the configuration is valid and exit (dry run)
#
# config.test_and_exit: false
#
# Periodically check if the configuration has changed and reload the pipeline
# This can also be triggered manually through the SIGHUP signal
#
# config.reload.automatic: false
#
# How often to check if the pipeline configuration has changed (in seconds)
# Note that the unit value (s) is required. Values without a qualifier (e.g. 60)
# are treated as nanoseconds.
# Setting the interval this way is not recommended and might change in later versions.
#
# config.reload.interval: 3s
#
# Show fully compiled configuration as debug log message
# NOTE: --log.level must be 'debug'
#
# config.debug: false
#
# When enabled, process escaped characters such as \n and \" in strings in the
# pipeline configuration files.
#
# config.support_escapes: false
#
# ------------ API Settings -------------
# Define settings related to the HTTP API here.
#
# The HTTP API is enabled by default. It can be disabled, but features that rely
# on it will not work as intended.
#
# api.enabled: true
#
# By default, the HTTP API is not secured and is therefore bound to only the
# host's loopback interface, ensuring that it is not accessible to the rest of
# the network.
# When secured with SSL and Basic Auth, the API is bound to _all_ interfaces
# unless configured otherwise.
#
# api.http.host: 127.0.0.1
api.http.host: 0.0.0.0
#
# The HTTP API web server will listen on an available port from the given range.
# Values can be specified as a single port (e.g., `9600`), or an inclusive range
# of ports (e.g., `9600-9700`).
#
# api.http.port: 9600-9700
api.http.port: 9600-9700
#
# The HTTP API includes a customizable "environment" value in its response,
# which can be configured here.
#
# api.environment: "production"
#
# The HTTP API can be secured with SSL (TLS). To do so, you will need to provide
# the path to a password-protected keystore in p12 or jks format, along with credentials.
#
# api.ssl.enabled: false
# api.ssl.keystore.path: /path/to/keystore.jks
# api.ssl.keystore.password: "y0uRp4$$w0rD"
#
# The availability of SSL/TLS protocols depends on the JVM version. Certain protocols are
# disabled by default and need to be enabled manually by changing `jdk.tls.disabledAlgorithms`
# in the $JDK_HOME/conf/security/java.security configuration file.
#
# api.ssl.supported_protocols: [TLSv1.2,TLSv1.3]
#
# The HTTP API can be configured to require authentication. Acceptable values are
#  - `none`:  no auth is required (default)
#  - `basic`: clients must authenticate with HTTP Basic auth, as configured
#             with `api.auth.basic.*` options below
# api.auth.type: none
#
# When configured with `api.auth.type` `basic`, you must provide the credentials
# that requests will be validated against. Usage of Environment or Keystore
# variable replacements is encouraged (such as the value `"${HTTP_PASS}"`, which
# resolves to the value stored in the keystore's `HTTP_PASS` variable if present
# or the same variable from the environment)
#
# api.auth.basic.username: "logstash-user"
# api.auth.basic.password: "s3cUreP4$$w0rD"
#
# When setting `api.auth.basic.password`, the password should meet
# the default password policy requirements.
# The default password policy requires non-empty minimum 8 char string that
# includes a digit, upper case letter and lower case letter.
# Policy mode sets Logstash to WARN or ERROR when HTTP authentication password doesn't
# meet the password policy requirements.
# The default is WARN. Setting to ERROR enforces stronger passwords (recommended).
#
# api.auth.basic.password_policy.mode: WARN
#
# ------------ Module Settings ---------------
# Define modules here.  Modules definitions must be defined as an array.
# The simple way to see this is to prepend each `name` with a `-`, and keep
# all associated variables under the `name` they are associated with, and
# above the next, like this:
#
# modules:
#   - name: MODULE_NAME
#     var.PLUGINTYPE1.PLUGINNAME1.KEY1: VALUE
#     var.PLUGINTYPE1.PLUGINNAME1.KEY2: VALUE
#     var.PLUGINTYPE2.PLUGINNAME1.KEY1: VALUE
#     var.PLUGINTYPE3.PLUGINNAME3.KEY1: VALUE
#
# Module variable names must be in the format of
#
# var.PLUGIN_TYPE.PLUGIN_NAME.KEY
#
# modules:
#
# ------------ Cloud Settings ---------------
# Define Elastic Cloud settings here.
# Format of cloud.id is a base64 value e.g. dXMtZWFzdC0xLmF3cy5mb3VuZC5pbyRub3RhcmVhbCRpZGVudGlmaWVy
# and it may have an label prefix e.g. staging:dXMtZ...
# This will overwrite 'var.elasticsearch.hosts' and 'var.kibana.host'
# cloud.id: <identifier>
#
# Format of cloud.auth is: <user>:<pass>
# This is optional
# If supplied this will overwrite 'var.elasticsearch.username' and 'var.elasticsearch.password'
# If supplied this will overwrite 'var.kibana.username' and 'var.kibana.password'
# cloud.auth: elastic:<password>
#
# ------------ Queuing Settings --------------
#
# Internal queuing model, "memory" for legacy in-memory based queuing and
# "persisted" for disk-based acked queueing. Defaults is memory
#
# queue.type: memory
queue.type: persisted
#
# If `queue.type: persisted`, the directory path where the pipeline data files will be stored.
# Each pipeline will group its PQ files in a subdirectory matching its `pipeline.id`.
# Default is path.data/queue.
#
# path.queue:
#
# If using queue.type: persisted, the page data files size. The queue data consists of
# append-only data files separated into pages. Default is 64mb
#
# queue.page_capacity: 64mb
#
# If using queue.type: persisted, the maximum number of unread events in the queue.
# Default is 0 (unlimited)
#
# queue.max_events: 0
#
# If using queue.type: persisted, the total capacity of the queue in number of bytes.
# If you would like more unacked events to be buffered in Logstash, you can increase the
# capacity using this setting. Please make sure your disk drive has capacity greater than
# the size specified here. If both max_bytes and max_events are specified, Logstash will pick
# whichever criteria is reached first
# Default is 1024mb or 1gb
#
# queue.max_bytes: 1024mb
#
# If using queue.type: persisted, the maximum number of acked events before forcing a checkpoint
# Default is 1024, 0 for unlimited
#
# queue.checkpoint.acks: 1024
#
# If using queue.type: persisted, the maximum number of written events before forcing a checkpoint
# Default is 1024, 0 for unlimited
#
# queue.checkpoint.writes: 1024
#
# If using queue.type: persisted, the interval in milliseconds when a checkpoint is forced on the head page
# Default is 1000, 0 for no periodic checkpoint.
#
# queue.checkpoint.interval: 1000
#
# ------------ Dead-Letter Queue Settings --------------
# Flag to turn on dead-letter queue.
#
# dead_letter_queue.enable: false

# If using dead_letter_queue.enable: true, the maximum size of each dead letter queue. Entries
# will be dropped if they would increase the size of the dead letter queue beyond this setting.
# Default is 1024mb
# dead_letter_queue.max_bytes: 1024mb

# If using dead_letter_queue.enable: true, the interval in milliseconds where if no further events eligible for the DLQ
# have been created, a dead letter queue file will be written. A low value here will mean that more, smaller, queue files
# may be written, while a larger value will introduce more latency between items being "written" to the dead letter queue, and
# being available to be read by the dead_letter_queue input when items are written infrequently.
# Default is 5000.
#
# dead_letter_queue.flush_interval: 5000

# If using dead_letter_queue.enable: true, controls which entries should be dropped to avoid exceeding the size limit.
# Set the value to `drop_newer` (default) to stop accepting new events that would push the DLQ size over the limit.
# Set the value to `drop_older` to remove queue pages containing the oldest events to make space for new ones.
#
# dead_letter_queue.storage_policy: drop_newer

# If using dead_letter_queue.enable: true, the interval that events have to be considered valid. After the interval has
# expired the events could be automatically deleted from the DLQ.
# The interval could be expressed in days, hours, minutes or seconds, using as postfix notation like 5d,
# to represent a five days interval.
# The available units are respectively d, h, m, s for day, hours, minutes and seconds.
# If not specified then the DLQ doesn't use any age policy for cleaning events.
#
# dead_letter_queue.retain.age: 1d

# If using dead_letter_queue.enable: true, the directory path where the data files will be stored.
# Default is path.data/dead_letter_queue
#
# path.dead_letter_queue:
#
# ------------ Debugging Settings --------------
#
# Options for log.level:
#   * fatal
#   * error
#   * warn
#   * info (default)
#   * debug
#   * trace
# log.level: info
#
# Options for log.format:
#   * plain (default)
#   * json
#
# log.format: plain
# log.format.json.fix_duplicate_message_fields: false
#
# path.logs:
path.logs: /opt/logstash/logs
#
# ------------ Other Settings --------------
#
# Allow or block running Logstash as superuser (default: true)
# allow_superuser: false
#
# Where to find custom plugins
# path.plugins: []
#
# Flag to output log lines of each pipeline in its separate log file. Each log filename contains the pipeline.name
# Default is false
# pipeline.separate_logs: false
#
# Determine where to allocate memory buffers, for plugins that leverage them.
# Default to direct, optionally can be switched to heap to select Java heap space.
# pipeline.buffer.type: heap
#
# ------------ X-Pack Settings (not applicable for OSS build)--------------
#
# X-Pack Monitoring
# https://www.elastic.co/guide/en/logstash/current/monitoring-logstash.html
#xpack.monitoring.enabled: false
#xpack.monitoring.elasticsearch.username: logstash_system
#xpack.monitoring.elasticsearch.password: password
#xpack.monitoring.elasticsearch.proxy: ["http://proxy:port"]
#xpack.monitoring.elasticsearch.hosts: ["https://es1:9200", "https://es2:9200"]
# an alternative to hosts + username/password settings is to use cloud_id/cloud_auth
#xpack.monitoring.elasticsearch.cloud_id: monitoring_cluster_id:xxxxxxxxxx
#xpack.monitoring.elasticsearch.cloud_auth: logstash_system:password
# another authentication alternative is to use an Elasticsearch API key
#xpack.monitoring.elasticsearch.api_key: "id:api_key"
#xpack.monitoring.elasticsearch.ssl.certificate_authority: "/path/to/ca.crt"
#xpack.monitoring.elasticsearch.ssl.ca_trusted_fingerprint: xxxxxxxxxx
#xpack.monitoring.elasticsearch.ssl.truststore.path: path/to/file
#xpack.monitoring.elasticsearch.ssl.truststore.password: password
# use either keystore.path/keystore.password or certificate/key configurations
#xpack.monitoring.elasticsearch.ssl.keystore.path: /path/to/file
#xpack.monitoring.elasticsearch.ssl.keystore.password: password
#xpack.monitoring.elasticsearch.ssl.certificate: /path/to/file
#xpack.monitoring.elasticsearch.ssl.key: /path/to/key
#xpack.monitoring.elasticsearch.ssl.verification_mode: full
#xpack.monitoring.elasticsearch.ssl.cipher_suites: []
#xpack.monitoring.elasticsearch.sniffing: false
#xpack.monitoring.collection.interval: 10s
#xpack.monitoring.collection.pipeline.details.enabled: true
#
# X-Pack Management
# https://www.elastic.co/guide/en/logstash/current/logstash-centralized-pipeline-management.html
#xpack.management.enabled: false
#xpack.management.pipeline.id: ["main", "apache_logs"]
#xpack.management.elasticsearch.username: logstash_admin_user
#xpack.management.elasticsearch.password: password
#xpack.management.elasticsearch.proxy: ["http://proxy:port"]
#xpack.management.elasticsearch.hosts: ["https://es1:9200", "https://es2:9200"]
# an alternative to hosts + username/password settings is to use cloud_id/cloud_auth
#xpack.management.elasticsearch.cloud_id: management_cluster_id:xxxxxxxxxx
#xpack.management.elasticsearch.cloud_auth: logstash_admin_user:password
# another authentication alternative is to use an Elasticsearch API key
#xpack.management.elasticsearch.api_key: "id:api_key"
#xpack.management.elasticsearch.ssl.ca_trusted_fingerprint: xxxxxxxxxx
#xpack.management.elasticsearch.ssl.certificate_authority: "/path/to/ca.crt"
#xpack.management.elasticsearch.ssl.truststore.path: /path/to/file
#xpack.management.elasticsearch.ssl.truststore.password: password
# use either keystore.path/keystore.password or certificate/key configurations
#xpack.management.elasticsearch.ssl.keystore.path: /path/to/file
#xpack.management.elasticsearch.ssl.keystore.password: password
#xpack.management.elasticsearch.ssl.certificate: /path/to/file
#xpack.management.elasticsearch.ssl.key: /path/to/certificate_key_file
#xpack.management.elasticsearch.ssl.cipher_suites: []
#xpack.management.elasticsearch.ssl.verification_mode: full
#xpack.management.elasticsearch.sniffing: false
#xpack.management.logstash.poll_interval: 5s

# X-Pack GeoIP Database Management
# https://www.elastic.co/guide/en/logstash/current/plugins-filters-geoip.html#plugins-filters-geoip-manage_update
#xpack.geoip.downloader.enabled: true
#xpack.geoip.downloader.endpoint: "https://geoip.elastic.co/v1/database"

```



### pipelines.yml

当你需要运行多个相互独立的 pipeline（比如不同来源→不同去向）时，用 `pipelines.yml` 定义若干 pipeline，每个 pipeline 指定 `pipeline.id`、`path.config`（以及可选的 `pipeline.workers`、`pipeline.batch.size` 覆盖全局默认）

```yaml
# List of pipelines to be loaded by Logstash
#
# This document must be a list of dictionaries/hashes, where the keys/values are pipeline settings.
# Default values for omitted settings are read from the `logstash.yml` file.
# When declaring multiple pipelines, each MUST have its own `pipeline.id`.
#
# Example of two pipelines:
#
# - pipeline.id: test
#   pipeline.workers: 1
#   pipeline.batch.size: 1
#   config.string: "input { generator {} } filter { sleep { time => 1 } } output { stdout { codec => dots } }"
# - pipeline.id: another_test
#   queue.type: persisted
#   path.config: "/tmp/logstash/*.config"
#
- pipeline.id: emqx_cloud_rabbitmq_to_es
  path.config: "/opt/logstash/config/emqx_cloud_rabbitmq_to_es.conf"
  pipeline.workers: 2
  pipeline.batch.size: 125

# Available options:
#
#   # name of the pipeline
#   pipeline.id: mylogs
#
#   # The configuration string to be used by this pipeline
#   config.string: "input { generator {} } filter { sleep { time => 1 } } output { stdout { codec => dots } }"
#
#   # The path from where to read the configuration text
#   path.config: "/etc/conf.d/logstash/myconfig.cfg"
#
#   # How many worker threads execute the Filters+Outputs stage of the pipeline
#   pipeline.workers: 1 (actually defaults to number of CPUs)
#
#   # How many events to retrieve from inputs before sending to filters+workers
#   pipeline.batch.size: 125
#
#   # How long to wait in milliseconds while polling for the next event
#   # before dispatching an undersized batch to filters+outputs
#   pipeline.batch.delay: 50
#
#   Set the pipeline event ordering. Options are "auto" (the default), "true" # #   or "false".
#   "auto" automatically enables ordering if the 'pipeline.workers' setting
#   is also set to '1', and disables otherwise.
#   "true" enforces ordering on a pipeline and prevents logstash from starting
#   a pipeline with multiple workers allocated.
#   "false" disable any extra processing necessary for preserving ordering.
#
#   pipeline.ordered: auto
#
#   # Internal queuing model, "memory" for legacy in-memory based queuing and
#   # "persisted" for disk-based acked queueing. Defaults is memory
#   queue.type: memory
#
#   # If using queue.type: persisted, the page data files size. The queue data consists of
#   # append-only data files separated into pages. Default is 64mb
#   queue.page_capacity: 64mb
#
#   # If using queue.type: persisted, the maximum number of unread events in the queue.
#   # Default is 0 (unlimited)
#   queue.max_events: 0
#
#   # If using queue.type: persisted, the total capacity of the queue in number of bytes.
#   # Default is 1024mb or 1gb
#   queue.max_bytes: 1024mb
#
#   # If using queue.type: persisted, the maximum number of acked events before forcing a checkpoint
#   # Default is 1024, 0 for unlimited
#   queue.checkpoint.acks: 1024
#
#   # If using queue.type: persisted, the maximum number of written events before forcing a checkpoint
#   # Default is 1024, 0 for unlimited
#   queue.checkpoint.writes: 1024
#
#   # If using queue.type: persisted, the interval in milliseconds when a checkpoint is forced on the head page
#   # Default is 1000, 0 for no periodic checkpoint.
#   queue.checkpoint.interval: 1000
#
#   # Enable Dead Letter Queueing for this pipeline.
#   dead_letter_queue.enable: false
#
#   If using dead_letter_queue.enable: true, the maximum size of dead letter queue for this pipeline. Entries
#   will be dropped if they would increase the size of the dead letter queue beyond this setting.
#   Default is 1024mb
#   dead_letter_queue.max_bytes: 1024mb
#
#   If using dead_letter_queue.enable: true, the interval in milliseconds where if no further events eligible for the DLQ
#   have been created, a dead letter queue file will be written. A low value here will mean that more, smaller, queue files
#   may be written, while a larger value will introduce more latency between items being "written" to the dead letter queue, and
#   being available to be read by the dead_letter_queue input when items are are written infrequently.
#   Default is 5000.
#
#   dead_letter_queue.flush_interval: 5000

#   If using dead_letter_queue.enable: true, controls which entries should be dropped to avoid exceeding the size limit.
#   Set the value to `drop_newer` (default) to stop accepting new events that would push the DLQ size over the limit.
#   Set the value to `drop_older` to remove queue pages containing the oldest events to make space for new ones.
#
#   dead_letter_queue.storage_policy: drop_newer

#   If using dead_letter_queue.enable: true, the interval that events have to be considered valid. After the interval has
#   expired the events could be automatically deleted from the DLQ.
#   The interval could be expressed in days, hours, minutes or seconds, using as postfix notation like 5d,
#   to represent a five days interval.
#   The available units are respectively d, h, m, s for day, hours, minutes and seconds.
#   If not specified then the DLQ doesn't use any age policy for cleaning events.
#
#   dead_letter_queue.retain.age: 1d

#
#   If using dead_letter_queue.enable: true, the directory path where the data files will be stored.
#   Default is path.data/dead_letter_queue
#
#   path.dead_letter_queue:

```



### *.conf（pipeline 配置文件，位于 `path.config` 指定目录）

真正的 ETL 逻辑在这里：`input { ... } filter { ... } output { ... }`。在 **单 pipeline 模式**（默认）中，Logstash 会把 `path.config` 目录下的所有 `.conf` 文件 **合并** 后当成 **同一个 pipeline** 来运行；在 **多 pipeline 模式**，每个 pipeline 会单独读取它 `path.config` 指定的文件或目录，形成各自的 pipeline。

```bash
# Sample Logstash configuration for creating a simple
# Beats -> Logstash -> Elasticsearch pipeline.
# /etc/logstash/pipelines/rabbit_to_es.conf
input {
  rabbitmq {
    host => "39.105.121.219"       # RabbitMQ 主机
    port => 5672
    user => "rabbitadmin"
    password => "jsy919"
    vhost => "/"                         # RabbitMQ vhost
    queue => "emqx_cloud_message_to_es"              # 目标队列名
    durable => true
    # prefetch_count 控制从 rabbit 一次拉取多少未 ack 的消息（配合 pipeline.workers 调整）
    prefetch_count => 50
    ack => true                          # 启用手动 ack（成功处理后 ack）
    codec => "json"                      # 如果消息是 JSON，建议用 json codec
    # include_headers => true            # 如需访问 rabbitmq header（可选）
  }
}

filter {
  # 当 codec => "json" 时，消息体已经被解析到字段中。
  # 下面示例做几个常见处理：
  # 1) 如果消息未能解析为 JSON，会打上 _jsonparsefailure 的 tag
  if "_jsonparsefailure" in [tags] {
    mutate { add_tag => ["_parse_failure"] }
  }

  # 2) 如果消息里有 timestamp 字段，解析为 @timestamp
  if [timestamp] {
    date {
      match => ["timestamp", "ISO8601", "yyyy-MM-dd HH:mm:ss", "UNIX"] 
      target => "@timestamp"
      remove_field => ["timestamp"]
    }
  }

  # 3) 将目标索引放到 @metadata，方便 output 使用而不进入 ES 文档
  mutate {
    add_field => { "[@metadata][target_index]" => "emqx-cloud-raw-message" }
  }

  # 可在此处添加 grok/mutate/kv 等解析逻辑
}

output {
  # 解析失败单独写入一个失败索引（方便排查）
  if "_parse_failure" in [tags] {
    elasticsearch {
      hosts => ["http://82.156.159.151:9200"]
      user => "elastic"
      password => "H9UqA+JN__nbvYHuku=B"
      index => "emqx-cloud-raw-message-parse-failed-%{+YYYY.MM.dd}"
      ilm_enabled => false
    }
  } else {
    elasticsearch {
      hosts => ["http://82.156.159.151:9200/"]  # ES 地址
      user => "elastic"
      password => "H9UqA+JN__nbvYHuku=B"
      # index 名称：使用 metadata 中的 target_index 并按天分
      index => "%{[@metadata][target_index]}-%{+YYYY.MM.dd}"
      # 如果消息携带唯一 id，建议设置 document_id，避免重复写入：
      # document_id => "%{[id]}"
      ilm_enabled => false         # 如果你不使用 ILM，可以关闭；使用 ILM 则配置模板/策略
      # manage_template => true    # 根据是否需要自动管理模板来设置
    }
  }
}

```

