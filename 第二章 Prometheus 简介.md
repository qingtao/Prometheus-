## 第二章 Prometheus 简介

Prometheus 专注于现在发生的事情，而不是追踪数周或数月前的数据。

### Prometheus 架构

Prometheus 通过抓取或拉取应用程序中暴露的时间序列数据来工作。

时间序列数据通常由应用程序本身通过客户端库或称为export（导出器）的代理来作为HTTP端点暴露。

Promethenus 还有一个推送网关（push gateway），可用于接收少量数据--例如，来自无法拉取的目标数据（如临时作业或者防火墙后的目标）。

#### 指标收集

Prometheus 称其可以抓取的指标来源为**端点**（*endpoint*）。

端点通常对应单个进程、主机、服务或应用程序。为了抓取端点数据，Prometheus定义了名为**目标**（*target*）的配置。一组目标称为**作业**（*job*）。

#### 服务发现

可以通过多种方式来处理要监控的资源的发现，包括：

- 用户提供的静态资源列表

- 基于文件的发现

    例如，使用配置管理工具生成在Prometheus中可以自动更新的资源列表。

- 自动发现

    例如，查询consul等数据存储，在Amazon或Google中运行实例，或使用DNS SRV记录来生成资源列表。

#### 冗余和高可用

冗余和高可用侧重弹性而非数据持久性。Prometheus团队建议将Prometheus服务器部署到特定环境和团队，而不是仅部署到一个单体Prometheus服务器。

HA模式，可以使用两个或两个以上配置相同的Prometheus服务器来收集时间序列数据，并且所有生成的警报都由可消除重复警报的高可用Alertmannager集群来处理。

### Prometheus 数据模型

#### 指标名称

**时间序列名称**通常描述收集的时间序列数据的一般性质。例如：website_visits_total表示网站访问的总数。名称可以包含ASCII字符、数字、下划线和冒号。

#### 标签

**标签**为Prometheus数据模型提供了维度。他们为特定时间序列添加上下文。websit_visits_total时间序列可以使用能够识别网站名称、请求IP或其他特殊标识的标签。标签可以包含ASCII字符、数字和下划线。

**标签**共有两大类：

- **插桩标签**（*instrumentation label*）

    插桩标签来自被监控的资源。例如：对于与HTTP相关的时间序列，标签可能会显示所使用的特定HTTP动词。这些标签在由诸如客户端或exporter抓取之前会被添加到时间序列中。

- **目标标签**（*target label*）

    目标标签更多的与架构相关，他们可能会识别时间序列所在的数据中心。目标标签由Prometheus在抓取期间和之后添加。

**注** 带有"__"前缀的标签名称保留给Prometheus内部使用。

#### 采样数据

时间序列的真实值是（sample）的结果，它包含两部分：

- 一个float64类型的数值
- 一个毫秒精度的时间戳

#### 符号表示

例如：带有标签website_visits_total的时间序列可能如下表示:

website_visits_total{site="MegaApp, location="NJ", instance="webserver", job="web"}

首先是时间序列名称（website_visits_total），后面跟着一组键值对标签。通常所有时间序列都有一个instance标签（标识源主机和应用）和一个job标签（包含抓取特定时间序列的作业名称）。

#### 保留时间

Prometheus专为短期监控和警报需求设计。默认情况下，他在数据库中保留**15**天的时间序列数据。如果要保留更长时间的数据，建议将所需数据发送到远程的第三方平台。Prometheus能够写入外部数据存储。


