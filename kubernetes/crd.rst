.. SPDX-License-Identifier: MIT

=====================================
编写Kubernetes CRD扩展Kubernetes API
=====================================

声明式编程
------------------

- 命令式编程：详细的命令机器怎么(How)去处理一件事情以达到你想要的结果(What)  
- 声明式编程：只告诉你想要的结果(What)，机器自己摸索过程(How)  

在Kubernetes中一般会编写对应API对象的YAML文件交给Kubernetes(而不是使用一些命令来直接操作API)，即只需要提交一个定义好的API对象来"声明"（这个YAML文件其实就是一种"声明"),表示所期望的最终状态是什么样子就可以了

用户只需要关心应用程序的最终状态，其他的都是通过K8s来帮助我们完成，通过这种声明式的方式可以大大简化应用的配置管理复杂度

GVK&&GVR
------------------

在Kubernetes中想要完成一个CRD，需要指定 ``group/version`` 和 ``kind``, 在kuberntes的API Server中简称为GVK，GVK是定位一种类型的方式，例如daemonsets就是Kubernetes的一种资源，当我们想要通过Kubernetes创建一个daemonsets的时候，需要定义daemonsets的描述文件

.. code-block:: yaml
   :linenos:

   apiVersion: apps/v1 
   kind: DaemonSet

这里声明了apiVersion是 ``apps/v1``,其实隐含了Group是apps，Version是v1，Kind是定义的DaemonSet，而kubectl接收到这个声明以后，可以根据这个声明去调用API Server对应的URL去获取信息，本例中就是 ``/apis/apps/v1/daemonset``

Kubernetes组织资源的方式是以REST的URI形式的，而组织的路径就是：

.. figure:: /_static/images/kubernetes/gvr.jpg
   :width: 100%
   :align: center
   :alt: Kubernetes gvr

   Kubernetes GVR

在API Server中还有第二个概念GVR, 当我们理解了GVK之后，再来理解GVR就容易了，和面向对象编程中的类和对象的概念是一样的

+---------------+---------+
|  Kubernetes   | OOP     |
+===============+=========+
|  Kind         | Class   |
+---------------+---------+
|  Resource     | Object  |
+---------------+---------+

Kind其实就是一个类，用于描述对象的，而Resource就是具体的Kind，可以理解成类已经实例化成对象了

控制器模式
------------------

kubernetes作为一个“容器编排”平台，其核心的功能是编排，Pod作为K8s调度的最小单位，具备很多属性和字段，k8s编排正是通过一个个控制器根据被控制对象的属性和字段来实现。

K8s集群在部署时包含了Controllers组件，里面对于每个build-in的资源类型（比如Deployment、Statefulset、Cronjob…）都有对应的Controller，基本是1:1的关系

.. code-block:: yaml
    :linenos:

    apiVersion: apps/v1 
    kind: Deployment 
    metadata: 
      name: test 
    spec: 
      selector:
        matchLabels:
          app: test
      replicas: 2
      template:
        metadata:
          labels:
            app: test
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9
            ports:
            - containerPort: 80

如上，Deployment资源创建以后，对应的Deployment Controller确保当前的deployment资源的Pod个数永远为2，Pod由Template部分组成,具体来说K8s里面的kube-controller-manager这个组件在做这件事情，在 ``kubernetes/pkg/controller`` 目录下包含了所有的控制器，它们都以独有的方式负责某种编排功能，但是它们都遵循一个通用的编排模式，即：调谐循环(Reconcile loop),伪代码逻辑如下:

.. code-block:: golang
   :linenos:

   for {
     actualState := GetResourceActualState(rsvc) 
     expectState := GetResourceExpectState(rsvc)
     if actualState == expectState {
        // do nothing
     } else {
        Reconcile(rsvc)
     }
   }

- 就是一个无限循环不断地对比期望状态和实际状态，如果有出入则进行Reconcile(调谐)逻辑将实际状态调整为期望状态  
- 期望状态就是我们对象的定义(通常是YAML文件),实际状态是k8s集群里面当前的运行状态  
- 控制器的编排逻辑主要是第三步做的，这个操作称为调谐(Reconcile),整个控制器的调谐过程称为"Reconcile Loop"


开发脚手架
------------------

* `code-generator <https://github.com/kubernetes/code-generator>`_
* `kubebuilder <https://github.com/kubernetes-sigs/kubebuilder>`_
* `operator-sdk <https://github.com/operator-framework/operator-sdk>`_

`code-generator` 实际上并不能称之为crd开发的脚手架，它只是能生成一些代码，如果用code-generator进行crd的开发需要我们编写很多逻辑控制代码

`kubebuilder` 和 `operator-sdk` 都是为了方便创建和管理operator而生的脚手架项目, `operator-sdk` 在底层使用了 `kubebuilder`,两者在创建operator项目时都是调用 `controller-runtime` 接口，具有相同的项目布局。

除此之外 `operator-sdk` 还增加了一些特性，具体可以 `参考 <https://sdk.operatorframework.io/docs/faqs/>`_

* 两者不是竞争关系, `operator-sdk` 相当于是 `kubebuilder+`  
* `operator-sdk` 的文档质量高，感觉 `operator-sdk` 更像是商业版本的kubebuilder,实际上它们都是开源的 

Operator SDK Getting Started
------------------

创建脚手架工程
~~~~~~~~~~~~~~~~
 
创建API
~~~~~~~~~~~~~~~~

定义CRD
~~~~~~~~~~~~~~~~

编写Controller逻辑
~~~~~~~~~~~~~~~~~~~~

测试发布
~~~~~~~~~~~~~~~~~~~~

总结
------------------

Local Debug
------------------
