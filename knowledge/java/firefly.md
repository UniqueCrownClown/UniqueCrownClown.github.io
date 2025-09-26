---
title: 可观测
date: 2017-08-21 11:23:51
categories: java #文章分类
tags: [可观测] #文章标签，可以一次添加多个标签
---

可观测...

<!-- more -->

## 功能划分

1. 日志采集

2. 性能监控

3. 链路追踪

## logkeeper

## filebeat

10.8.4.118:8972

filebeat 采集的日志是怎么写到 elasticsearch 上的
filebeat 内置的可以通过配置文件上送到 elasticsearch

## logstash

借助 Logstash 实现数据同步，其底层实现原理就是根据配置定期使用 SQL 查询新增的数据写入 ES 中，实现数据的增量同步。

## mysql

基于 Binlog 的实时同步

## opentelemetry

是一组标准和工具的集合，旨在管理观测类数据，如 trace、metrics、logs 等 (未来可能有新的观测类数据类型出现)。

OpenTelemetry 提供与 vendor 无关的实现，根据用户的需要将观测类数据导出到不同的后端，如开源的 Prometheus、Jaeger 或云厂商的服务中。

## skywalking

skywalking Agents

skywalking oap

透传走 http 到 skywalking 查询
http://" + address + "/graphql"

也是用 FeignClientUtil

## Prometheus

走 MetricFeignAuthService 用 feign 发 http 请求

### Metrics 指标

#### 概念

在 Prometheus 里，指标代表要收集和监控的数值型数据。每个指标都有一个唯一的名称，用于描述被监控的对象，例如 http_requests_total 可表示 HTTP 请求的总次数。

#### 组成部分

1. 指标名称：唯一标识一个指标，通常使用有意义的名称来描述被监控的对象和维度，比如 process_cpu_seconds_total 表示进程的 CPU 使用总时间。
2. 标签（Labels）：一组键值对，用于对指标进行进一步的细分和标识。通过标签可以区分不同维度的数据，例如 http_requests_total{method="GET", endpoint="/api"} 表示 /api 端点的 GET 请求次数。
3. 时间戳：记录指标数据采集的时间点。
4. 样本值：指标在特定时间点的具体数值。

#### 指标类型

1. Counter(计数器) 一种累加的指标，其值只能单调递增或在重新启动时重置为 0。常用于记录请求次数、错误次数等。

2. Gauge(仪表盘) 表示一个可以任意上下浮动的数值。常用于记录当前内存使用量、在线用户数等。

3. Histogram(直方图) 用于对观察结果进行分组统计，例如请求的响应时间。它会将观察值划分到不同的桶（Bucket）中，并记录每个桶的计数和所有观察值的总和。

4. Summary(摘要) 与 Histogram 类似，也是用于统计观察结果的分布情况，但它会直接计算分位数。

#### 指标的采集和查询

Prometheus 通过 HTTP 协议定期从被监控目标的 /metrics 端点拉取指标数据。采集到的数据会存储在 Prometheus 服务器中，用户可以使用 PromQL（Prometheus Query Language）查询和分析这些指标数据。

### Alertmanager 组件
