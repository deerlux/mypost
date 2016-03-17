title: 什么是kubernetes 
date: 2016-03-17 12:58:01
tags: Linux 云计算
---

【译者按】译自[kubernetes官网文档](http://kubernetes.io/docs/whatisk8s/)，主要介绍kubernetes的基本概念以及应用场景，K8s的设计理念，以及K8s能干什么不能干什么。

# 什么是kubernetes?
Kubernetes是一款应用于集群的，[容器自动部署、扩展和管理的开源平台](http://www.slideshare.net/BrianGrant11/wso2con-us-2015-kubernetes-a-platform-for-automating-deployment-scaling-and-operations)，提供了一种以容器为中心的基础架构。

利用kubernetes，你可以快速高效地响应客户如下请求：
* 应用程序的动态、精准部署
* 应用程序的动态扩展
* 无缝推出新功能
* 按需优化使用硬件资源

我们的目标是提供一套减轻应用程序在公用云或私有云上运行负担的，生态环境的组件和工具。

## kubernetes是：

* 便携的：公有云、私有云、混合云、多云
* 可扩展的：模块化、即插即用、钩子化、组合化
* 自动修复的：自动布局、自动重启、自动副本、自动伸缩

Kubernetes项目是2014年由Google公司启动的，Kubernetes在[Google公司15年生产环境经验基础上](https://research.google.com/pubs/pub43438.html)，结合了社区的一些优秀点子和实践而构建的。

准备好[开始了](http://kubernetes.io/docs/getting-started-guides/)吗？

# 为什么要用容器

寻找一下你用[容器](http://aucouranton.com/2014/06/13/linux-containers-parallels-lxc-openvz-docker-and-more/)理由？

*传统方法*部署应用程序是利用操作系统的包管理器向主机上安装应用，这种方法的一个弊端是应用程序、配置、支撑库以及生命周期等经常与主机操作系统纠缠在一起，当然你也可以构建虚拟机镜像来实现可控的发布，但是虚拟机比较重量化并且其可移植性也不好。

*新方法*部署容器则是基于操作系统级别的虚拟化而不是硬件层面的虚拟化实现的，这些容器相互之间以及容器与主机之间是隔离的：它们拥有自己的文件系统，共享运算资源，但它们彼此看不到对方。容器的构建也比虚拟机的构建要容易，由于它们不依赖于运行平台和文件系统，因此可以跨越云和操作系统发行版实现可移植性。

因为容器小而快速，一个应用程序可以打包成容器镜像，这种应用与镜像的一一对应关系，彻底发挥了容器的好处。

1. 永久的容器镜像可以在应用的开发阶段进行构建，而不是留在部署阶段，因为每个应用并不需要在其生命周期的其他阶段或是在正式交付生产环境时进行修改，这使得开发环境和生产环境高度一致。
1. 容器相对于虚拟机更透明，便于监控和管理，特别是当容器的进程周期是由管理平台进行管理而不是由隐藏在容器中的超级进程进行管理时。
1. 一个应用一个容器，对容器的管理相当于对应用的管理。

容器的优点可以归结为:

* **敏捷地进行应用构建和部署**: 相比于虚拟机的管理，容器的管理更为容易和高效。
* **可连续进行开发、集成和部署**：采用快速回滚的方式，使其具备可靠和频繁的容器镜像构建和部署的能力（基于镜像的永久性）。
* **开发和运维可分开考虑**：创建应用程序的容器镜像是在构建/发布阶段而不是在部署阶段，因此将应用程序与基础设施进行了解耦。
* **可保持开发、测试和生产环境的一致性**：在笔记本上运行和放在云端运行效果是一样的。
* **云端和操作系统的可移植性**:在Unbuntu、RHEL、CoreOS、on-prem、Google Container Engine以及其他地方随意运行。
* **应用为核心的管理**：（相比于虚拟机的方式）抽象的层级由在虚拟硬件上运行操作系统提升到在操作系统上利用其逻辑资源直接运行应用程序。
* **松耦合、弹性、分布式、微服务**: 应用被分为小型独立的片，可以动态进行部署和管理，无须在一个大的单一目的的主机上运行胖的程序栈。
* **资源隔离**: 可预测的程序性能。
* **资源利用最大化**: 高性能、高密度。

## 为什么我需要Kubernetes，它能干什么

Kubernetes可以实现在物理集群或虚拟机集群上调度和运行容器，当然它还可以做得更多。

为了充分发挥容器的优势并将传统的应用部署方式甩开，需要容器的部署与运行独立于基础设施。

然而，当特定的容器不再与特定的主机绑定时，**主机为中心**的基础设施也不再适用：负载均衡、自动扩展等，因此需要**容器为中心**的架构，这便是kubernetes所提供的。

Kubernetes满足了应用程序在生产环境中的一些通用需求，例如：

* [协同定位的辅助进程](http://kubernetes.io/docs/user-guide/pods/)，利用复杂应用部署，并且还保持了单容器单应用的模型。
* [挂载存储系统](http://kubernetes.io/docs/user-guide/volumes/)
* [分布式加密管理](http://kubernetes.io/docs/user-guide/secrets/)
* [应用健康状况检查](http://kubernetes.io/docs/user-guide/production-pods/#liveness-and-readiness-probes-aka-health-checks)
* [应用实例副本](http://kubernetes.io/docs/user-guide/replication-controller/)
* [水平自动扩展](http://kubernetes.io/docs/user-guide/horizontal-pod-autoscaler/)
* [命名与发现](http://kubernetes.io/docs/user-guide/connecting-applications/)
* [负载均衡](http://kubernetes.io/docs/user-guide/services/)
* [滚动升级](http://kubernetes.io/docs/user-guide/update-demo/)
* [资源监控](http://kubernetes.io/docs/user-guide/monitoring/)
* [日志的获取和注入](http://kubernetes.io/docs/user-guide/logging/)
* [支持自省和调试](http://kubernetes.io/docs/user-guide/introspection-and-debugging/)以及，
* [认证和授权](http://kubernetes.io/docs/admin/authorization/)

上述功能提供了平台即服务(PaaS)的简易性以及基础设施即服务(IaaS)的灵活性，提升了跨基础设施移植的方便性。

想得到更多细节内容，参加[用户指南](http://kubernetes.io/docs/user-guide/)

## 为什么如何Kubernetes成为了一个平台

尽管Kubernetes提供了很多功能，但总有新的应用场景需要新的特性，应用相关的工作流可流水线化以加速开发速度，最初设计的Ad hoc机制经常在扩展时需要更为灵活的自动化。这也是为什么Kubernetes被设计成为一个构建组件和工具生态的平台，使得应用的部署、扩展和管理更为容易。

[标签](http://kubernetes.io/docs/user-guide/labels/)授权用户可以按需组织他们的资源，[标注功能](http://kubernetes.io/docs/user-guide/annotations/)能够使用户对特定的资源添加一些定制信息，以方便其工作流的实现，并且为管理工具的状态检查提供了一种解决方法。

此外，[Kubernetes控制面板](http://kubernetes.io/docs/admin/cluster-components)是基于API进行构建的，这些[API](http://kubernetes.io/docs/api/)对于开发者和用户同样适用，用户可以基于此写出自己的控制器、[调度器](https://github.com/kubernetes/kubernetes/tree/release-1.2/docs/devel/scheduler.md)等，甚至可以有针对性的用[自己的API](https://github.com/kubernetes/kubernetes/blob/release-1.2/docs/design/extending-api.md)写一些通用的[命令行工具]()http://kubernetes.io/docs/user-guide/kubectl-overview/)

这种[设计](https://github.com/kubernetes/kubernetes/blob/release-1.2/docs/design/principles.md)使得用户可以在Kubernetes基础上构建一些其他的系统。

## Kubernetes并不是：

Kubernetes并不是传统意义包打天下的Paas系统，我们在一些重要的方面保留了用户选择的权利。

* Kubernetes并不限制支持的应用类型，也不指定应用的框架（例如[Wildfly](http://wildfly.org/)），不限制支持的语言环境（例如：Java, Python, Ruby），并不迎合[12-factor application](http://12factor.net/)，也不是区分“应用”和“服务”。Kubernetes目的在于支持尽可能多样的负载，包括有状态、无状态、数据处理型的负载，只要应用程序可以运行在容器中，就可以很好地跑在Kubernetes。
* Kubernetes不提供中间件（例如消息总线）、数据处理框架（如Spark）、数据库（如mysql），也不提供集群存储系统（如Ceph），这些应用运行在Kubernetes上。
* Kubernetes没有点击即部署的服务前端。
* Kubernetes并不提供源代码-镜像的处理空间，它并不部署源代码也不会构建你的应用，持续集成(Continuous Integration: CI)工作流要求用户和项目分别有自己的需求和选项，所以我们支持分层CI工作流，但是并不指定工作流该怎么工作。
* Kubernetes允许用户自由选择日志、监视和告警系统。（尽管作为概念验证也提供了相应的集成）
* Kubernetes并不提供也不指定一种综合的应用配置语言/系统（如[jsonnet](https://github.com/google/jsonnet)）。
* Kubernetes并不提供也不采用任何综合的机器配置、维护、管理或自我修复系统。

另一方面，很多PaaS系统运行在Kubernetes上，例如[Openshift](https://github.com/openshift/origin)、[Deis](http://deis.io/)以及[Gondor](https://gondor.io/)，你也可以定制你自己的Paas，整合一个CI系统，或者仅用Kubernetes也挺好，将容器镜像拿来直接部署到Kubernetes上。

由于Kubernetes工作在应用层而不是仅在硬件层，它提供了一些一般Paas所能提供的通用特性，比如部署、扩展、负载均衡、日志和监控等，但Kubernetes并非一个整体，这些功能都是可选和可插拔的。

此外，Kubernetes并不是一个业务流程系统，它消除了业务流程的需要，业务流程是指按照预定义的工作流执行任务，先A再B再C。相反，Kubernetes包含了一套独立、可组合的控制处理来连续驱动当前状态向所需状态转换，它不关心你怎么从A到C的。中央控制也并不需要，这种方法更像舞蹈，这使得系统更易于使用，也更强大、鲁棒、弹性和可扩展。

## Kubernetes是什么意思？K8s？

**Kubernetes**一词源自希腊语，意思是舵手或飞行员，并且是总督或控制论的根源。（The name Kubernetes originates from Greek, meaning “helmsman” or “pilot”, and is the root of “governor” and “cybernetic”）。K8s是将中间的8个字母用8来简写的结果。

