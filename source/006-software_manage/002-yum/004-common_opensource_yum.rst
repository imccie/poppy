常用的开源软件网络yum源
#########################


nginx的官方yum源
=========================

.. literalinclude:: ../../../code/yum.repos.d/nginx.repo
    :lines: 1-


kubernetes在阿里的yum源
==============================

.. code-block:: bash

    $ wget -P /etc/yum.repos.d/ https://raw.githubusercontent.com/AlvinWanCN/poppy/master/code/yum.repos.d/kubernetes.repo

.. literalinclude:: ../../../code/yum.repos.d/kubernetes.repo
    :lines: 1-


zabbix3.4的zabbix官方yum源
=====================================

.. literalinclude:: ../../../code/yum.repos.d/zabbix3.4.repo
    :lines: 1-

mariadb10.3 stable版官方源
================================
网络地址: https://downloads.mariadb.org/mariadb/repositories/#mirror=neusoft&distro=CentOS&distro_release=centos7-amd64--centos7&version=10.3

.. literalinclude:: ../../../code/yum.repos.d/mariadb10.3.repo
    :lines: 1-

docker-ce 的阿里云yum源
====================================
从网络下载：

.. code-block:: bash

    $ wget -P /etc/yum.repos.d/ https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

.. literalinclude:: ../../../code/yum.repos.d/docker-ce.repo
    :lines: 1-