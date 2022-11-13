---
title: 【Metropolitan 本纪】01-为什么要重复造轮子？
date: 2022-11-13 01:50:01
tags:
  - Metropolitan
  - Open Source
categories:
  - Metropolitan
  - Open Source
---

> 1863 年 1 月，一列由蒸汽机车牵引的木质车厢驶向了伦敦的地下，这宣告着世界上的第一条地铁—— **大都会地铁（Metropolitan Railway）** 就此诞生。

## Metropolitan 是什么？

`Metropolitan` 是一个开源运维自动化平台，其灵感来源于 [Ansible](https://github.com/ansible/ansible) 和 [蓝鲸作业平台](https://github.com/Tencent/bk-job)，支持一键对上万台服务器进行脚本执行、文件分发、定时任务等操作，用户除了使用 `Metropolitan` 提供的这些标准操作之外，还可以根据业务需要定义自己的操作类型（如添加监控、屏蔽监控告警、调用第三方系统等）。`Metropolitan` 还支持将这些操作按步骤编排为一个自动化的任务并通过 `API` 进行调用，实现运维自动化，降低运维成本。

## Metropolitan 的功能？

`Metropolitan` 具有蓝鲸作业平台拥有的几乎所有功能，同时也抛弃了一些不具备通用性的功能（例如和蓝鲸运维体系下其他系统强耦合的功能等），添加了一些新的功能（如自定义的操作类型、服务发现功能等），并大大增强了通用性和可扩展性，且在架构方面更加轻量，更便于小规模业务使用和企业内部根据自己的业务需要进行二次开发。

### 快捷操作

类似于蓝鲸作业平台的“快速执行”功能，对脚本执行、文件分发和某些用户配置的自定义操作类型提供轻量级的、便捷的一次性操作入口。

### 任务编排

类似于蓝鲸作业平台的“任务编排”功能，可以按照用户需要将各项操作（Operation）组装成一个可以单独运行的“任务（Task）”，便于按需调用。

### 任务执行（Metropolitan-Executor）

是一个轻量级的、独立启动的进程，用于执行任务或快捷操作并收集其执行结果，同时还能管理执行产生的日志等信息。

### 定时任务

类似于蓝鲸作业平台的“定时执行”功能，可以实现任务的周期执行和单次定时执行。对于周期执行的任务，可通过图形化的方式或 `Linux` 的 `cron` 表达式指定某个任务的执行时间。

### 通知管理

类似于蓝鲸作业平台的“消息通知”功能，可配置权限与通知事件的对应关系，也可将用户分组并提供按组通知的功能，并在任务执行失败、定时任务执行失败、定时任务开始执行前等需要通知的场景对用户进行通知，同时会保留完整的通知日志，便于用户进行查看。

### 服务发现（Metropolitan-Discovery）

类似于“蓝鲸节点管理平台”，是一个独立启动的进程，可以对 `Agent` 和 `Proxy` 进行管理和查看，也可配置 `Agent` 和 `Proxy` 的发现方式并对其进行健康检查等操作。`Metropolitan-Discovery` 严格来说是一个为 `Metropolitan` 等平台服务的通用组件，所以拥有自己单独的前端界面。

### 基于 RBAC 的用户管理功能

`Metropolitan` 默认提供了基于 `RBAC` 的用户管理功能，可将用户与服务器账号和 `Metropolitan` 的各项功能关联起来，可实现大部分用户的需求，同时也支持用户根据自己的需要接入自己的权限管理系统。

## 架构设计

Metropolitan 的架构图如下：

![Metropolitan 架构图](https://cdn.nlark.com/yuque/0/2022/png/32771015/1668273853403-eddd08ae-6e9c-4c3a-8dac-534cbf586ac6.png#averageHue=%23f7f4f1&clientId=u00a1dc89-6a3b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=348&id=u34be980e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=522&originWidth=1137&originalType=binary&ratio=1&rotation=0&showTitle=false&size=34263&status=done&style=none&taskId=u2d1031fa-c94a-480d-bb70-666deb227c2&title=&width=758)

`Metropolitan` 是基于 `Go 语言` 和 `TypeScript` 编写的，采用前后端分离的设计方式和端口-适配器模式（六边形架构），具有超强的扩展性，便于企业根据自己的业务需要进行二次开发。

### 前端

基于 `React.js` 和 `Ant Design Pro`，采用 `TypeScript` 开发。

### 后端

使用 `Golang` 开发，整体基于端口-适配器模式（六边形架构）设计，除了 `Metropolitan-Discovery`（由于通用性强而拆分） 和 `Metropolitan-Executor`（由于并发量大、性能要求高而拆分）外，其余部分（统称为 `Metropolitan-Core`）为单体架构，并且是无状态的，可以满足多点部署的需求。`Metropolitan-Discovery`、`Metropolitan-Executor` 和 `Metropolitan-Core` 之间通过 `rpcx`（默认）、`HTTP` 和其他可扩展的方式进行通信。

### 中间件

- `etcd`：提供分布式锁服务的同时作为服务注册与发现中心，使得 `Metropolitan-Core` 与 `Metropolitan-Executor` 之间可以相互进行服务发现。
- `MongoDB`：`Metropolitan` 的主数据库，保存 `Metropolitan` 所需的几乎全部数据（注：不用 `MySQL` 是为了满足一些结构无法确定的数据的存储需求，如用户自定义的操作类型步骤等）。
- `Kafka`（可选）：消息队列，传递系统中的心跳检测、任务执行状态切换等 `CloudEvents` 事件等，并传递任务执行过程中产生的日志等信息。
- `Redis`（可选）：缓存服务，负责缓存任务执行过程中的主机和状态等信息，提高系统的并发能力。
- `ElasticSearch`：日志数据库，负责保存任务执行中产生的日志信息。
- `MinIO`：默认的文件存储服务，负责保存用户上传的文件和导出的日志文件等。
  
## Metropolitan 的特色功能
  
蓝鲸作业平台所具有的那些功能就不再提了，以下是 `Metropolitan` 目前规划的一些“人无我有”的特色功能。
  
### 自定义的操作类型（Operation）
  
由于 `Metropolitan` 的操作类型 `Operation` 是可扩展的，因此用户可以根据自己的需要来开发可插拔的自定义操作类型——例如调用第三方系统、操作 `K8s`、调用 `GitLab` 等，并将其作为步骤或快捷操作中的执行实体使用，以此来扩展 `Metropolitan` 的能力。
  
### 自定义的脚本语言类型（ScriptType）
  
`Metropolitan` 默认提供了 `Shell` 和 `Python` 两种脚本语言作为脚本执行的内容，但用户可根据自己的需要来扩展所需的脚本语言，如 `PowerShell` 等（甚至是非脚本语言——如 `Golang` 等），只需要在 `Agent` 上扩展实现对应脚本语言的编译等操作并在 `Agent` 所在的机器上安装对应语言的执行环境即可。
  
### 自定义的接入层（API）
  
`Metropolitan` 默认提供了 `HTTP` 与 `rpcx` 两种协议的接口，用户可根据需要实现其他协议的接口，如 `gRPC` 等。
  
### Client SDK（Metropolitan-Cli）
  
默认提供 `Go` 语言的 `Client SDK`，通过 `rpcx` 的方式与 `Metropolitan` 进行交互，便于用户在第三方系统中对接 `Metropolitan`，增强 `Metropolitan` 的自动化能力。
  
### 自定义的执行目标来源管理（ExecuteTarget）
  
默认通过 `Metropolitan-Discovery` 形成可用的目标服务器列表并通过每个 `Agent` 的 `uuid` 区分不同的执行目标，用户也可以通过二次开发来接入自己的 `CMDB` 等第三方系统实现灵活的执行目标来源管理功能，甚至可以实现按节点、集群等方式批量选择执行目标，只需实现 `GetExecuteTargets()` 方法即可。
  
### 系统配置管理（CustomConfig）
  
一个可以提供自定义 `JSON` 格式的配置管理的小功能，便于用户在二次开发时将一些必需的数据保存在 `Metropolitan` 中（例如第三方系统的地址等）。
  
## 为什么要重复造轮子？
  
你可能要问了：既然有了蓝鲸作业平台，那为什么又要重复造轮子呢？除了上述那些特色功能之外，还有以下两个原因：
  
### 更轻量
  
相比于微服务架构的蓝鲸作业平台，单体架构的 `Metropolitan` 显然更轻量，虽然有 `Metropolitan-Discovery` 和 `Metropolitan-Executor` 这两个独立启动的进程，但是进程数量也比蓝鲸作业平台少得多；同时，`Metropolitan` 是使用 `Golang` 编写的，相比基于 `Java` 和 `Spring Cloud` 的蓝鲸作业平台在运行时占用的资源少得多，部署更加简便，在业务规模较小时也不会造成太大的资源压力。
  
### 扩展性更强
  
`Metropolitan` 是作为开源项目而不是商业项目诞生的，所以对扩展性和二次开发的能力格外重视。由于六边形架构天然具有更强的可扩展性，架构方面的优势赋予了 `Metropolitan` 强大的扩展能力——几乎什么都可以扩展，可以完美满足用户各种各样的二次开发需求，同时还提供了 `Client SDK`，便于用户将 `Metropolitan` 与现有系统对接。
  
### 松耦合
  
`Metropolitan` 的定位是一个可以独立使用的项目，它的运行不依赖于其他任何第三方系统，相比蓝鲸作业平台有着更好的兼容性和泛用性。

此外，`Metropolitan` 诞生的意义并不是取代蓝鲸作业平台。蓝鲸作业平台其实只是 `Metropolitan` 的灵感来源之一，而 `Metropolitan` 完全是一个独立的开源项目。
  
## 总结
  
“我最擅长的事情并不是凭空想出一个不存在的东西，而是优化现有的东西。”
  
作为《Metropolitan 本纪》系列的第一篇，这篇文章也标示着 Metropolitan 项目的开始，希望 Metropolitan 可以被更多的人所认可，最终为 SRE 行业贡献出自己的力量。
