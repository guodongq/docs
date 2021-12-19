.. SPDX-License-Identifier: MIT

=====================================
编写Kubernetes CRD扩展Kubernetes API
=====================================

声明式编程
~~~~~~~~~~

- 命令式编程：详细的命令机器怎么(How)去处理一件事情以达到你想要的结果(What)  
- 声明式编程：只告诉你想要的结果(What)，机器自己摸索过程(How)  

在Kubernetes中一般会编写对应API对象的YAML文件交给Kubernetes(而不是使用一些命令来直接操作API)，即只需要提交一个定义好的API对象来"声明"（这个YAML文件其实就是一种"声明"),表示所期望的最终状态是什么样子就可以了

用户只需要关心应用程序的最终状态，其他的都是通过K8s来帮助我们完成，通过这种声明式的方式可以大大简化应用的配置管理复杂度

控制器模式
~~~~~~~~~~

GVKs&&GVRs
~~~~~~~~~~

开发脚手架
~~~~~~~~~~
* code-generator
* kubebuilder
* operator-sdk

Getting Started
~~~~~~~~~~~~~~~~~

Local Debug
~~~~~~~~~~~~~~~~
