systemd service
####################

.. contents::

需求
``````````
运行环境为CentOS 7系统，我们开发了一个程序，需要在开机时启动它，当程序进程crash之后，守护进程立即拉起进程。

解决方案
```````````
使用CentOS 7中的init进程systemd


systemd简介
``````````````````

参考资料:https://blog.csdn.net/shuaixingi/article/details/49641721

Linux Init & CentOS systemd

Linux一直以来采用init进程。例如下面的命令用来启动服务：

$ sudo /etc/init.d/apache2 start

或者\ $ service apache2 start

但是init有两个缺点：

* 1、启动时间长。Init进程是串行启动，只有前一个进程启动完，才会启动下一个进程。（这也是CentOS5的主要特征)
* 2、启动脚本复杂。Init进程只是执行启动脚本，不管其他事情。脚本需要自己处理各种情况，这使得脚本变得很长而且复杂。


Init：

* Centos 5 Sys init 是启动速度最慢的，串行启动过程，无论进程相互之间有无依赖关系。
* Centos6 Upstart init 相对启动速度快一点有所改进。有依赖的进程之间依次启动而其他与之没有依赖关系的则并行同步启动。
* Centos7 systemd 与以上都不同。所有进程无论有无依赖关系则都是并行启动（当然很多时候进程没有真正启动而是只有一个信号或者说是标记而已，在真正利用的时候才会真正启动。）

systemd为了解决上文的问题而诞生。它的目标是，为系统的启动和管理提供一套完整的解决方案。根据linux惯例，字母d是守护进程（daemon） 的缩写。Systemd名字的含义就是 守护整个系统。Centos 7里systemd代替了init，成为了系统的第一个进程。PID为1.其他所有的进程都是它的子进程。

systemd 是 Linux 下的一款系统和服务管理器，兼容 SysV 和 LSB 的启动脚本。systemd 的特性有：支持并行化任务；同时采用 socket 式与 D-Bus 总线式激活服务；按需启动守护进程（daemon）；利用 Linux 的 cgroups 监视进程；支持快照和系统恢复；维护挂载点和自动挂载点；各服务间基于依赖关系进行精密控制。


systemd文件示例：
`````````````````````
ExecStart后面的，就是启动该服务器时要执行的命令，可以说是单个脚本，也可以是一个命令加参数。

.. code-block:: bash

    echo '
    [Unit]
    Description=The Sophiroth Service
    After=syslog.target network.target salt-master.service

    [Service]
    Type=simple
    User=alvin
    WorkingDirectory=/opt/sophiroth-pxe
    ExecStart=/usr/bin/python2 -m CGIHTTPServer 8001
    KillMode=process
    Restart=on-failure
    RestartSec=3s

    [Install]
    WantedBy=multi-user.target graphic.target
    ' > /usr/lib/systemd/system/sophiroth-pxe.service

启动 sophroth-pxe服务
```````````````````````````

.. code-block:: bash

    systemctl enable sophiroth-pxe
    systemctl start sophiroth-pxe
    systemctl status sophiroth-pxe
    lsof -i:8001
