# 在沙箱环境中试验Swarm

译自Docker Swarm[官方文档](https://docs.docker.com/swarm/install-w-machine/)，译者deerlux@163.com。通过在vagrant virtualbox环境中启动swarm相关服务，对swarm环境的基础配置能有个初步的概念。

本指南将向你展示如何创建一个Docker Swarm——Docker的原生集群管理工具。

你将使用Docker Toolbox在你机器上安装一个Docker Machine以及其他相关的工具，然后你将利用Docker Machine提供一组Docker Engine的主机，最后你将利用Docker客户端连接这些主机，你可以创建一个发现令牌，创建一个包含一台Swarm Manager以及其他节点机的集群，并用Swarm进行管理。

当你完成时，你将拥有一个在你的windows或Mac主机上的VirtualBox上运行的Swarm，这样便可以用此沙箱环境进行开发。

如果在Linux系统上用Swarm，请看[在生产环境中构建Swarm集群](https://docs.docker.com/swarm/install-manual/)。

> 译注：其实在Linux系统下构建沙箱环境也是完全可以的，如果你只是为了体验一下Swarm的功能性能，何必一开始就安装在物理机上呢，我便是在Archliunx下的虚拟机中完成的。


## 安装Docker Toolbox

下载并安装[Docker Toolbox](https://www.docker.com/docker-toolbox)。

这个工具箱在本地Windows或Mac主机上安装了一些工具，在这个练习中，你将会用到其中的三个工具。

* **Docker Machine**: 用于部署运行Docker Engine的虚拟机。
* **VirtualBox**: 作为部署Docker Machine的环境。
* **Docker客户端**: 从你的本地计算机连接到Docker Engine虚拟机并向Docker命令用于创建Swarm。

> 译注：那个Toolbox实际上是为Windows和Mac准备的，如果在Linux系统上述三个东西大多都可以直接装。

接下来的部分将为你深入解释上述每一个工具，以下虚拟机会简称VM。


## Create three VMs running Docker Engine

Here, you use Docker Machine to provision three VMs running Docker Engine.

