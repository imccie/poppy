介绍
#####


kubernetes与openshift
================================

kubernetes本身已成为红帽当中的一种PAAS，PAAS是云计算当中的一种形式，叫平台即服务，红帽内部的平台即服务产品叫openshift，而openshift的核心，就是kubernetes，所以从这个角度来讲kubernetes只是一个容器编排工具。
它还没有到完整的PAAS这种云计算平台的标准，openshift是其中一个实现，我们也可以理解为openshift是kubernetes的发行版。

kubernetes做的非常底层，真正离用户的终端使用，要自己使用kubernetes需要自己在生产上还要自己部署很多工具，以解决对devops的需要，或者解决自己对完整的PAAS平台的需要，而openshift就是一个完整的集成的解决方案。
它里面拥有了paas平台devops平台需要的一切的工具，都直接整合进去了。


kubernetes的特点
======================

#. 自动装箱
    基于资源依赖及其他约束能够自动完成容器的部署，而且不影响其可用性。

#. 自我修复
    有自愈能力,一旦一个容器崩了，由于考虑到容器非常轻量的问题，它可以在一秒钟启动。假设镜像是做好的，镜像是做好完成的，能够完成最快在一秒钟启动起来。有些应用程序初始化自身笔记慢的，则需要的时间更长一点。
    所以，如果一个容器崩了，我们没必要修复它，将容器直接kill掉，然后重新启动一个，用这种方式来替代修复。
    有了k8s这样的容器编排平台后，我们更多的关注的是群体，而不是个体了，个体坏了之后直接干掉，再重新启动一个。

#. 水平扩展
    一个容器不够，再启一个，还不够就再启一个，可以不断的进行向上扩展，只要我们的物理平台资源支撑是足够的。

#. 服务发现和负载均衡
    当我需要在k8s上运行很多的应用程序的时候，程序和程序之间那种关系像微服务化以后，一个微服务如果依赖于其他服务，他能够用服务发现的方式找到依赖它的服务，更重要的是，每一个服务如果启了多个容器，它能实现自动做负载均衡。

#. 自动发现和回滚
#. 密钥和配置管理
    配置集中化，将配置信息保存在k8s之上的一个对象当中，能让每一个用到此配置的容器启动时直接加载，所以模拟了配置中心的作用。

#. 存储编排
    把存储卷实现动态工具，也就意味着，某一个容器，需要用到存储卷时，根据容器的自身需求，创建能够满足它的需要的存储卷，从而实现存储编排。

#. 任务批量处理运行


- kubernetes就是一个集群

    从我们此前的运维的角度来理解的话，kubernetes其实就是一个集群，从多台主机的资源整合成一个大的资源池，并统一对外提供计算、存储等能力的集群。
    我们在每一台主机上都安装kubernetes的相关应用程序，并通过这个应用程序协同工作，把多个主机当做一个主机来使用，其实仅此而已。

kubernetes的功能模型
----------------------------

kubernetes是master/nodes 的模式，nodes节点也成为worker节点，用来干活的。  一般有一个或多个节点是主节点，主节点一般三个就够了，做一个冗余。

- 用户怎么在集群中去运行容器呢？

    用户的客户端请求要先发给master，把创建、启动容器的请求交给master，master当中，有一个调度器去分析各node现有的可用资源状态，找一个最佳适配运行用户所请求的容器的节点，并把它调度上去，
    由这个node本地的docker或其他容器引擎负责把这个容器启动起来。

    node在启动这个容器时，先检查本地是否有镜像，如果没有镜像，就把镜像先拖下来。

    kubernetes也可以把自己托管在kubernetes上，自己托管自己。

    接受请求的只能是kubernetes cluster，提供服务让客户端能够远程访问


#. master
    master: API Server,Scheduler,Controller-Manager
    负责总的管理，master是整个集群的大脑，它有三个核心组件。

#. apiserver，负责接受和处理请求的。
    如何用户的请求是要创建一个容器，那这个容器不应该运行在master之上，它应该运行在node之上。那么哪一个node更合用呢？由谁来决定呢？由调度器来管理。

#. scheduler,调度器，调度容器创建的请求。
    负责调度，负责将任务分配给合适的node，master上面的一个非常非常重要的组件，scheduler，调度器。 它负责去观测每一个node之上总共可用的计算，比如cpu，内存和存储资源，并根据用户创建这个容器所需要的资源、最低需求，去判断那个node最合适。比如一个容器需要最低4G内存，那调度器需要判断哪些节点符合这个容器的运行需求，
    比如有三个节点满足容器的运行需求，那么接下来就从这3个可用节点里选择一个最佳的节点去运行，至于如何选择，那就根据我们的算法来判断的了。

    容器起来后，我们可以监控容器的健康与否，能够对容器进行可用性探测。kubelet就是来做这个的。

#. 控制器

    确保容器健康。

    kubernetes必须得有自愈能力，一旦这个容器不见了，不用用户人工参与，我只需要用另一个新的同样的个体来取代它就行了，接下来它需要在其他节点上创建出一个一模一样的容器来。

    **如何确保这个容器始终是健康的呢？**

    用于监控容器是否健康的，是控制器。

    他一旦故障之后怎么知道它是故障了呢？所以我要持续的监控他们，确保他们始终是健康的， 所以kubernetes还实现了一大堆的叫控制器的应用程序，负责去监控它所管理的每一个容器是否是健康的。
    一旦发现不是健康的，控制器就负责在向master api server发送请求，所我的容器挂了一个，你再帮我调度重新起一个。 然后master就从其他节点中挑一个重新启动起来。

    所以，这里我们有一个服务，叫做控制器，controller，这个控制器需要在本地不停的loop中，也就是循环，这个循环用于周期性探测，持续性探测它管理的容器是否是健康的，一旦不健康，
    甚至一旦不符合用户所定义的目标工作状态，就需要确保它始终不断的移向用户所期望的状态，或者说向用户所期望的状态进行迁移，以确保达到用户的期望。


    **如果控制器挂了呢？怎么办？**

    如果控制器挂了，那么容器的健康就无法得到保证了。

    那么这就要说到第三个重要的组件了.

#. controller-manager，控制器管理器，确保已创建的容器处于健康状态。

    在master之后，我们做了第三个组件，非常重要的组件，叫做控制器管理器，controller manager，控制器管理器是用来确保控制器健康的，而控制器是用来确保容器健康的，kubernetes支持众多类型的控制器，控制容器自身健康的，只是其中一种。
    如果控制器不健康了，有控制器管理器确保它是健康的，就ok了。

    那么如果控制器管理器不健康了呢？ 因此，在控制器管理器级别做冗余，master一般三个节点，master之上我们每个节点都有控制器管理器。

    .. image:: ../../../images/k8s1.png



#. pod,kuberntes里面运行的原子单元

    kubernetes并不直接调度容器的运行，它调度的是pod，pod可以理解为是容器的外壳，给容器做了一层抽象的封装。所以pod便是kubernetes之上最小的调度的逻辑单元，pod内部可以放、或者说主要就是用来放容器的。

    pod有一个工作特点，可以将多个容器联合起来加入到同一个网络名称空间中去。 一个pod中可以包含多个容器，这多个容器共享一个底层的网络名称空间，共享同一个主机名，ip地址，相当于同一个虚拟机里的内容，这是kubernetes在组织容器时一个非常非常精巧的办法，
    使得我们可以构建较为精细的容器间通信了。 同一个pod里的容器也可以共享同一个容器，存储卷属于pod，而不是属于容器。 一般一个pod里有一个主程序，然后其他容器用来辅助这个程序的运行，比如将我们一个pod里一个服务是nginx，另一个服务是filebeat，filebeat只是用来收集nginx的日志。

    我们的调度器调度的是pod，node里面运行的也是pod，一个pod内，无论它是有一个容器，还是有多个容器，一旦我们把某一个pod调度到某一个node上去运行后，这一个pod里面的容器只能运行在这同一个node之上。

    创建pod的时候，可以给pod添加标签，方便我们能快速的找到相应的pod，因为当我们想找到同一类pod的时候，比如我们创建的nginx要运行4个pod，而每个pod的名字都是不一样的，如何直接一次性找到这四个pod呢？給它贴标签，然后用标签来挑它出来就好了。

#. node
    node: kubelet,docker,
    node是kubernetes集群里面的工作节点，负责运行由master指派的各种任务，而最根本的是，它的最核心的任务就是以pod的形式去运行容器的，理论上讲，node可以是任何形式的计算设备，只有能够有传统意义上的CPU、内存、存储空间，
    并且能够装上kubernetes的集群代理程序，它都可以作为整个kubernetes的一份子去进行工作。

#. kubelet
    与apiserver交互的核心组件




kubernetes对象概述
==========================
kubernetes中的对象是一些持久化的实体，可以理解为是对集群状态的描述或期望。

包括：

- 集群中哪些node上运行了哪些容器化应用
- 应用的资源是否满足使用
- 应用的执行策略，例如重启策略、更新策略、容错策略等。

kubernetes的对象是一种意图（期望）的记录，kubernetes会始终保持预期创建的对象存在和保持集群运行在预期的状态下。

操作kubernetes对象（增删改查）需要通过kubernetes API，一般有以下几种方式：

- kubectl命令工具
- Client library的方式，例如 client-go

Spec and Status
====================
每个kubernetes对象的结构描述都包含spec和status两个部分。

- spec：该内容由用户提供，描述用户期望的对象特征及集群状态。
- status：该内容由kubernetes集群提供和更新，描述kubernetes对象的实时状态。


任何时候，kubernetes都会控制集群的实时状态status与用户的预期状态spec一致。

例如：当你定义Deployment的描述文件，指定集群中运行3个实例，那么kubernetes会始终保持集群中运行3个实例，如果任何实例挂掉，kubernetes会自动重建新的实例来保持集群中始终运行用户预期的3个实例。


对象描述文件
=====================


当你要创建一个kubernetes对象的时候，需要提供该对象的描述信息spec，来描述你的对象在kubernetes中的预期状态。

一般使用kubernetes API来创建kubernetes对象，其中spec信息可以以JSON的形式存放在request body中，也可以以.yaml文件的形式通过kubectl工具创建。

例如，以下为Deployment对象对应的yaml文件：

::

    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: nginx-deployment
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9
            ports:
            - containerPort: 80

::

    # create command
    kubectl create -f https://k8s.io/docs/user-guide/nginx-deployment.yaml --record
    # output
    deployment "nginx-deployment" created


必须字段
=============

在对象描述文件.yaml中，必须包含以下字段。

apiVersion：kubernetes API的版本。

kind：kubernetes对象的类型。

metadata：唯一标识该对象的元数据，包括name，UID，可选的namespace。

spec：标识对象的详细信息，不同对象的spec的格式不同，可以嵌套其他对象的字段。

k8s里的各种对象
===================

- RESTful
    - GET,PUT,DELETE,POST,...
    - kubectl run,get,edit,...
- 资源: 对象
    - workload:Pod,ReplicasSet,Deployment,StatefulSet,DaemonSet,Job,Cronjob,...
    - 服务发现及均衡:Service, Ingress,...
    - 配置与存储: Volume,CSI
        - ConfigMap,Secret
        - DownwardAPI
    - 集群级资源
        - Namespace, Node, Role, ClusterRole,RoleBinding,ClusterRoleBingding
    - 云数据型资源
        - HPA, PodTemplate,LimitRange
