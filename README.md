# Loki-Operator - a kubernetes operator for loki management

Loki Operator是基于[Operator-SDK](https://github.com/operator-framework/operator-sdk)构建的一个Ansible控制器，playbook利用[kubernetes.community](https://github.com/ansible-collections/community.kubernetes)模块与kubernetes实现交互。

Loki Operator完整的实现Loki在kubernetes环境下的生命周期和配置管理，适合在云原生场景多租户模式下的部署与管理。

> 🔔🔔🔔 Loki Operator还未经过大规模测试，目前还不建议直接用于生产环境.

最后，感谢Operator-SDK️项目 🌈

## Supported System

Kubernetes: 1.18+

## Features
- [x] Loki Architecture
  - [x] Single Node
  - [x] Cluster / HA [ 🔔 What is Loki Ha mode](https://github.com/grafana/loki/tree/main/production/docker)
    - [x] Loki
    - [x] Loki Frontent
    - [x] Loki Gateway
  - [ ] Cluster / MicroService
- [x] Ring
  - [x] Memberlist
  - [ ] Consul 
  - [ ] Etcd 
- [x] Cached
  - [x] Redis
  - [x] Memcached
- [x] StorageSchema
  - [x] S3
  - [x] boltdb-shipper
  - [ ] Cansandra
  - [ ] GCS
- [x] metrics
  - [x] Redis
  - [x] Memcache
  - [x] Loki
- [x] LokiStack
  - [ ] Grafana
  - [ ] Promtail

## Installation

1. 执行以下命令安装loki operator

```
kubtctl apply -f https://raw.githubusercontent.com/CloudmindsRobot/loki-operator/main/deploy/loki-operator.yaml
```

2. 执行以下命令验证部署结果

```
kubectl get pod -n loki-operator-system
NAME                                               READY   STATUS    RESTARTS   AGE
loki-operator-controller-manager-56c5547b9-d8v5j   2/2     Running   0          4h2m
```

## Deployment

1. 部署一个Loki单实例，使用boltdb-shipper本地磁盘做数据持久化

```
apiVersion: plugins.cloudminds.com/v1
kind: Loki
metadata:
  name: loki
  annotations:
    ansible.operator-sdk/reconcile-period: "30s"
spec:
  version: 2.2.1
  multitenancy: false
  servicemonitor: false
  service: 
    mode: single
    single:
      loki:
        image: grafana/loki
  schemaconfig:
    index: boltdb-shipper
    chunk: filesystem

  storageconfig:
    boltdb_shipper:
      shared_store: filesystem

    filesystem:
      directory: /loki/chunks
```

2. 在上述的Loki部署中启用redis缓存，并启用servicemonitor采集监控指标

```
apiVersion: plugins.cloudminds.com/v1
kind: Loki
metadata:
  name: loki
  annotations:
    ansible.operator-sdk/reconcile-period: "30s"
spec:
  version: 2.2.1
  multitenancy: false
  servicemonitor: true
  service: 
    mode: single
    single:
      loki:
        image: grafana/loki
  cacheconfig: 
    enabled: true
    expiration: 1h
    type: redis
    redis:
      image: redis
      tag: 5.0.6
  schemaconfig:
    index: boltdb-shipper
    chunk: filesystem

  storageconfig:
    boltdb_shipper:
      shared_store: filesystem

    filesystem:
      directory: /loki/chunks
```

3. 在上述的Loki集群HA模式，并使用boltdb-shipper和S3作为日志存储，同时打开缓存和监控指标，启用Ruler组件，并开启多租户

```
apiVersion: plugins.cloudminds.com/v1
kind: Loki
metadata:
  name: loki
  annotations:
    ansible.operator-sdk/reconcile-period: "30s"
spec:
  version: 2.2.1
  grafana: 
    enabled: true
  multitenancy: true
  servicemonitor: true
  service: 
    mode: cluster
    single:
      loki:
        image: grafana/loki
    cluster:
      type: ha
      replication_factor: 2
      ring:
        type: memberlist
      gateway:
        image: nginx
        tag: 1.15.1-alpine
        replicas: 2
      frontend:
        image: grafana/loki
        replicas: 2 
      loki:
        image: grafana/loki
        replicas: 3
  cacheconfig: 
    enabled: true
    expiration: 1h
    type: memcached
    redis:
      image: redis
      tag: 5.0.6

  ruler:
    enabled: true
    ring: memberlist
    alertmanager: http://xxxxxxx
    storage: s3
    s3:
      bucket: loki-ruler-operator

  schemaconfig:
    index: boltdb-shipper
    chunk: s3

  storageconfig:
    boltdb_shipper:
      shared_store: s3

    filesystem:
      directory: /loki/chunks

    s3:
      address: x.x.x.x
      secret_key: xPfIfXBj1Mnt625ZA9c2wXXgmLVPaMUOmMBt3M6H
      access_key: g373dR9D1ZAsUD6FWEO
      bucket: loki-operator
```

更多关于CRD的描述请参考[Loki CRD Spec](deploy/crd_loki_spec.md)

