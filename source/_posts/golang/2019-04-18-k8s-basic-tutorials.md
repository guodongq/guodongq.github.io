---
layout: post
title:  "k8s basic tutorials!"
date:   2019-04-18 10:22:16 +0800
tags: ["kubernetes"]
published: true
comments: true
script: [post.js]
excerpted: |
    kubernetes basic tutorials ...
day_quote:
  title: The word of the day!
  description: |
    Put a very powerful message.
---


# kubernetes基础

## kuberntes能为你做什么?
- 给现代化的web服务提供7*24的运行机制;
- 随时都能发布新的应用程序版本;

## kubernetes基本模块
- 创建k8s集群(kubernetes cluster)
- 部署一个应用(deploy app)
- 查看你的应用(describe app)
- 暴露你的应用(expose app)
- 扩展你的应用(scale app)
- 升级你的应用(rolling update app)

# 创建k8s集群
`Minikube可以用来搭建最简单的k8s集群，只包含一个 master以及一个node`

k8s集群的两种类型资源：
- Master： 协调集群
- Nodes： 工作节点，运行应用程序

node节点上包含kubelet进程，该进程类似一个agent，用来管理部署在该节点上的pod，它主要负责管理node节点和master之间的通信

pod是k8s抽象的概念，它是原子的，被用来表示一组、一个或多个应用程序容器，以及这些容器的一些共享的资源.Pod是给应用程序的逻辑主机建模，其中pod内包含的容器是相对紧密耦合的。pod中的容器共享一个IP地址和端口空间，总是同时定位和调度，在同一个节点上共享上下文中运行。pod中共享的资源包含：
- 共享存储，作为卷
- 共享网络，作为唯一的集群ip地址
- 有关如何运行每个容器的信息，如容器映像版本和要使用的特定的端口

# 部署应用
- kubectl version: 查看版本
- kubectl get nodes： 获取k8s的node节点
- kubectl cluster-info：获取k8s集群信息
- kubectl run app_name --image=image_name --port=8080： 部署应用，并暴露端口为8080
- kubectl get deployments： 获取部署单元


# 查看应用
- kubectl get: 列出资源
- kubectl describe: 查看资源的详细信息
- kubectl logs： 打印pod中的容器的日志
- kubectl exec: 在pod的容器中执行命令
- kubectl get pods: 获取所有pods，具体环境中需要带上namespace(--namespace=abc)
- kubectl describe pods: 获取所有pods的详细,具体环境需要带上namespace
- kubectl describe pod/$pod_name
- kubectl　logs $POD_name: 获取pod中容器的日志，具体环境需要带上namespace,如果pod中有多个容器，会提示需要查看哪一个容器的日志，要用下面的方式进行查看
  kubectl logs $pod_name  $container_name： 如果pod中包含多个容器，需要用这种方式查看日志
- kubectl exec -it --namespace=$NAMESPACE_NAME $POD_NAME --  sh： 进入具体的pod中查看日志


# 暴露应用
`pod的生命周期毕竟有限，可能由于其他的原因导致终结，k8s集群发现出现该种情况，会重建新的pod替换原来消亡的pod，但是新的pod的ip地址会发生改变，为了适配因pod的改变导致的ip地址的变化，需要增加一种方式自动协调pod的这种改变`

- Service： Kubernetes中的服务是一个抽象，它定义了一组逻辑Pod和访问它们的策略。
- Service支持依赖的pod之间的松散耦合
- 服务可以通过在ServiceSpec中指定类型以不同的方式公开
- 有些服务没有定义`selector`, 没有定义`selector`的服务，不会创建【对应的端点对象】
- 服务使用标签和选择器匹配一组pod，选择器是一种分组原语，允许对Kubernetes中的对象进行逻辑操作

几种ServiceSpec类型：
- ClusterIP(默认)： 在集群中的内部IP上公开服务。这种类型使服务只能从集群中访问。
- NodePort： 使用NAT在集群中每个选定节点的相同端口上公开服务。使用<NodeIP>:<NodePort>从集群外部访问服务。ClusterIP的超集。
- LoadBalancer： 在当前云中创建一个外部负载平衡器(如果受支持)，并为服务分配一个固定的外部IP。NodePort的超集。
- ExternalName： 通过返回带有名称的CNAME记录，使用任意名称(在规范中由externalName指定)公开服务。没有使用代理。这种类型需要使用v1.7或更高版本的kube-dns。


pod和deployment的关系?
pod是单一或者一组容器的合集，deployment是pod版本管理的工具，用来区分不用版本的pod
pod是可以单独创建并进行生命周期管理的，单独创建pod的时候就不会出现depoloyment，但是创建deployment一定会创建pod，因为pod是一个基础单位。
任何控制器单位的具体实现必须落到pod中去实现。


标签Label，是k/v键值对，可以有以下几种使用方式：
- 为开发、测试和生产指定对象
- 嵌入版本标记
- 使用标签对对象进行分类


# 扩展应用

# 升级应用
