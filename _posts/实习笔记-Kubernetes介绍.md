---
title: 实习笔记-Kubernetes介绍
date: 2020-11-18 17:44
description: 商汤-工程院-容器平台实习随笔
categories: 
- 云计算
---
# 实习笔记-Kubernetes介绍

## 什么是Kubernetes？
Kubernetes是一种容器编排系统，类似的有Docker Swarm。通过容器编排系统，能很好的对软件进行资源隔离以及保证服务的高可用。

## Node 和 Pod
节点是k8s中的物理机或者虚拟机。节点提供容器的Runtime，通常是docker，也有可能是containerd或者其他。可以通过 `Kubectl get node -owide`查看集群中的节点情况。

![pod](https://feisky.gitbooks.io/kubernetes/content/introduction/media/pod.png)

Pod是k8s中的最小单元，Pod在被创建后会被scheduler调度到特定的节点上，即在特定节点上创建容器。Pod内部包含一个或者多个容器，Pod本质上是对容器的抽象。k8s一般采用YAML来描述资源。下面是一个nginx的Pod资源定义，其中metadata中包含了该Pod的名字和标签，在Spec中，指定了该Pod只含有一个nginx容器，容器所开放的端口为80. 在定义好资源后，可以使用命令 `kubectl apply -f pod.yaml`创建该资源。Pod被调度后，会被分配到一个InternalIp，就是k8s集群中的IP，在集群中，可以通过这个IP来定位特定的Pod。但是，Pod的生命是短暂的，当Pod挂掉后，k8s自动会拉起新的Pod，但是新的Pod的IP会发生变化，这个在Service中会提到。此外，Pod是无状态的，如果Pod中运行了数据库，Pod重启之后数据会丢失，这显然是不可取的。因此，Pod还可以使用Volume来运行有状态服务，将数据保存起来，后续会在Volume中介绍。

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```


## Service 和 Ingress
Service是一层抽象，每个Pod都有自己的标签，Service通过标签，可以将提供相同功能的Pod划分在一起，抽象为Service，例如，Redis有两个Replica，则这两个Replica可以被抽象为一个Redis的Service。
Service的出现解决了Pod重启之后IP改变的问题。假设一个Pod中运行了Web后端，一个Pod中运行了数据库，Web后端需要访问数据库，而数据库可能会重启，但是因为Service的存在，提供了不变的Service IP，后端访问数据库时使用ServiceIP就不会感知到数据库重启IP改变无法访问的问题。以上面nginx为例，创建nginx的Service。Service的spec中指定了访问该服务的端口80，以及target port，即属于这个Service的Pod上提供该服务的端口，两者可以不相同。

```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx
  name: nginx
  namespace: default
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
  sessionAffinity: None
  type: ClusterIP
```

可以看到，在该Service的type指定为ClusterIP，因此是不能被集群外部访问的。需要被集群外部访问，存在两种方式，一种是采用NodeIP，即将Service的IP映射到节点上。这样一来，外部访问节点的特定端口就能访问到集群内部的Service。但是，这种方式不美观，没有提供URL形式的网址，这个时候，Ingress就出现了，Ingress是提供集群外部访问集群内服务的手段。如图创建了一个Ingress资源，通过foo.bar.com就可以访问到集群内s1服务的80端口，该s1再将流量转发到其Backends的Pod的特定端口上。
```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test
spec:
  rules:
  - host: foo.bar.com
    http:
      paths:
      - backend:
          serviceName: s1
          servicePort: 80
  - host: bar.foo.com
    http:
      paths:
      - backend:
          serviceName: s2
          servicePort: 80
```

# Volume
k8s提供了Volume资源，从而可以很好的运行有状态服务。保证了数据的持久化存储。k8s支持非常多的Volume类型，其中既包括持久化的，也包括非持久化的。这里介绍一下Hostpath。HostPath 允许挂载 Node 上的文件系统到 Pod 里面去。因此，Pod可以直接访问节点上的文件数据，例如，Pod可以将宿主机上的docker.sock挂载，这样，Pod中的程序可以直接与宿主机上的Docker daemon进行通信。此外，还有两种比较常见的是Secret和ConfigMap，这两个也是k8s的资源，其中Secret中可以存储密码等信息，采用base64加密，Configmap中是明文的配置，可以是数据库的配置文件等，这样数据库的Pod重启的时候会读取挂载的Configmap。还有最为常用的PersistentVolumeClaim和CSI，PVC是一个声明，表示告诉k8s集群，需要一个PV资源，即PersistentVolume，拿Ceph rbd为例，运维人员在集群中创建好一定量的PV，PV与Ceph集群中的块存储对应，当创建Pod中Volume字段包含PVC，k8s就会从PV中找到符合要求的PV，绑定上PVC，这样Pod相当于就使用了Ceph的存储。PV的出现对存储进行了抽象，用户不用关心底层的存储工具是什么。但是PV需要运维人员手动创建也是一件繁琐的事情，所以CSI应运而生。CSI可以使得创建好PVC之后，PVC会通过StorageClass自动去存储商（例如Ceph）中索取PV并绑定。

CSI是容器存储接口，CSI的出现使得各个存储运营商开始根据这个接口写出特定的StorageClass。使得k8s在更新的过程中与存储商解藕。此外，k8s-csi小组提供了一系列方便CSI开发的Container。后续会以Ceph为例针对CSI进行介绍。

# Deployment 
由于Pod的生命周期是短暂的，但Pod被Kill掉之后，新的Pod不会被创建。这不是我们想要的状态，Deployment的出现可以理解为给定一个期望达到的状态，例如Pod的数量，Pod所挂载的目录等，交由Controller去实时Watch这些资源，一旦发生了改变，则会作出相应的动作使得恢复到原有的状态。下面是一个Deployment的声明。
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
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
```
这个Deployment申明了需要3个nginx的Pod，template.spec中的内容与前面Pod的spec相同。就是Pod的相关配置。当在集群中配置了该Deployment之后，Deployment首先会根据Replicas的数量创建一个Replicaset，然后由Replicaset去创建Pod。StatefulSet 是为了解决有状态服务的问题（对应 Deployments 和 ReplicaSets 是为无状态服务而设计）。Statefulset可以提供

- 稳定的持久化存储，即 Pod 重新调度后还是能访问到相同的持久化数据，基于 PVC 来实现
- 稳定的网络标志，即 Pod 重新调度后其 PodName 和 HostName 不变，基于 Headless Service（即没有 Cluster IP 的 Service）来实现
- 有序部署，有序扩展，即 Pod 是有顺序的，在部署或者扩展的时候要依据定义的顺序依次依序进行（即从 0 到 N-1，在下一个 Pod 运行之前所有之前的 Pod 必须都是 Running 和 Ready 状态），基于 init containers 来实现
- 有序收缩，有序删除（即从 N-1 到 0）

在数据库服务中，一般都采用Statefulset的形式去部署。
