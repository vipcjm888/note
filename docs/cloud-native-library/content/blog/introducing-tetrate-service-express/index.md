---
title: "Tetrate推出针对 Amazon EKS 设计的服务网格解决方案TSE"
subtitle: ""
summary: "Tetrate Service Express (TSE)是一款基于开源软件的服务连接、安全和弹性自动化解决方案，专为Amazon EKS设计。"
authors: ["Tetrate"]
tags: ["Tetrate","TSE"]
categories: ["Service Mesh"]
date: 2023-04-13T16:13:33+08:00
lastmod: 2023-04-13T16:13:33+08:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
links:
  - icon: language
    icon_pack: fa
    name: 阅读英文版原文
    url: https://tetrate.io/blog/introducing-tetrate-service-express/
---

Tetrate Service Express (TSE)是一款基于开源软件的服务连接、安全和弹性自动化解决方案，专为Amazon EKS设计。

本文译自：[Tetrate Service Express介绍](https://tetrate.io/blog/introducing-tetrate-service-express/)

## 快速实现Amazon EKS上安全和弹性的服务网格

今天我们很高兴地宣布Tetrate Service Express (TSE)，这是一款针对Amazon EKS的服务连接、安全和弹性自动化解决方案。我们基于Istio和Envoy等开源服务网格组件构建了TSE，并针对AWS对TSE进行了简化安装、配置和操作的优化。如果您的团队正在AWS上进行服务网格实验，并且需要快速证明投资回报率，而无需掌握复杂的Istio和AWS基元，那么TSE就是适合您的选择！如果您的团队已经在单个集群上拥有了服务网格，但希望将网格扩展到多个集群甚至区域，那么TSE也可以帮助您。事实上，TSE是唯一一款基于开源软件并针对AWS进行优化的产品，预先集成了最受欢迎的AWS服务，可在几分钟内让您上手。

如果您想快速了解Tetrate Service Express的功能，在[此处](https://tetr8.io/tse)加入等待列表以获取您的评估副本。

如果您想深入了解Tetrate Service Express的新功能，请继续阅读。

## 为什么选择Tetrate Service Express

如果您的组织想要创造更好的客户体验、提高运营效率或保护知识产权，那么很可能必须构建和运行软件——更高质量、更快速的发布、更好的正常运行时间。而且很可能，应用程序开发团队、运营团队和平台团队正在借助两种创新趋势来实现这些目标：云基础设施的灵活性和微服务。

在云中构建微服务时，许多团队选择Amazon EKS的便利性。然而，随着他们扩展EKS工作负载，他们会发现自己面临以下问题：

- 如何保护分布在AWS上的微服务组件？
- 如何在更新或负载下单个组件失败时保持可靠性？
- 当一切都是动态的，手动配置不再起作用时，如何管理服务之间的流量？

越来越多的平台运营团队会在微服务之上创建一个专用的基础设施层——服务网格——它提供了跨微服务组件的服务发现、安全性、认证和可观测性。Istio和Envoy是服务网格控制平面和数据平面的开源标准，但Istio和Envoy只是部分解决方案，因为它们为运行Amazon EKS的团队添加了新的操作复杂性。

![Amazon EKS上的服务网格挑战](image.png)

Tetrate Service Express为平台团队提供了Istio和Envoy之上的服务网格自动化。它处理在Amazon EKS上安装和配置开源组件，与AWS服务集成，并为平台运营商提供管理控制台，以快速配置服务网格以实现安全、弹性和可观察性。

![Tetrate Service Express提供了一种一致的、可自动化的方法，用于在Amazon EKS内部和跨Amazon EKS连接、保护和观察服务](image-1.png)

## 快速安装Amazon EKS和集成AWS服务

让Istio在EKS上运行可能变得更加容易（例如使用[Tetrate Istio Distro EKS add-on](https://tetrate.io/blog/tid-add-on-for-eks/)），但在您可以操作功能性服务网格之前，还需要完成更多的工作：

- 如何确保所有服务网格组件都已正确安装和配置？
- 如何定义Istio和Envoy周围的网络基础架构，以实现安全的跨集群连接和可用性？
- 如何观察和排查网格上的服务，以协助扩展和优化？

TSE可以使用Helm chat轻松安装在EKS上，并与Route 53和NLB（或其他AWS负载平衡器类型）轻松集成，以便您可以快速：

- 定位服务的边缘和入口网关，以管理和安全地控制入口和出口流量。
- 通过内置身份验证、速率限制、HA和安全性提供应用程序的API。
- 获取服务和应用程序的MELT（度量、事件、日志、跟踪）。

![使用Tetrate Service Express和示例配置开始](image-2.png)

## 一步式服务间的mTLS加密

服务网格的第一个用例之一是使用mTLS启用服务间加密，但一旦你扩展到多个集群，事情就会很快变得复杂：

- 如何在各个集群之间建立和管理单个信任根？
- 如何在全面支持零信任姿态的情况下，强制执行mTLS？
- 如何定期轮换证书，并在泄漏或损坏证书的情况下做出响应？

Tetrate Service Express通过提供以下内容使服务易于加密并应用零信任姿态：

- 内置的易于使用的证书颁发机构，可在所有群集中轮换和管理证书。
- 在管理级别上定义对所有服务的mTLS需求。
- 从默认的拒绝所有姿态开始创建细粒度的访问策略。

![默认设置mTLS](image-3.png)

## 集群和区域之间的易于故障转移设置

在定义应用程序基础架构时，设置服务的内部高可用性可能是比较复杂的事情，特别是在不同的区域内。一些可能遇到的问题包括：

- 如何配置Route53、Amazon负载均衡和其他负载均衡服务，以在不同的集群之间实现可靠的HA？
- 如何在没有Hairpinning的情况下设置跨集群通信和内部高可用性？
- 当服务失败时如何自动化网络配置更改？

Tetrate Service Express通过提供以下功能使获取服务HA易于实现：

- 对服务发布进行自动配置的入口、Amazon负载均衡和Route 53。
- 为内部高可用性自动配置内部和东/西网关。
- 动态重新配置网络规则，以在最小中断的情况下在集群之间和之间传输流量。

![配置跨区域故障转移](image-4.png)

## 接下来

这只是我们要从Tetrate Service Express中突出显示的一些功能。Tetrate Service Express将在本月晚些时候发布技术预览版，但是您现在可以加入等待列表，以在发布时获取评估副本。一旦您加入等待列表，我们将与您联系，向您提供访问评估软件、新文档门户网站和Slack社区频道的信息。

我们还计划推出一系列额外的博客、视频和网络研讨会，以展示更多TSE的功能。下一场网络研讨会定于5月举行，请[立即注册](https://app.livestorm.co/p/06a20bee-58b0-4976-977d-c2e4b16dbe68)。最后，请不要忘记在[Twitter](https://twitter.com/Tetrateio)或[LinkedIn](https://www.linkedin.com/company/tetrate)上关注Tetrate，以便在新的TSE内容可用时获得即时更新！
