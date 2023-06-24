---
title: 可观测性
date: 2023-04-03 23:08:08
categories:
    - work
tags: 
    - 可观测性
    - 日志
    - 云原生
    - grafana
---



# 前言

## 目前需要什么？

- 一个mock服务器 -- 方便前端、客户端、trd服务端、甚至是cp接入接口

- 约定俗成的api描述规范 -- OpenApi就行

- 一个因地制宜的便捷的api文档维护方式 -- 谁也不想写文档，要尽量减少编写、维护文档的成本

- 一致性测试，或者说接口集成测试 -- 保证开发&上线之间的一致性

    https://microcks.io/documentation/using/tests/

## 为什么需要这些东西？

- 是前后端沟通的媒介，也是个留档
- 方便前后端并行开发
- 方便进行一致性校验，如搞个actions测接口
- 给客户端一些接口mock支持
- 给cp一些接口mock支持



## 目前有什么现成的工具？

### mock工具

- wiremock：一个开源的测试工具，支持HTTP响应存根、请求验证、代理/拦截、记录和回放

- PowerMock

- TestableMock：2020 年 12 月开始开源，出自阿里云云效团队，主要想解决 Java 开发者在日常单元测试中经常遇到的痛点

    https://github.com/alibaba/testable-mock

### API文档管理工具

注：swagger 和 YApi 可以相互辅助，利用 swagger 生成API文档元数据，再通过 YApi 进行集中管理

如果觉得不够方便，还可以自己打造一个**中央文档系统**，笔者的前东家唯品会就是这么做的，开发使用一条命令就可以生成API元数据并上传到中央文档系统中，使用更便捷。

- swagger：丝袜哥~，是一套基于 OpenAPI 规范构建的开源工具

    https://cloud.tencent.com/developer/article/1644063

- YApi：集中式文档管理工具

# 一些有意思的问题

1. 如何方便地从spring的controller里提取出接口，并且以OAS描述？
2. 要搞自己的mock服务器么？这样子会增加运维成本哟



# 一些方案

1、分段式设计

各个段落应该保持独立，交流方式是固定的，合作伙伴是可变的

业务代码 → 单元测试 → OAS数据 → 数据中转平台 → 文档集中管理（权限管理） → mock服务器（权限管理） → 一致性测试（actions CI/CD）

可以写一套sdk，利用注解+反射实现OAS数据生成，其中可以用URL识别接口，以Hash值作为版本判断依据。

2、Apifox -- 一个我需要的平台，但是它丫的不开源，要收钱

它的定位：Apifox = Postman + Swagger + Mock + JMeter

接口文档和接口开发调试使用同一个工具
