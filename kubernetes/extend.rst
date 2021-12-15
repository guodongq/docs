.. SPDX-License-Identifier: MIT

====================
Kubernetes扩展
====================

目前Kubernetes已经成为容器编排的事实标准，其本身的功能也是非常丰富并且灵活，但是也不能满足所有人的需求。在遇到Kubernetes提供的能力无法满足我们需求的时候，我们可以利用Kubernetes高度可配置且可扩展的能力进行定制。

Kubernetes的定制化方法主要分为配置和扩展两种，这里主要讲述Kubernetes扩展，实际上从客户端到底层容器运行时，绝大部分地方Kubernetes都为我们预留了扩展点

.. figure:: /_static/images/kubernetes/kubernetes-extend.jpg
   :width: 100%
   :align: center
   :alt: Kubernetes extend

   Kubernetes Extend


Kubectl
---------------

用户通常使用kubectl与Kubernetes API交互。 `Kubectl插件 <https://kubernetes.io/zh/docs/tasks/extend-kubectl/kubectl-plugins/>`_ 能够扩展Kubectl的行为，不过这些插件只会影响到每个用户的本地环境


Kubectl将会在用户的PATH路径下查找 ``kubectl-*`` 为前缀的二进制文件，并且把它当成一个插件.

例如tekton的cli命令行工具，可以作为一个kubectl插件 `kubectl-tkn <https://github.com/tektoncd/cli#tkn-as-a-kubectl-plugin>`_ 方式运行

.. figure:: /_static/images/kubernetes/kubectl-plugin.jpg
   :width: 100%
   :align: center
   :alt: Kubernetes plugin

   Kubectl Plugins


有一些写好的kubectl plugins可供我们使用，更多信息请访问 `这里 <https://krew.sigs.k8s.io/plugins/>`_


ApiServer
---------------

APIServer处理所有的请求，当请求到达APIServer时，需要经过认证->鉴权->准入控制的步骤，在这些步骤中都存在扩展点

.. figure:: /_static/images/kubernetes/kube-apiserver.jpg
   :width: 100%
   :align: center
   :alt: Kubernetes plugin

   Kube-ApiServer

准入控制
~~~~~~~~~~~~

用的最多的是准入控制的扩展，准入控制会先经过变更准入控制MutatingAdmissionWebhook，然后再经过验证准入控制ValidatingAdmissionWebhook,任何一个准入控制器返回了错误这个请求都会失败，在这两个准入控制器中可以做很多事情，例如注入sidecar，验证资源，调整pod的配额等等


Reference:

* `Kubernetes API访问扩展 <https://kubernetes.io/zh/docs/concepts/security/controlling-access/>`_

* `深入理解k8s中的访问控制 <https://www.cnblogs.com/yangyuliufeng/p/13548915.html>`_

Aggregation Layer
~~~~~~~~~~~~~~~~~~~~~~~~

从K8s 1.7版本之后，APIServer引入了聚合层的功能，API Aggregation允许在不修改Kubernetes核心代码的同时扩展Kubernetes API，即将第三方服务注册到Kubernetes API中，这样就可以通过Kubernetes API来访问外部服务



资源
---------------

控制器
---------------

调度器
---------------

网络插件CNI
---------------

存储插件CSI
---------------

容器运行时CRI
---------------


