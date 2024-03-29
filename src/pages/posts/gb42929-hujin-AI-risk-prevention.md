---
layout: '../../layouts/MarkdownPost.astro'
title: '互联网金融智能风险防控技术要求'
pubDate: 2023-11-26
description: '国标GBT42929 互联网金融智能风险防控技术要求'
author: 'UncleLLD'
cover:
    url: 'https://i.imgs.ovh/2023/11/28/pwrO5.th.png'
    alt: 'cover'
tags: ["风控"]
theme: 'dark'
featured: true
---



## 《互联网金融智能风险防控技术要求》

8月6日，国家市场监督管理总局和国家标准化管理委员会发布《互联网金融智能风险防控技术要求》（GB/T 42929-2023）（以下简称“《要求》”），将于2023年12月1日实施。



![cover|inline](https://i.imgs.ovh/2023/11/28/pw2Es.png)



《要求》规定了互联网金融场景下智能风险防控技术所需满足的**技术框架、功能要求、技术要求、实现的安全要求以及运行要求等**。适用于开展互联网金融业务的组织机构，以及提供智能风险防控技术服务的机构建设、运行和优化智能风险防控系统防控互联网金融风险。

《要求》提到，根据不同的主体对象，需要从**交易维度、账户维度、商户维度、客户维度**设计并实施风险防控策略，建立“点、线、面、体”的跨场景、跨渠道的全生命周期风险防控体系。

《要求》显示，智能风险防控系统主要包含**“智能风险防控功能”、“智能风险防控技术”、“智能风险防控系统安全”、“智能风险防控系统运行”**4大部分。

![arc|inline](https://i.imgs.ovh/2023/11/28/pwZKU.png)

### 智能风险防控4大功能

风险防控各环节核心功能包括**风险监测、风险识别与分析、风险评价、风险处置**。



#### 1. 风险监测

《要求》提到，风险监测主要技术包括9项内容。比如应**覆盖典型互联网金融业务流程**；应根据互联网金融业务**流程要素设置风险监测对象**；应同时具备**自动化监测功能和人工监测能力**，并根据实际情况进行合理选择；应具备对业务**相关数据的监测能力**；**监测结果应实时传递到风险识别与分析功能模块**等。



#### 2. 风险识别与分析

《要求》提到，风险识别主要技术包括10项内容。比如应支持**信息真实性和信息一致性的信息标识风险识别**；应采用**层次化的技术方法进行风险识别**；应具备**快速识别层实现对高发风险的快速识别**；宜支持**标识技术**，从设备、客户、账户等多维度对风险进行识别等。而在风险分析上，分为**在线分析和离线分析**，其中前者又包括**在线实时分析和在线准实时分析**。



#### 3. 风险评价

**风险计算采用定性、定量相结合的计算方法**。风险计算方法**针对业务面临风险的大小进行准确排序**，以确定风险的处置策略。另外，通过风险计算，对业务实际风险进行综合分析与评价，并**对风险计算值进行等级化处理**。



#### 4. 风险处置

《要求》提出，风险处置的基本要求有3项。而风险处置方式包括**实时风险处置和残余风险处置**。



### 智能风险防控技术

《要求》对智能分析与处理4个部分各提出多项要求，**从风险防控规则与模型、智能分析与处理**2方面阐述智能风险防控技术。

#### 1. 风险防控规则与模型

* **风险防控规则**的主要技术包括4项内容，比如**规则种类应包括名单型、流量型、时序型、匹配型和地理位置型**等。
* **风险防控模型**的主要技术包括7项内容，比如宜遵循业务**需求分析、建模设计、模型训练、模型调优、模型测试定版、模型发布上线、模型监控、模型运行维护和模型下线等**模型全生命周期流程进行管理等。



#### 2. 智能分析与处理

智能分析与处理由4个部分构成：

* **一是智能分析与处理逻辑**，包含5项要求，比如应**构建覆盖业务全渠道、全场景、全时段的企业级智能风险防控系统，集智能决策中心、指标引擎、知识图谱、图计算、机器学习平台为一体**，支持跨渠道、跨场景的联防联控，支持全局风险核查与运营，支持风险决策效果量化评估与运营等。
* **二是智能分析与处理数据**，包含4项要求，比如应具备**多渠道、多场景、多源数据（实时业务数据、外部三方数据、内部离线数据）的对接能力，为实时/准实时系统提供输入**等。
* **三是智能分析与处理技术**，包含8项要求，比如应具备对**数据进行实时/离线分析、处理和查询能力；应具备计算资源动态和线性扩展能力等**。
* **四是智能分析与处理管理**，包含6项要求，比如应**支持在线规则、模型、策略的灵活上下线和更新**；应对**规则、模型、策略预估时间具备有效的管理机制**，避免模型异常影响正常风险防控等。



### 智能风险防控系统安全

智能风险防控系统安全分为**基础安全、应用安全、数据安全**3方面。

#### 1. 基础安全

安全技术宜按照GB/T 22239中安全物理环境、安全通信网络、安全区域边界、安全计算环境、云计算安全扩展要求、移动互联安全扩展要求、物联网安全扩展要求、工业控制系统安全扩展要求等相关条款执行。安全管理宜按照GB/T 22239中安全管理中心、安全管理制度、安全管理机构、安全管理人员、安全建设管理、安全运维管理等相关条款执行。



#### 2. 应用安全

应用安全包括**浏览器缓存安全、身份认证安全、会话管理安全、权限管理安全、日志规范、安全审计、引用第三方资源安全**7部分。



#### 3. 数据安全

数据安全包括**基本数据安全、数据生命周期安全、个人信息安全**3部分。

具体而言，基本数据安全有7项要求。**数据生命周期应遵循合法正当、目的明确、选择同意、最小够用、全程可控、动态控制、责权一致的基本原则**；数据服务应符合GB/T 35274中针对数据进行**采集、传输、存储、使用、删除、销毁的相关要求**。而涉及个人信息收集、存储、使用、共享、转让、公开披露、删除等处理的，应符合GB/T 35273中规定的个人信息处理活动的原则和安全要求，设计并实施覆盖互联网金融业务风险防控全生命周期的个人信息保护策略，并在必要时对执行这些要求导致的对智能分析与处理的适用性和精度的影响进行评估。



### 智能风险防控系统日常运行

智能风险防控系统日常运行时，应满足10项要求。比如应根据业务重要程度制定业务连续性计划及业务中断影响分析；**应定期对数据、数据处理系统、网络系统、基础设施进行备份**；宜采用多重TP指标来监测风险防控系统或服务的性能，包括但不限于TP50、TP90、TP99、TP999等。

而应急响应方面，在风险事件影响业务正常运行时，智能风险防控系统按照应急响应框架智能分析事件级别，识别风险干系人，并触发预警和相应级别的应急预案，支撑风险事件的应急响应。应急响应包括事前预案智能推荐、事中智能应急处置、事后智能优化。
