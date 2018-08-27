Alvin的常用脚本
#####################





解决ssh缓慢问题
======================

.. code-block:: bash

    bash -c "$(curl -fsSL https://raw.githubusercontent.com/AlvinWanCN/poppy/master/code/common_tools/sshslowly.sh)"


关闭firewalld和selinux
============================

.. code-block:: bash

    bash -c "$(curl -fsSL https://raw.githubusercontent.com/AlvinWanCN/poppy/master/code/common_tools/disableSeAndFir.sh)"


添加本地仓库
===================


.. code-block:: bash

    python -c "$(curl -fsSL https://raw.githubusercontent.com/AlvinWanCN/poppy/master/code/common_tools/pullLocalYum.py)"



加入natasha的ldap系统
==============================

描述：加入到natasha.alv.pub ldap系统，并配置autofs将dc.alv.pub的用户数据目录挂载过来，dc.alv.pub是alvin的虚拟机，仅alvin自己可以访问。

.. code-block:: bash

    python -c "$(curl -fsSL https://raw.githubusercontent.com/AlvinWanCN/poppy/master/code/common_tools/joinNatashaLDAP.py)"
