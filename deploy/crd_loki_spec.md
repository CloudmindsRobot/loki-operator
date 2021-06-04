## LokiSpec

**LokiSpec**定义了部署Loki的行为和配置.

| VARIABLE NAME  | TYPE               | REQUIRED | DESCRIPTION                               |
| -------------- | ------------------ | -------- | ----------------------------------------- |
| version        | string             | yes      | Loki版本，用于镜像的TAG                   |
| multitenancy   | bool               | yes      | Loki多租户                                |
| servicemonitor | bool               | yes      | 启用监控指标，依赖prometheus-operator服务 |
| service        | *serviceSpec       | yes      | Loki服务架构                              |
| cacheconfig    | *cacheconfigSpec   | yes      | Loki缓存配置                              |
| ruler          | *rulerSpec         | yes      | Loki Ruler组件配置                        |
| shemaconfig    | *shemaconfigSpec   | yes      | Index、Chunks的持久化类型                 |
| storageconfig  | *storageconfigSpec | yes         | 数据持久化类型配置                        |



**serviceSpec**

| VARIABLE NAME | TYPE         | REQUIRED | DESCRIPTION                                                  |
| ------------- | ------------ | -------- | ------------------------------------------------------------ |
| mode          | string       | yes      | Loki运行模式，支持单实例和集群两种类型  <br>single / cluster |
| single        | *single      | yes      | Loki单实例配置                                               |
| cluster       | *clusterSpec | yes      | Loki集群配置                                                 |

**clusterSpec**

| VARIABLE NAME      | TYPE           | REQUIRED | DESCRIPTION                                                  |
| ------------------ | -------------- | -------- | ------------------------------------------------------------ |
| type               | string         | yes      | Loki集群类型，支持HA和微服务两种类型  <br>ha / microservice  |
| replication_factor | int            | yes      | 复制因子                                                     |
| ring               | *ring          | yes      | 哈希环类型，当前只支持memberlist                             |
| gateway            | *gateway       | yes      | 网关服务配置                                                 |
| frontend           | *frontend      | yes      | frontend服务配置                                             |
| loki               | *loki          | yes      | Loki配置（主要为ha模式下的ingester、distributor、querier三合一服务） |
| microservices      | *microservices | yes      | Loki微服务模式下的配置（当前还不支持）                       |
|                    |                |          |                                                              |

**cacheSpec**

| VARIABLE NAME | TYPE       | REQUIRED | DESCRIPTION                    |
| ------------- | ---------- | -------- | ------------------------------ |
| enabled       | bool       | yes      | 开启Loki缓存                   |
| expiration    | string     | yes      | 缓存key持久化时间              |
| type          | string     | yes      | 缓存类型 <br>redis / memcached |
| redis         | *redis     | yes      | redis服务配置                  |
| memcached     | *memcached | yes      | memcached服务配置              |

**schemaconfig**

| VARIABLE NAME | TYPE   | REQUIRED | DESCRIPTION                     |
| ------------- | ------ | -------- | ------------------------------- |
| index         | string | yes      | index存储  <br/>boltdb-shipper  |
| chunk         | string | yes      | chunk存储  <br/>filesystem / s3 |

**storageconfig**

| VARIABLE NAME               | TYPE            | REQUIRED | DESCRIPTION                                |
| --------------------------- | --------------- | -------- | ------------------------------------------ |
| boltdb-shipper              | *boltdb-shipper | yes      |                                            |
| boltdb-shipper.shared_store | string          | yes      | boltdb-shipper后端存储<br/>s3 / filesystem |
| filesystem                  | *filesystem     | yes      |                                            |
| filesystem.directory        |                 |          | filesystem写入路径                         |
| s3                          | *s3             | yes      |                                            |
| s3.address                  | string          | yes      | s3地址                                     |
| s3.secret_key               | string          | yes      | secret key                                 |
| s3.access_key               | string          | yes      | access key                                 |
| s3.bucket                   | string          | yes      | bucket名                                   |
|                             |                 |          |                                            |

