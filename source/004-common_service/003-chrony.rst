chrony
##########
使用ntp时间同步服务

本次我们指定的ntp服务器服务端地址是ntp.alv.pub

安装chrony
================

.. code-block:: bash

    yum install chrony -y


配置ntp服务器地址
===================

删除server开头的其他行，替换为我们的server ntp.alv.pub iburst,  其他行中，makestep 1.0 3 这一行最不可少， 最少该配置文件中可以只包含server开头和makestep开头的这两行。

.. code-block:: bash

    # vim /etc/chrony.conf
    server ntp.alv.pub iburst


启动chrony服务
========================

.. code-block:: bash

    systemctl start chronyd
    systemctl enable chronyd