常用操作
========

本章节列出服务器上的常用操作命令和技巧。

文件管理
--------

基本命令
~~~~~~~~

* 查看当前目录: ``ls -l``
* 切换目录: ``cd /path/to/directory``
* 创建目录: ``mkdir directory_name``
* 复制文件: ``cp source destination``
* 移动/重命名文件: ``mv source destination``
* 删除文件: ``rm filename``
* 删除目录: ``rm -rf directory_name``

系统监控
--------

资源查看
~~~~~~~~

* 查看进程: ``top`` 或 ``htop``
* 查看 GPU 使用情况: ``nvidia-smi``
* 查看磁盘空间: ``df -h``
* 查看内存使用: ``free -h``
* 查看目录大小: ``du -sh directory_name``

数据下载
--------

使用 wget 下载
~~~~~~~~~~~~~~

``wget`` 是一个强大的下载工具，支持断点续传。

基本用法：

.. code-block:: bash

   # 下载单个文件
   wget https://example.com/dataset.zip
   
   # 断点续传
   wget -c https://example.com/large_file.tar.gz
   
   # 后台下载
   wget -b https://example.com/dataset.zip
   
   # 限速下载（限制为 1MB/s）
   wget --limit-rate=1m https://example.com/file.zip

使用 curl 下载
~~~~~~~~~~~~~~

``curl`` 也是常用的下载工具。

基本用法：

.. code-block:: bash

   # 下载文件
   curl -O https://example.com/dataset.zip
   
   # 下载并重命名
   curl -o mydata.zip https://example.com/dataset.zip
   
   # 断点续传
   curl -C - -O https://example.com/large_file.tar.gz

从 Google Drive 下载
~~~~~~~~~~~~~~~~~~~~

对于 Google Drive 分享的文件，可以使用 ``gdown``：

.. code-block:: bash

   # 安装 gdown
   pip install gdown
   
   # 下载文件
   gdown https://drive.google.com/uc?id=FILE_ID

磁盘挂载
--------

查看磁盘
~~~~~~~~

.. code-block:: bash

   # 查看所有磁盘
   lsblk
   
   # 查看磁盘分区
   fdisk -l

挂载数据盘
~~~~~~~~~~

1. **创建挂载点**

   .. code-block:: bash

      mkdir /data

2. **格式化磁盘**（仅首次使用需要）

   .. code-block:: bash

      # 格式化为 ext4 文件系统
      mkfs.ext4 /dev/vdb

   .. warning::

      格式化会清空磁盘所有数据，请谨慎操作！

3. **挂载磁盘**

   .. code-block:: bash

      mount /dev/vdb /data

4. **设置开机自动挂载**

   编辑 ``/etc/fstab`` 文件：

   .. code-block:: bash

      echo '/dev/vdb /data ext4 defaults 0 0' >> /etc/fstab

5. **验证挂载**

   .. code-block:: bash

      df -h

卸载磁盘
~~~~~~~~

.. code-block:: bash

   umount /data

环境管理
--------

Conda 环境
~~~~~~~~~~

.. code-block:: bash

   # 创建新环境
   conda create -n myenv python=3.8
   
   # 激活环境
   conda activate myenv
   
   # 安装包
   conda install numpy pandas
   
   # 退出环境
   conda deactivate
   
   # 查看所有环境
   conda env list

pip 包管理
~~~~~~~~~~

.. code-block:: bash

   # 安装包
   pip install package_name
   
   # 从 requirements.txt 安装
   pip install -r requirements.txt
   
   # 导出已安装的包
   pip freeze > requirements.txt

