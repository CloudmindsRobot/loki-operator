
# Loki-Operator - a kubernetes operator for loki management

Loki Operator是基于[Operator-SDK](https://github.com/operator-framework/operator-sdk)构建的一个Ansible控制器，playbook利用[kubernetes.community](https://github.com/ansible-collections/community.kubernetes)模块与kubernetes实现交互。Loki Operator完整的实现Loki在kubernetes环境下的生命周期和配置管理，适合在云原生场景多租户模式下的部署与管理。

# Features
- 支持Loki单实例部署
- 支持Loki集群的HA架构自动部署（包含loki、frontend和gateway）
- 支持缓存部署与配置（内置redis和memcached）
- 日志持久化支持对接S3和boltdb本地磁盘

# Feature
- 支持Loki微服务架构部署
- 包含更多的数据持久化存储服务和配置（Cassandra和三方存储）
- 包含LokiStack内更多产品集成


