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


Kubectl
---------------

ApiServer
---------------

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


