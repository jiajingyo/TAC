# TAC 自动化测试集管理系统
[![ice](https://img.shields.io/badge/developing%20with-ICE-2077ff.svg)](https://github.com/alibaba/ice)

# 前言
分享一个最近实现的一个自动化测试管理系统
- 为什么做这个东西：市面上API的管理和执行系统非常多，测试用例管理的系统也很多，但是管理和执行测试集的系统不多，github上没有找到合适的，加上公司需要这么个东西。
- 为什么这么设计：想做一套基于TestNG framework深度定制的自动化测试管理平台
- 开发的难易程度: 前后端分离, 后端写逻辑提供接口和服务，前端通过开源工具快速构建。
- 为什么要写个分布式Case执行器不用Jenkins执行调度: 因为通过 消息队列三种模式 可以间接满足各种复杂的执行情况。
- 目前还有很多设想的功能还没实现，期望各位大佬一起PR

# 一、简介
## 1.1 概述
TAC 是应对与TestNG的自动化测试用例管理系统，以TestNG package出的jar 和TestNG 的xml 文件来驱动执行测试集。
目的在于对测试集进行管理和回归，分布式的Case执行器可以 应对以后大量Case并发执行的需求，加速回归时间。

## 1.2 工程介绍
- TACase 是前端项目 技术栈:React, IceWork, IceDesign
- TACase_Server 是后端项目 技术栈: Mybatis, Dubbo, SpringBoot, Thymeleaf
- TACase_Executor 是case执行器项目 技术栈: XXL-MQ SpringBoot

## 1.3 特性
- 分布式Case执行器: 加速执行大量回归测试集的时间
- DashBoard: 测试集，执行，浏览 数据的统计
- 简单全面： 对于测试集的管理，执行，和报告的查看
- 与TestNG解耦：跟TestNG 库的关联 通过Listener, 即对TestNG测试用例代码无侵入.(后期还需要继续解耦,或者合并TestNG)
- 执行钩子: 提供相应测试集的 执行钩子 ，方便CI

## 1.4 背景
### Why TAC
TAC 是TA Case简写，在将来测试集数量多，总类多的时候，如何筛选回归用例，如果管理回归用例以及非测试开发人员还需要搭建环境来进行回归会成为一个问题
- 管理用例： 系统上对每个测试集设置了项目的字段，及搜索的功能
- 筛选用例： 每个测试集对应一个TestNG xml文件，使用自带的 group，及选择package 功能进行筛选
- 执行用例： 将执行Case 托付给Case执行器，可以随时对期望回归的测试集进行回归。

### Why XXL-MQ
执行器用到了XXL-MQ，是因为XXL-MQ是一款轻量级分布式消息队列，支持串行、并行和广播等多种消息模型，且开源
- 目前流行的ActiveMQ、RabbitMQ和ZeroMQ等消息队列的软件中，大多为了实现AMQP，STOMP，XMPP之类的协议，变得极其重量级(如新版本Activemq建议分配内存达1G+)，但在很多Web应用中的实际情况是：我们只是想找到一个缓解高并发请求的解决方案，一个轻量级的消息队列实现方式才是我们真正需要的

## 1.5 环境
- Zookeeper3.4+
- Jdk1.8+
- Mysql5.5+

# 二、系统设计
## 2.1 系统框架图
![image](https://github.com/yili1992/TAC/raw/master/assets/1.png)


## 2.2 名词解释
- Icework: 阿里开源工具，通过GUI工具极速构建中后台前端应用
- QUEUE：点对点模式, 消息进去队列之后, 只会被消费一次。同一Topic下的多个Consumer并行消费消息, 吞吐量较大
- SERIAL_QUEUE：点对点模式, 消息进去队列之后, 只会被消费一次。 但是,同一个Topic下只会有一个Consumer串行消费消息, 适用于严格限制并发的场景

# 三、安装部署
请按照下面顺序进行安装部署
## 3.1 安装部署TAC
- [XXL-MQ](http://www.xuxueli.com/xxl-mq/#/) xxl-MQ进行消息队列来分发消息，安装broker即可，阅读3.2, 3.3, 3.4 然后打出war包 部署在tomcat上 正常访问到管理平台 标识安装成功
- [TAC_Server安装](https://github.com/yili1992/TAC/tree/master/TACase_Server)
- [TAC_Executor安装](https://github.com/yili1992/TAC/tree/master/TAC_Executor)

# 四、更新日志
## v1.0.0
- TestCase 增加/管理/执行
## v1.1.0
- 增加定时任务功能
- springboot 升级到 2.0.0
- 修复case执行状态更新bug
- 版本升级需要 执行TACase_Server/tac-web/resource/quartzDB.sql 和TACase_Server/README.md 中cron_task 建表语句

# 快照
![image](https://github.com/yili1992/TAC/raw/master/assets/2.png)
![image](https://github.com/yili1992/TAC/raw/master/assets/3.png)
![image](https://github.com/yili1992/TAC/raw/master/assets/4.png)
![image](https://github.com/yili1992/TAC/raw/master/assets/5.png)

