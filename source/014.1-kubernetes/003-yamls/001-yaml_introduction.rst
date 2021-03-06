yaml文件编写
#####################

创建资源的方法：
    apiserver仅接收JSON格式的资源定义；
    yaml格式提供配置清单，apiserver可自动将起转为json格式，而后再提交；

大部分资源的配置清单:
    apiVersion: /group/version
        .. code-block:: bash

            $ kubectl api-versions

    kind: 资源类型

    metadata: 元数据
        name （名字）
        namespace （命名空间）
        labels  （标签）
        annotations （资源注解）

        每个资源的引用PATH
            /api/GROUP/VERSION/namespaces/NAMESPACE/TYPE/NAME/ (这里大写的内容替换为大写的名称。)

    spec: 期望的状态，disired sate

    status： 当前状态，current state，本字段由kubernetes集群维护，用户不能定义它   （让status的状态不断的接近spec状态）

- 查看一个对象有哪些字段
    这里我们查看pod有哪些字段

    ::

        [alvin@k8s1 ~]$ kubectl explain pods
        KIND:     Pod
        VERSION:  v1

        DESCRIPTION:
             Pod is a collection of containers that can run on a host. This resource is
             created by clients and scheduled onto hosts.

        FIELDS:
           apiVersion	<string>
             APIVersion defines the versioned schema of this representation of an
             object. Servers should convert recognized schemas to the latest internal
             value, and may reject unrecognized values. More info:
             https://git.k8s.io/community/contributors/devel/api-conventions.md#resources

           kind	<string>
             Kind is a string value representing the REST resource this object
             represents. Servers may infer this from the endpoint the client submits
             requests to. Cannot be updated. In CamelCase. More info:
             https://git.k8s.io/community/contributors/devel/api-conventions.md#types-kinds

           metadata	<Object>
             Standard object's metadata. More info:
             https://git.k8s.io/community/contributors/devel/api-conventions.md#metadata

           spec	<Object>
             Specification of the desired behavior of the pod. More info:
             https://git.k8s.io/community/contributors/devel/api-conventions.md#spec-and-status

           status	<Object>
             Most recently observed status of the pod. This data may not be up to date.
             Populated by the system. Read-only. More info:
             https://git.k8s.io/community/contributors/devel/api-conventions.md#spec-and-status


    那么如果我们想知道pod里的metadata又有哪些字段呢？那么我们可以进行如下操作

    ::

        [alvin@k8s1 ~]$ kubectl explain pods.metadata
        KIND:     Pod
        VERSION:  v1

        RESOURCE: metadata <Object>

        DESCRIPTION:
             Standard object's metadata. More info:
             https://git.k8s.io/community/contributors/devel/api-conventions.md#metadata

             ObjectMeta is metadata that all persisted resources must have, which
             includes all objects users must create.

        FIELDS:
           annotations	<map[string]string>
             Annotations is an unstructured key value map stored with a resource that
             may be set by external tools to store and retrieve arbitrary metadata. They
             are not queryable and should be preserved when modifying objects. More
             info: http://kubernetes.io/docs/user-guide/annotations

           clusterName	<string>
             The name of the cluster which the object belongs to. This is used to
             distinguish resources with same name and namespace in different clusters.
             This field is not set anywhere right now and apiserver is going to ignore
             it if set in create or update request.

           creationTimestamp	<string>
             CreationTimestamp is a timestamp representing the server time when this
             object was created. It is not guaranteed to be set in happens-before order
             across separate operations. Clients may not set this value. It is
             represented in RFC3339 form and is in UTC. Populated by the system.
             Read-only. Null for lists. More info:
             https://git.k8s.io/community/contributors/devel/api-conventions.md#metadata

           deletionGracePeriodSeconds	<integer>
             Number of seconds allowed for this object to gracefully terminate before it
             will be removed from the system. Only set when deletionTimestamp is also
             set. May only be shortened. Read-only.

           deletionTimestamp	<string>
             DeletionTimestamp is RFC 3339 date and time at which this resource will be
             deleted. This field is set by the server when a graceful deletion is
             requested by the user, and is not directly settable by a client. The
             resource is expected to be deleted (no longer visible from resource lists,
             and not reachable by name) after the time in this field, once the
             finalizers list is empty. As long as the finalizers list contains items,
             deletion is blocked. Once the deletionTimestamp is set, this value may not
             be unset or be set further into the future, although it may be shortened or
             the resource may be deleted prior to this time. For example, a user may
             request that a pod is deleted in 30 seconds. The Kubelet will react by
             sending a graceful termination signal to the containers in the pod. After
             that 30 seconds, the Kubelet will send a hard termination signal (SIGKILL)
             to the container and after cleanup, remove the pod from the API. In the
             presence of network partitions, this object may still exist after this
             timestamp, until an administrator or automated process can determine the
             resource is fully terminated. If not set, graceful deletion of the object
             has not been requested. Populated by the system when a graceful deletion is
             requested. Read-only. More info:
             https://git.k8s.io/community/contributors/devel/api-conventions.md#metadata

           finalizers	<[]string>
             Must be empty before the object is deleted from the registry. Each entry is
             an identifier for the responsible component that will remove the entry from
             the list. If the deletionTimestamp of the object is non-nil, entries in
             this list can only be removed.

           generateName	<string>
             GenerateName is an optional prefix, used by the server, to generate a
             unique name ONLY IF the Name field has not been provided. If this field is
             used, the name returned to the client will be different than the name
             passed. This value will also be combined with a unique suffix. The provided
             value has the same validation rules as the Name field, and may be truncated
             by the length of the suffix required to make the value unique on the
             server. If this field is specified and the generated name exists, the
             server will NOT return a 409 - instead, it will either return 201 Created
             or 500 with Reason ServerTimeout indicating a unique name could not be
             found in the time allotted, and the client should retry (optionally after
             the time indicated in the Retry-After header). Applied only if Name is not
             specified. More info:
             https://git.k8s.io/community/contributors/devel/api-conventions.md#idempotency

           generation	<integer>
             A sequence number representing a specific generation of the desired state.
             Populated by the system. Read-only.

           initializers	<Object>
             An initializer is a controller which enforces some system invariant at
             object creation time. This field is a list of initializers that have not
             yet acted on this object. If nil or empty, this object has been completely
             initialized. Otherwise, the object is considered uninitialized and is
             hidden (in list/watch and get calls) from clients that haven't explicitly
             asked to observe uninitialized objects. When an object is created, the
             system will populate this list with the current set of initializers. Only
             privileged users may set or modify this list. Once it is empty, it may not
             be modified further by any user.

           labels	<map[string]string>
             Map of string keys and values that can be used to organize and categorize
             (scope and select) objects. May match selectors of replication controllers
             and services. More info: http://kubernetes.io/docs/user-guide/labels

           name	<string>
             Name must be unique within a namespace. Is required when creating
             resources, although some resources may allow a client to request the
             generation of an appropriate name automatically. Name is primarily intended
             for creation idempotence and configuration definition. Cannot be updated.
             More info: http://kubernetes.io/docs/user-guide/identifiers#names

           namespace	<string>
             Namespace defines the space within each name must be unique. An empty
             namespace is equivalent to the "default" namespace, but "default" is the
             canonical representation. Not all objects are required to be scoped to a
             namespace - the value of this field for those objects will be empty. Must
             be a DNS_LABEL. Cannot be updated. More info:
             http://kubernetes.io/docs/user-guide/namespaces

           ownerReferences	<[]Object>
             List of objects depended by this object. If ALL objects in the list have
             been deleted, this object will be garbage collected. If this object is
             managed by a controller, then an entry in this list will point to this
             controller, with the controller field set to true. There cannot be more
             than one managing controller.

           resourceVersion	<string>
             An opaque value that represents the internal version of this object that
             can be used by clients to determine when objects have changed. May be used
             for optimistic concurrency, change detection, and the watch operation on a
             resource or set of resources. Clients must treat these values as opaque and
             passed unmodified back to the server. They may only be valid for a
             particular resource or set of resources. Populated by the system.
             Read-only. Value must be treated as opaque by clients and . More info:
             https://git.k8s.io/community/contributors/devel/api-conventions.md#concurrency-control-and-consistency

           selfLink	<string>
             SelfLink is a URL representing this object. Populated by the system.
             Read-only.

           uid	<string>
             UID is the unique in time and space value for this object. It is typically
             generated by the server on successful creation of a resource and is not
             allowed to change on PUT operations. Populated by the system. Read-only.
             More info: http://kubernetes.io/docs/user-guide/identifiers#uids

        [alvin@k8s1 ~]$

    那如果是更深一点的字段呢？ 比如pods.spec.containers.livenessProbe

    ::

        [alvin@k8s1 ~]$ kubectl explain pods.spec.containers.livenessProbe
        KIND:     Pod
        VERSION:  v1

        RESOURCE: livenessProbe <Object>

        DESCRIPTION:
             Periodic probe of container liveness. Container will be restarted if the
             probe fails. Cannot be updated. More info:
             https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle#container-probes

             Probe describes a health check to be performed against a container to
             determine whether it is alive or ready to receive traffic.

        FIELDS:
           exec	<Object>
             One and only one of the following should be specified. Exec specifies the
             action to take.

           failureThreshold	<integer>
             Minimum consecutive failures for the probe to be considered failed after
             having succeeded. Defaults to 3. Minimum value is 1.

           httpGet	<Object>
             HTTPGet specifies the http request to perform.

           initialDelaySeconds	<integer>
             Number of seconds after the container has started before liveness probes
             are initiated. More info:
             https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle#container-probes

           periodSeconds	<integer>
             How often (in seconds) to perform the probe. Default to 10 seconds. Minimum
             value is 1.

           successThreshold	<integer>
             Minimum consecutive successes for the probe to be considered successful
             after having failed. Defaults to 1. Must be 1 for liveness. Minimum value
             is 1.

           tcpSocket	<Object>
             TCPSocket specifies an action involving a TCP port. TCP hooks not yet
             supported

           timeoutSeconds	<integer>
             Number of seconds after which the probe times out. Defaults to 1 second.
             Minimum value is 1. More info:
             https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle#container-probes



YAML 基础
=================
YAML是专门用来写配置文件的语言，非常简洁和强大，使用比json更方便。它实质上是一种通用的数据串行化格式。后文会说明定义YAML文件创建Pod和创建Deployment。

YAML语法规则：
    - 大小写敏感
    - 使用缩进表示层级关系
    - 缩进时不允许使用Tal键，只允许使用空格
    - 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可
    - ”#” 表示注释，从这个字符一直到行尾，都会被解析器忽略

在Kubernetes中，只需要知道两种结构类型即可：
    - Lists
    - Maps

使用YAML用于K8s的定义带来的好处包括：
    - 便捷性：不必添加大量的参数到命令行中执行命令
    - 可维护性：YAML文件可以通过源头控制，跟踪每次操作
    - 灵活性：YAML可以创建比命令行更加复杂的结构

YAML Maps
===============
Map顾名思义指的是字典，即一个Key:Value 的键值对信息。例如：

::

    ---
    apiVersion: v1
    kind: Pod

第一行的---是分隔符，是可选的，在单一文件中，可用连续三个连字号---区分多个文件。上述内容表示有两个键apiVersion和kind，分别对应的值为v1和Pod。

Maps的value既能够对应字符串也能够对应一个Maps。例如：

::

    ---
    apiVersion: v1
    kind: Pod
    metadata:
      name: kube100-site
      labels:
        app: web

注：上述的YAML文件中，metadata这个KEY对应的值为一个Maps，而嵌套的labels这个KEY的值又是一个Map。实际使用中可视情况进行多层嵌套。

​YAML处理器根据行缩进来知道内容之间的关联。上述例子中，使用 **两个空格作为缩进，但空格的数据量并不重要，只是至少要求一个空格并且所有缩进保持一致的空格数** 。例如，name和labels是相同缩进级别，因此YAML处理器知道他们属于同一map；它知道app是lables的值因为app的缩进更大。

**注意：在YAML文件中绝对不要使用tab键**


YAML Lists
===============

List即列表，说白了就是数组，例如：

::

    args
     -beijing
     -shanghai
     -shenzhen
     -guangzhou

可以指定任何数量的项在列表中，每个项的定义以破折号（-）开头，并且与父元素之间存在缩进。在JSON格式中，表示如下：

::

    {
      "args": ["beijing", "shanghai", "shenzhen", "guangzhou"]
    }

当然Lists的子项也可以是Maps，Maps的子项也可以是List，例如：

::

    ---
    apiVersion: v1
    kind: Pod
    metadata:
      name: kube100-site
      labels:
        app: web
    spec:
      containers:
        - name: front-end
          image: nginx
          ports:
            - containerPort: 80
        - name: flaskapp-demo
          image: jcdemo/flaskapp
          ports: 8080

如上述文件所示，定义一个containers的List对象，每个子项都由name、image、ports组成，每个ports都有一个KEY为containerPort的Map组成，转成JSON格式文件：

::

    {
      "apiVersion": "v1",
      "kind": "Pod",
      "metadata": {
            "name": "kube100-site",
            "labels": {
                "app": "web"
            },

      },
      "spec": {
            "containers": [{
                "name": "front-end",
                "image": "nginx",
                "ports": [{
                    "containerPort": "80"
                }]
            }, {
                "name": "flaskapp-demo",
                "image": "jcdemo/flaskapp",
                "ports": [{
                    "containerPort": "5000"
                }]
            }]
      }
    }

使用YAML创建Pod
======================

创建Pod
-------------

::

    ---
    apiVersion: v1
    kind: Pod
    metadata:
      name: kube100-site
      labels:
        app: web
    spec:
      containers:
        - name: front-end
          image: nginx
          ports:
            - containerPort: 80
        - name: flaskapp-demo
          image: jcdemo/flaskapp
          ports:
            - containerPort: 5000

上面定义了一个普通的Pod文件，简单分析下文件内容：
    - apiVersion：此处值是v1，这个版本号需要根据安装的Kubernetes版本和资源类型进行变化，记住不是写死的。
    - kind：此处创建的是Pod，根据实际情况，此处资源类型可以是Deployment、Job、Ingress、Service等。
    - metadata：包含Pod的一些meta信息，比如名称、namespace、标签等信息。
    - spec：包括一些 containers，storage，volumes，或者其他Kubernetes需要知道的参数，以及诸如是否在容器失败时重新启动容器的属性。你可以在特定Kubernetes API找到完整的Kubernetes Pod的属性。


下面是一个典型的容器的定义：

    ::

        …
        spec:
          containers:
            - name: front-end
              image: nginx
              ports:
                - containerPort: 80
        …


    - 上述例子只是一个简单的最小定义：一个名字（front-end）、基于nginx的镜像，以及容器将会监听的指定端口号（80）。

    - 除了上述的基本属性外，还能够指定复杂的属性，包括容器启动运行的命令、使用的参数、工作目录以及每次实例化是否拉取新的副本。 还可以指定更深入的信息，例如容器的退出日志的位置。容器可选的设置属性包括：

        - name
        - image
        - command
        - args
        - workingDir
        - ports
        - env
        - resources
        - volumeMounts
        - livenessProbe
        - readinessProbe
        - livecycle
        - terminationMessagePath
        - imagePullPolicy
        - securityContext
        - stdin
        - stdinOnce
        - tty


了解了Pod的定义后，将上面创建Pod的YAML文件保存成pod.yaml，然后使用Kubectl创建Pod：


::

    $ kubectl create -f pod.yaml
    pod "kube100-site" created

可以使用Kubectl命令查看Pod的状态

::

    $ kubectl get pods
    NAME           READY     STATUS    RESTARTS   AGE
    kube100-site   2/2       Running   0          1m

注： Pod创建过程中如果出现错误，可以使用kubectl describe 进行排查。

创建Deployment
======================

上述介绍了如何使用YAML文件创建Pod实例，但是如果这个Pod出现了故障的话，对应的服务也就挂掉了，所以Kubernetes提供了一个Deployment的概念 ，目的是让Kubernetes去管理一组Pod的副本，也就是副本集 ，这样就能够保证一定数量的副本一直可用，不会因为某一个Pod挂掉导致整个服务挂掉。


::

    ---
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: kube100-site
    spec:
      replicas: 2
      template:
        metadata:
          labels:
            app: web
        spec:
          containers:
            - name: front-end
              image: nginx
              ports:
                - containerPort: 80
            - name: flaskapp-demo
              image: jcdemo/flaskapp
              ports:
                - containerPort: 5000


一个完整的Deployment的YAML文件如上所示，接下来解释部分内容：
    - 注意这里apiVersion对应的值是extensions/v1beta1，同时也需要将kind的类型指定为Deployment。
    - metadata指定一些meta信息，包括名字或标签之类的。
    - spec 选项定义需要两个副本，此处可以设置很多属性，例如受此Deployment影响的Pod的选择器等
    - spec 选项的template其实就是对Pod对象的定义
    - 可以在Kubernetes v1beta1 API 参考中找到完整的Deployment可指定的参数列表

将上述的YAML文件保存为deployment.yaml，然后创建Deployment：

::

    $ kubectl create -f deployment.yaml
    deployment "kube100-site" created

可以使用如下命令检查Deployment的列表：

::

    $ kubectl get deployments
    NAME           DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    kube100-site   2         2         2            2           2m


我们可以看到所有的 Pods 都已经正常运行了。

到这里我们就完成了使用 YAML 文件创建 Kubernetes Deployment 的过程，在了解了 YAML 文件的基础后，定义 YAML 文件其实已经很简单了，最主要的是要根据实际情况去定义 YAML 文件，所以查阅 Kubernetes 文档很重要




