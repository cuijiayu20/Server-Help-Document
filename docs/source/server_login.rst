登录捷智算服务器
================

本章节介绍如何连接和登录您租用的服务器实例。
登录捷智算服务器
================

本章节介绍如何连接和登录您租用的服务器实例。

SSH 登录
--------

SSH 是最常用的远程连接方式。

获取登录信息
~~~~~~~~~~~~

1. 在控制台的 **"我的实例"** 列表中，找到您需要连接的实例。
2. 点击 **"查看访问信息"** 按钮，获取 SSH 连接信息。

连接服务器
~~~~~~~~~~

**Windows 用户**

使用 SSH 客户端（如 PuTTY, MobaXterm）：

1. 打开 SSH 客户端
2. 输入服务器地址和端口
3. 选择 SSH 连接类型
4. 点击连接，输入用户名和密码

**Linux/Mac 用户**

在终端中使用以下命令：

.. code-block:: bash

   ssh username@server_address -p port

示例：

.. code-block:: bash

   ssh root@120.232.255.237 -p 30371

参数说明：

* **username**: 通常为 ``root``
* **server_address**: 服务器的公网 IP 地址
* **port**: 映射的 SSH 端口号

首次连接时会提示确认指纹，输入 ``yes`` 继续，然后输入密码即可登录。

使用 SCP 传输文件
-----------------

SCP（Secure Copy）用于在本地和服务器之间安全传输文件。

上传文件到服务器
~~~~~~~~~~~~~~~~

.. code-block:: bash

   scp -P port local_file username@server_address:remote_path

示例：

.. code-block:: bash

   # 上传单个文件
   scp -P 30371 data.txt root@120.232.255.237:/root/
   
   # 上传整个文件夹
   scp -P 30371 -r local_folder root@120.232.255.237:/root/

从服务器下载文件
~~~~~~~~~~~~~~~~

.. code-block:: bash

   scp -P port username@server_address:remote_file local_path

示例：

.. code-block:: bash

   # 下载单个文件
   scp -P 30371 root@120.232.255.237:/root/result.txt ./
   
   # 下载整个文件夹
   scp -P 30371 -r root@120.232.255.237:/root/data ./

.. note::

   * ``-P`` 参数指定端口（注意是大写 P）
   * ``-r`` 参数用于递归复制整个目录

Web 登录
--------

部分镜像或服务（如 JupyterLab）支持通过浏览器直接访问。

Jupyter 访问
~~~~~~~~~~~~

1. 在控制台实例详情中，查找 **"Jupyter"** 链接
2. 点击链接在浏览器中打开
3. 输入 Token 或密码进行登录

VNC 远程桌面
~~~~~~~~~~~~

1. 在访问信息中获取 VNC 地址和端口
2. 使用 VNC 客户端连接
3. 输入 VNC 密码登录

自定义 Web 服务
~~~~~~~~~~~~~~~

如果您的服务运行在特定端口（如 8888），使用以下格式访问：

.. code-block:: text

   http://server_address:port

.. tip::

   确保服务器防火墙已开放相应端口，否则可能无法访问。
