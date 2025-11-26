开发环境配置
==============

本章节介绍如何配置开发环境，包括 PyCharm 远程开发、数据存储、后台运行等。

PyCharm SSH 远程开发
--------------------

PyCharm 支持通过 SSH 连接到远程服务器进行开发和调试。

.. warning::

   **Windows 用户注意**
   
   由于文件目录结构兼容性问题，**Windows 系统下的 PyCharm 无法直接通过 SSH 连接服务器进行远程开发**。
   建议 Windows 用户使用 :doc:`best_practices` 中推荐的"本地开发 + 上传运行"模式。
   
   以下配置指南主要适用于 Mac/Linux 用户。

配置 SSH 连接
~~~~~~~~~~~~~

1. **打开 PyCharm**，选择 ``File`` → ``Settings`` (Windows/Linux) 或 ``PyCharm`` → ``Preferences`` (Mac)

2. **添加 SSH 配置**
   
   导航到 ``Tools`` → ``SSH Configurations``，点击 ``+`` 添加新配置：
   
   * **Host**: 服务器 IP 地址（如 ``120.232.255.237``）
   * **Port**: SSH 端口号（如 ``30371``）
   * **Username**: 用户名（通常为 ``root``）
   * **Authentication type**: 选择 ``Password`` 或 ``Key pair``
   * **Password**: 输入服务器密码

3. **测试连接**
   
   点击 ``Test Connection`` 确认连接成功。

配置远程解释器
~~~~~~~~~~~~~~

1. 打开 ``Settings`` → ``Project`` → ``Python Interpreter``

2. 点击齿轮图标 → ``Add...`` → ``SSH Interpreter``

3. 选择 ``Existing server configuration``，选择刚才创建的 SSH 配置

4. 指定远程 Python 解释器路径，例如：
   
   .. code-block:: text
   
      /root/miniconda3/envs/myenv/bin/python

5. 设置远程项目路径（代码将同步到此目录）：
   
   .. code-block:: text
   
      /root/projects/myproject

配置文件同步
~~~~~~~~~~~~

PyCharm 会自动将本地代码同步到远程服务器。您可以在 ``Tools`` → ``Deployment`` 中查看和配置同步设置。

.. note::

   * 代码会自动上传到服务器的指定目录
   * 修改本地文件后会自动同步到服务器
   * 运行和调试都在服务器上执行

数据文件存储建议
----------------

数据盘挂载
~~~~~~~~~~

**强烈建议将数据存储在挂载的数据盘上**，而不是系统盘。

原因：

* 数据盘在实例释放后可以选择继续保存
* 数据盘容量大，适合存储大型数据集
* 数据盘可以单独管理和备份

推荐目录结构
~~~~~~~~~~~~

.. code-block:: text

   /data/                    # 挂载的数据盘
   ├── datasets/             # 数据集存储
   │   ├── imagenet/
   │   ├── coco/
   │   └── custom_data/
   ├── models/               # 训练好的模型
   │   ├── checkpoints/
   │   └── pretrained/
   └── outputs/              # 输出结果
       ├── logs/
       └── results/

/root/                       # 系统盘（代码存储）
├── projects/                # 项目代码
│   ├── project1/
│   └── project2/
└── miniconda3/              # Python 环境

在代码中使用数据盘路径
~~~~~~~~~~~~~~~~~~~~~~

在您的 Python 代码中，使用服务器上的绝对路径：

.. code-block:: python

   # 错误示例：使用本地路径
   # data_path = "C:/Users/username/data/dataset.zip"  # ❌
   
   # 正确示例：使用服务器路径
   data_path = "/data/datasets/imagenet/train"  # ✅
   model_save_path = "/data/models/checkpoints/model.pth"  # ✅
   output_dir = "/data/outputs/experiment1"  # ✅

.. warning::

   **重要提示**
   
   * 代码需要上传到服务器运行，所有路径应使用服务器的绝对路径
   * 不要使用 Windows 风格的路径（如 ``C:\``）
   * 数据文件也需要提前上传到服务器

后台运行程序
------------

使用 tmux 保持会话
~~~~~~~~~~~~~~~~~~

``tmux`` 是一个终端复用器，可以让程序在后台持续运行，即使断开 SSH 连接也不会中断。

安装 tmux
^^^^^^^^^

.. code-block:: bash

   # Ubuntu/Debian
   apt-get install tmux
   
   # CentOS/RHEL
   yum install tmux

基本使用
^^^^^^^^

.. code-block:: bash

   # 创建新会话
   tmux new -s training
   
   # 在会话中运行程序
   python train.py
   
   # 分离会话（程序继续运行）
   # 按键：Ctrl+B，然后按 D
   
   # 查看所有会话
   tmux ls
   
   # 重新连接到会话
   tmux attach -t training
   
   # 关闭会话
   tmux kill-session -t training

常用快捷键
^^^^^^^^^^

* ``Ctrl+B`` 然后 ``D``: 分离会话
* ``Ctrl+B`` 然后 ``C``: 创建新窗口
* ``Ctrl+B`` 然后 ``N``: 切换到下一个窗口
* ``Ctrl+B`` 然后 ``P``: 切换到上一个窗口
* ``Ctrl+B`` 然后 ``%``: 垂直分割窗口
* ``Ctrl+B`` 然后 ``"``: 水平分割窗口

使用 nohup
~~~~~~~~~~

``nohup`` 也可以让程序在后台运行：

.. code-block:: bash

   # 后台运行并将输出重定向到文件
   nohup python train.py > training.log 2>&1 &
   
   # 查看运行的进程
   ps aux | grep train.py
   
   # 查看日志
   tail -f training.log
   
   # 停止程序
   kill <进程ID>

使用 screen
~~~~~~~~~~~

``screen`` 是另一个类似 tmux 的工具：

.. code-block:: bash

   # 创建新会话
   screen -S training
   
   # 运行程序
   python train.py
   
   # 分离会话：Ctrl+A 然后 D
   
   # 重新连接
   screen -r training

代码部署最佳实践
----------------

上传代码到服务器
~~~~~~~~~~~~~~~~

**方法一：使用 PyCharm 自动同步**

配置好远程解释器后，PyCharm 会自动同步代码。

**方法二：使用 SCP**

.. code-block:: bash

   # 上传整个项目文件夹
   scp -P 30371 -r /local/path/myproject root@120.232.255.237:/root/projects/

**方法三：使用 Git**

.. code-block:: bash

   # 在服务器上克隆代码
   cd /root/projects
   git clone https://github.com/username/myproject.git
   
   # 更新代码
   cd myproject
   git pull

路径配置建议
~~~~~~~~~~~~

1. **使用配置文件**

   创建 ``config.py`` 存储路径配置：
   
   .. code-block:: python
   
      # config.py
      import os
      
      # 数据路径
      DATA_ROOT = "/data/datasets"
      MODEL_ROOT = "/data/models"
      OUTPUT_ROOT = "/data/outputs"
      
      # 具体路径
      TRAIN_DATA = os.path.join(DATA_ROOT, "imagenet/train")
      VAL_DATA = os.path.join(DATA_ROOT, "imagenet/val")
      CHECKPOINT_DIR = os.path.join(MODEL_ROOT, "checkpoints")

2. **使用命令行参数**

   .. code-block:: python
   
      import argparse
      
      parser = argparse.ArgumentParser()
      parser.add_argument('--data_dir', type=str, default='/data/datasets/imagenet')
      parser.add_argument('--output_dir', type=str, default='/data/outputs/exp1')
      args = parser.parse_args()

3. **检查路径是否存在**

   .. code-block:: python
   
      import os
      
      data_path = "/data/datasets/imagenet"
      if not os.path.exists(data_path):
          raise FileNotFoundError(f"数据路径不存在: {data_path}")

完整工作流程示例
----------------

.. code-block:: bash

   # 1. SSH 登录服务器
   ssh root@120.232.255.237 -p 30371
   
   # 2. 创建项目目录
   mkdir -p /root/projects/myproject
   mkdir -p /data/datasets
   mkdir -p /data/outputs
   
   # 3. 上传代码（本地执行）
   scp -P 30371 -r ./myproject root@120.232.255.237:/root/projects/
   
   # 4. 上传数据（本地执行）
   scp -P 30371 -r ./dataset.zip root@120.232.255.237:/data/datasets/
   
   # 5. 在服务器上解压数据
   cd /data/datasets
   unzip dataset.zip
   
   # 6. 创建 tmux 会话
   tmux new -s training
   
   # 7. 激活环境并运行
   conda activate myenv
   cd /root/projects/myproject
   python train.py --data_dir /data/datasets/imagenet --output_dir /data/outputs/exp1
   
   # 8. 分离会话（Ctrl+B 然后 D）
   
   # 9. 随时重新连接查看进度
   tmux attach -t training

.. tip::

   **最佳实践总结**
   
   * ✅ 代码放在 ``/root/projects/``
   * ✅ 数据放在 ``/data/datasets/``（挂载的数据盘）
   * ✅ 使用 tmux 或 screen 保持长时间运行
   * ✅ 所有路径使用服务器的绝对路径
   * ✅ 使用 Git 或 SCP 部署代码
   * ✅ 定期保存检查点到 ``/data/models/``
