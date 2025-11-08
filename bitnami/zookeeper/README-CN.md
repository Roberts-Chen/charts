# Bitnami 版 Apache ZooKeeper 软件包

Apache ZooKeeper 为分布式应用程序提供了可靠的、集中式的配置数据和服务注册中心。

[Apache ZooKeeper 概述](https://zookeeper.apache.org)

商标声明：本软件列表由 Bitnami 打包。所提供产品中提及的各个商标分别归相应公司所有，使用这些商标并不意味着任何关联或背书。

## 简明教程

```
helm install my-release oci://registry-1.docker.io/bitnamicharts/zookeeper
```

想要在生产环境中使用 Apache ZooKeeper 吗？可以尝试 [VMware Tanzu Application Catalog](https://bitnami.com/enterprise)，它是 Bitnami 目录的商业版本。

## ⚠️ 重要通知：Bitnami 目录即将发生的变更

自 2025 年 8 月 28 日起，Bitnami 将改进其公共目录，在新的 [Bitnami 安全镜像计划](https://news.broadcom.com/app-dev/broadcom-introduces-bitnami-secure-images-for-production-ready-containerized-applications) 下提供一系列精心挑选的、注重安全性的强化镜像。作为此次过渡的一部分：

- 首次向社区用户提供热门容器镜像的安全优化版本。

- Bitnami 将开始在其免费层级中停止对非强化的、基于 Debian 的软件镜像的支持，并将逐步从公共目录中移除非最新标签。因此，社区用户将只能访问数量减少的强化镜像。这些镜像仅以 “latest” 标签发布，适用于开发目的。

- 从 8 月 28 日开始，在两周内，所有现有容器镜像，包括较旧的或带版本的标签（例如 2.50.0、10.6），都将从公共目录（[docker.io/bitnami](http://docker.io/bitnami)）迁移到 “Bitnami Legacy” 仓库（[docker.io/bitnamilegacy](http://docker.io/bitnamilegacy)），并且不再接收更新。

- 对于生产工作负载和长期支持，建议用户采用 Bitnami 安全镜像，其中包括强化容器、更小的攻击面、CVE 透明度（通过 VEX/KEV）、SBOM 以及企业支持。

这些变更旨在通过推广软件供应链完整性和最新部署的最佳实践，提高所有 Bitnami 用户的安全态势。有关更多详细信息，请访问 [Bitnami 安全镜像公告](https://github.com/bitnami/containers/issues/83267)。

## 简介

本图表使用 [Helm](https://helm.sh) 包管理器在 [Kubernetes](https://kubernetes.io) 集群上引导 [ZooKeeper](https://github.com/bitnami/containers/tree/main/bitnami/zookeeper) 部署。

## 先决条件

- Kubernetes 1.23+

- Helm 3.8.0+

- 底层基础设施支持 PV 供应器

## 安装图表

要安装名为 my-release 的版本，请执行：

```
helm install my-release oci://REGISTRY_NAME/REPOSITORY_NAME/zookeeper
```

> 注意：您需要将占位符 REGISTRY_NAME 和 REPOSITORY_NAME 替换为您的 Helm 图表 registry 和仓库的引用。例如，对于 Bitnami，您需要使用 REGISTRY_NAME=[registry-1.docker.io](http://registry-1.docker.io) 和 REPOSITORY_NAME=bitnamicharts。

这些命令使用默认配置在 Kubernetes 集群上部署 ZooKeeper。[参数](#parameters) 部分列出了可在安装过程中配置的参数。

> **提示**：使用 helm list 列出所有版本

## 配置和安装详情

### 资源请求和限制

Bitnami 图表允许为图表部署内的所有容器设置资源请求和限制。这些设置位于 resources 值中（查看参数表）。设置请求对于生产工作负载至关重要，应根据您的具体用例进行调整。

为简化此过程，该图表包含 resourcesPreset 值，它会根据不同的预设自动设置 resources 部分。查看 [bitnami/common 图表](https://github.com/bitnami/charts/blob/main/bitnami/common/templates/_resources.tpl#L15) 中的这些预设。但是，在生产工作负载中，不建议使用 resourcesPreset，因为它可能无法完全适应您的特定需求。有关容器资源管理的更多信息，请参阅 [官方 Kubernetes 文档](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)。

### 更新凭据

Bitnami 图表在首次启动时配置凭据。秘密或凭据的任何后续更改都需要手动干预。请按照以下说明操作：

- 按照 [上游文档](https://zookeeper.apache.org/documentation.html) 更新用户密码

- 使用新值更新密码密钥（替换占位符 SECRET_NAME、CLIENT_PASSWORD 和 SERVER_PASSWORD）

```
kubectl create secret generic SECRET_NAME --from-literal=client-password=CLIENT_PASSWORD --from-literal=server-password=SERVER_PASSWORD --dry-run -o yaml | kubectl apply -f -
```

### Prometheus 指标

通过将 metrics.enabled 设置为 true，可以将此图表与 Prometheus 集成。这将暴露 Zookeeper 原生 Prometheus 端点和可在 metrics.service 部分配置的 metrics 服务。它将具有必要的注释，以便被 Prometheus 自动抓取。

#### Prometheus 要求

要使集成工作，必须安装有效的 Prometheus 或 Prometheus Operator。安装 [Bitnami Prometheus helm 图表](https://github.com/bitnami/charts/tree/main/bitnami/prometheus) 或 [Bitnami Kube Prometheus helm 图表](https://github.com/bitnami/charts/tree/main/bitnami/kube-prometheus)，以便在您的集群中轻松获得有效的 Prometheus。

#### 与 Prometheus Operator 集成

该图表可以部署 ServiceMonitor 对象，以与 Prometheus Operator 安装集成。为此，请将值 metrics.serviceMonitor.enabled 设置为 true。确保在集群中安装了 Prometheus Operator CustomResourceDefinitions，否则会失败并出现以下错误：

```
no matches for kind "ServiceMonitor" in version "monitoring.coreos.com/v1"
```

安装 [Bitnami Kube Prometheus helm 图表](https://github.com/bitnami/charts/tree/main/bitnami/kube-prometheus) 以获得必要的 CRD 和 Prometheus Operator。

### [滚动标签与不可变标签](https://techdocs.broadcom.com/us/en/vmware-tanzu/application-catalog/tanzu-application-catalog/services/tac-doc/apps-tutorials-understand-rolling-tags-containers-index.html)

强烈建议在生产环境中使用不可变标签。这确保如果相同标签使用不同镜像更新，您的部署不会自动更改。

如果主容器有新版本、重大更改或存在严重漏洞，Bitnami 将发布新图表来更新其容器。

### 配置日志级别

您可以使用 ZOO_LOG_LEVEL 环境变量或参数 logLevel 配置 ZooKeeper 日志级别。默认情况下，它设置为 ERROR，因为每次使用存活探针和就绪探针都会在连接时产生 INFO 消息，在断开连接时产生 WARN 消息，从而在日志中生成大量无用信息。

为了消除该日志噪音，以便将级别设置为 “INFO”，必须进行两项更改。

首先，确保您不是通过在 ZooKeeper 客户端端口上轮询 “mntr” 这种已弃用的模式来获取指标。轮询 Apache ZooKeeper 指标的首选方法是 ZooKeeper 指标服务器。当将 metrics.enabled 设置为 true 时，此图表支持该方法。

其次，为避免探针的连接 / 断开连接消息，您可以为这些检查设置自定义值，将它们指向 ZooKeeper 管理服务器而不是客户端端口。默认情况下，将启动一个管理服务器，在端口 8080 上监听 [localhost](http://localhost)。以下是将管理服务器用于探针的示例：

```
livenessProbe:
  enabled: false
readinessProbe:
  enabled: false
customLivenessProbe:
  exec:
    command: ['/bin/bash', '-c', 'curl -s -m 2 http://localhost:8080/commands/ruok | grep ruok']
  initialDelaySeconds: 30
  periodSeconds: 10
  timeoutSeconds: 5
  successThreshold: 1
  failureThreshold: 6
customReadinessProbe:
  exec:
    command: ['/bin/bash', '-c', 'curl -s -m 2 http://localhost:8080/commands/ruok | grep error | grep null']
  initialDelaySeconds: 5
  periodSeconds: 10
  timeoutSeconds: 5
  successThreshold: 1
  failureThreshold: 6
```

您还可以通过使用在 conf/log4j.properties 中设置的 ZOO_LOG4J_PROP 来设置 log4j 日志级别以及启用哪些日志附加器，默认情况下，zookeeper.root.logger 为

```
zookeeper.root.logger=INFO, CONSOLE
```

可用的附加器有：

- CONSOLE

- ROLLINGFILE

- RFAAUDIT

- TRACEFILE

### 备份和恢复

要在 Kubernetes 上备份和恢复 Helm 图表部署，您需要从源部署备份持久卷，并使用 [Velero](https://velero.io/)（一种 Kubernetes 备份 / 恢复工具）将它们附加到新部署。在 [本指南](https://techdocs.broadcom.com/us/en/vmware-tanzu/application-catalog/tanzu-application-catalog/services/tac-doc/apps-tutorials-backup-restore-deployments-velero-index.html) 中查找使用 Velero 的说明。

## 持久性

[Bitnami ZooKeeper](https://github.com/bitnami/containers/tree/main/bitnami/zookeeper) 镜像将 ZooKeeper 数据和配置存储在容器的 /bitnami/zookeeper 路径下。

持久卷声明用于在部署之间保留数据。已知这在 GCE、AWS 和 minikube 中有效。请参阅 [参数](#parameters) 部分配置 PVC 或禁用持久性。

如果在使用持久卷时遇到错误，请参考我们的 [持久卷故障排除指南](https://docs.bitnami.com/kubernetes/faq/troubleshooting/troubleshooting-persistence-volumes/)。

### 调整持久卷挂载点的权限

由于镜像默认以非 root 用户运行，因此需要调整持久卷的所有权，以便容器可以向其中写入数据。

默认情况下，图表配置为使用 Kubernetes 安全上下文自动更改卷的所有权。但是，此功能并非在所有 Kubernetes 发行版中都能正常工作。

作为替代方案，此图表支持使用 initContainer 在将卷挂载到最终目标之前更改卷的所有权。

您可以通过将 volumePermissions.enabled 设置为 true 来启用此 initContainer。

### 配置数据日志目录

您可以使用专用设备存储日志（而不是使用数据目录），以帮助避免日志记录和快照之间的资源竞争。为此，请设置 dataLogDir 参数，指定用于写入事务日志的路径。或者，将此参数设置为空字符串，日志将写入数据目录（Zookeeper 的默认行为）。

当使用专用设备存储日志时，您可以使用 PVC 来持久化日志。为此，请将 persistence.enabled 设置为 true。有关更多信息，请参阅 [持久性参数](#persistence-parameters) 部分。

### 设置 pod 亲和性

此图表允许您使用 affinity 参数设置自定义 pod 亲和性。有关 pod 亲和性的更多信息，请参阅 [Kubernetes 文档](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity)。

或者，您可以使用 [bitnami/common](https://github.com/bitnami/charts/tree/main/bitnami/common#affinities) 图表中提供的任何 pod 亲和性、pod 反亲和性和节点亲和性的预设配置。为此，请设置 podAffinityPreset、podAntiAffinityPreset 或 nodeAffinityPreset 参数。

## 参数

### 全局参数

| 名称                                                | 描述                                                         | 值    |
| --------------------------------------------------- | ------------------------------------------------------------ | ----- |
| global.imageRegistry                                | 全局 Docker 镜像仓库                                         | ""    |
| global.imagePullSecrets                             | 作为数组的全局 Docker 仓库密钥名称                           | []    |
| global.defaultStorageClass                          | 持久卷的全局默认存储类                                       | ""    |
| global.storageClass                                 | 已弃用：请改用 global.defaultStorageClass                    | ""    |
| global.security.allowInsecureImages                 | 允许跳过镜像验证                                             | false |
| global.compatibility.openshift.adaptSecurityContext | 调整部署的 securityContext 部分，使其与 Openshift restricted-v2 SCC 兼容：移除 runAsUser、runAsGroup 和 fsGroup，让平台使用其允许的默认 ID。可能的值：auto（如果检测到运行的集群是 Openshift，则应用）、force（始终执行适配）、disabled（不执行适配） | auto  |

### 通用参数

| 名称                   | 描述                                                         | 值            |
| ---------------------- | ------------------------------------------------------------ | ------------- |
| kubeVersion            | 覆盖 Kubernetes 版本                                         | ""            |
| nameOverride           | 部分覆盖 common.names.fullname 模板的字符串（将保留发布名称） | ""            |
| fullnameOverride       | 完全覆盖 common.names.fullname 模板的字符串                  | ""            |
| clusterDomain          | Kubernetes 集群域                                            | cluster.local |
| extraDeploy            | 要部署的额外对象（作为模板评估）                             | []            |
| commonLabels           | 向所有部署的资源添加标签                                     | {}            |
| commonAnnotations      | 向所有部署的资源添加注释                                     | {}            |
| namespaceOverride      | 覆盖 ZooKeeper 资源的命名空间                                | ""            |
| usePasswordFiles       | 将凭据挂载为文件而不是使用环境变量                           | true          |
| diagnosticMode.enabled | 启用诊断模式（所有探针将被禁用，命令将被覆盖）               | false         |
| diagnosticMode.command | 覆盖有状态集中所有容器的命令                                 | ["sleep"]     |
| diagnosticMode.args    | 覆盖有状态集中所有容器的参数                                 | ["infinity"]  |

### ZooKeeper 图表参数

| 名称                        | 描述                                                         | 值                        |
| --------------------------- | ------------------------------------------------------------ | ------------------------- |
| image.registry              | ZooKeeper 镜像仓库                                           | REGISTRY_NAME             |
| image.repository            | ZooKeeper 镜像仓库路径                                       | REPOSITORY_NAME/zookeeper |
| image.digest                | ZooKeeper 镜像摘要，格式为 sha256:aa.... 请注意，如果设置了此参数，将覆盖标签 | ""                        |
| image.pullPolicy            | ZooKeeper 镜像拉取策略                                       | IfNotPresent              |
| image.pullSecrets           | 作为数组指定 docker-registry 密钥名称                        | []                        |
| image.debug                 | 指定是否应设置调试值                                         | false                     |
| auth.client.enabled         | 启用 ZooKeeper 客户端 - 服务器身份验证。使用 SASL/Digest-MD5 | false                     |
| auth.client.clientUser      | ZooKeeper 客户端用于身份验证的用户                           | ""                        |
| auth.client.clientPassword  | ZooKeeper 客户端用于身份验证的密码                           | ""                        |
| auth.client.serverUsers     | 要创建的用户的逗号、分号或空格分隔列表                       | ""                        |
| auth.client.serverPasswords | 为创建的用户分配的密码的逗号、分号或空格分隔列表             | ""                        |
| auth.client.existingSecret  | 使用现有密钥（忽略先前的密码）                               | ""                        |
| auth.quorum.enabled         | 启用 ZooKeeper 服务器 - 服务器身份验证。使用 SASL/Digest-MD5 | false                     |
| auth.quorum.learnerUser     | ZooKeeper quorumLearner 用于向 quorumServers 进行身份验证的用户。 | ""                        |
| auth.quorum.learnerPassword | ZooKeeper quorumLearner 用于向 quorumServers 进行身份验证的密码。 | ""                        |
| auth.quorum.serverUsers     | quorumServers 的用户的逗号、分号或空格分隔列表。             | ""                        |
| auth.quorum.serverPasswords | 为创建的用户分配的密码的逗号、分号或空格分隔列表             | ""                        |
| auth.quorum.existingSecret  | 使用现有密钥（忽略先前的密码）                               | ""                        |
| tickTime                    | ZooKeeper 用于心跳的基本时间单位（以毫秒为单位）             | 2000                      |
| initLimit                   | ZooKeeper 用于限制仲裁中的 ZooKeeper 服务器连接到领导者的时间长度 | 10                        |
| syncLimit                   | 服务器与领导者之间的最大时间差                               | 5                         |
| preAllocSize                | 事务日志文件的块大小                                         | 65536                     |
| snapCount                   | 在可以拍摄快照（并滚动事务日志）之前记录在事务日志中的事务数 | 100000                    |
| maxClientCnxns              | 限制单个客户端可以与 ZooKeeper 集群的单个成员建立的并发连接数 | 60                        |
| maxSessionTimeout           | 服务器允许客户端协商的最大会话超时时间（以毫秒为单位）       | 40000                     |
| heapSize                    | Java 堆选项（Xmx 和 Xms）的大小（以 MB 为单位）              | 1024                      |
| fourlwCommandsWhitelist     | 可执行的逗号分隔的四字命令列表                               | srvr, mntr, ruok          |
| minServerId                 | 最小 SERVER_ID 值，节点分别递增其 ID                         | 1                         |
| listenOnAllIPs              | 允许 ZooKeeper 在所有可用 IP 地址上监听来自其对等节点的连接  | false                     |
| zooServers                  | ZooKeeper 空格分隔的服务器列表。留空以使用默认的 ZooKeeper 服务器名称。 | ""                        |
| autopurge.snapRetainCount   | 要保留的最新快照数量（以及相应的事务日志）                   | 10                        |
| autopurge.purgeInterval     | 触发清理任务的时间间隔（以小时为单位）                       | 1                         |
| logLevel                    | ZooKeeper 服务器的日志级别。默认情况下为 ERROR               | ERROR                     |
| jvmFlags                    | ZooKeeper 进程的默认 JVM 标志                                | ""                        |
| dataLogDir                  | 专用数据日志目录                                             | ""                        |
| configuration               | 使用自定义 zoo.cfg 文件配置 ZooKeeper                        | ""                        |
| existingConfigmap           | 包含您的 ZooKeeper 自定义配置的现有 ConfigMap 的名称         | ""                        |
| extraEnvVars                | 要添加到 ZooKeeper 节点的额外环境变量数组                    | []                        |
| extraEnvVarsCM              | 包含 ZooKeeper 节点额外环境变量的现有 ConfigMap 的名称       | ""                        |
| extraEnvVarsSecret          | 包含 ZooKeeper 节点额外环境变量的现有 Secret 的名称          | ""                        |
| command                     | 覆盖默认容器命令（使用自定义镜像时有用）                     | ["/scripts/setup.sh"]     |
| args                        | 覆盖默认容器参数（使用自定义镜像时有用）                     | []                        |

### 有状态集参数

| 名称                                   | 描述                                                         | 值     |
| -------------------------------------- | ------------------------------------------------------------ | ------ |
| replicaCount                           | ZooKeeper 节点数量                                           | 1      |
| revisionHistoryLimit                   | 保留的旧历史记录数量，以便允许回滚                           | 10     |
| containerPorts.client                  | ZooKeeper 客户端容器端口                                     | 2181   |
| containerPorts.tls                     | ZooKeeper TLS 容器端口                                       | 3181   |
| containerPorts.follower                | ZooKeeper 追随者容器端口                                     | 2888   |
| containerPorts.election                | ZooKeeper 选举容器端口                                       | 3888   |
| containerPorts.adminServer             | ZooKeeper 管理服务器容器端口                                 | 8080   |
| containerPorts.metrics                 | ZooKeeper Prometheus 导出器容器端口                          | 9141   |
| livenessProbe.enabled                  | 在 ZooKeeper 容器上启用存活探针                              | true   |
| livenessProbe.initialDelaySeconds      | 存活探针的初始延迟秒数                                       | 30     |
| livenessProbe.periodSeconds            | 存活探针的周期秒数                                           | 10     |
| livenessProbe.timeoutSeconds           | 存活探针的超时秒数                                           | 5      |
| livenessProbe.failureThreshold         | 存活探针的失败阈值                                           | 6      |
| livenessProbe.successThreshold         | 存活探针的成功阈值                                           | 1      |
| livenessProbe.probeCommandTimeout      | 存活探针的探针命令超时时间                                   | 3      |
| readinessProbe.enabled                 | 在 ZooKeeper 容器上启用就绪探针                              | true   |
| readinessProbe.initialDelaySeconds     | 就绪探针的初始延迟秒数                                       | 5      |
| readinessProbe.periodSeconds           | 就绪探针的周期秒数                                           | 10     |
| readinessProbe.timeoutSeconds          | 就绪探针的超时秒数                                           | 5      |
| readinessProbe.failureThreshold        | 就绪探针的失败阈值                                           | 6      |
| readinessProbe.successThreshold        | 就绪探针的成功阈值                                           | 1      |
| readinessProbe.probeCommandTimeout     | 就绪探针的探针命令超时时间                                   | 2      |
| startupProbe.enabled                   | 在 ZooKeeper 容器上启用启动探针                              | false  |
| startupProbe.initialDelaySeconds       | 启动探针的初始延迟秒数                                       | 30     |
| startupProbe.periodSeconds             | 启动探针的周期秒数                                           | 10     |
| startupProbe.timeoutSeconds            | 启动探针的超时秒数                                           | 1      |
| startupProbe.failureThreshold          | 启动探针的失败阈值                                           | 15     |
| startupProbe.successThreshold          | 启动探针的成功阈值                                           | 1      |
| customLivenessProbe                    | 覆盖默认存活探针的自定义存活探针                             | {}     |
| customReadinessProbe                   | 覆盖默认就绪探针的自定义就绪探针                             | {}     |
| customStartupProbe                     | 覆盖默认启动探针的自定义启动探针                             | {}     |
| lifecycleHooks                         | 用于 ZooKeeper 容器在启动前后自动配置                        | {}     |
| resourcesPreset                        | 根据一个常见预设设置容器资源（允许的值：none、nano、micro、small、medium、large、xlarge、2xlarge）。如果设置了 resources，则忽略此设置（resources 推荐用于生产环境）。 | micro  |
| resources                              | 为不同资源（如 CPU 或内存）设置容器请求和限制（对生产工作负载至关重要） | {}     |
| podSecurityContext.enabled             | 启用 ZooKeeper pods 的安全上下文                             | true   |
| podSecurityContext.fsGroupChangePolicy | 设置文件系统组更改策略                                       | Always |
| podSecurityContext.sysctls             | 使用 sysctl 接口设置内核参数                                 | []     |