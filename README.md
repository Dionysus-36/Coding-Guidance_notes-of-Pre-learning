# Coding-Guidance_notes-of-Pre-learning
基于Linux系统的人工智能深度学习的前置学习笔记。包括WSL的Ubuntu环境搭建、计算机网络相关概念、构建局域网网络代理、Vscode下的Ubuntu及其Git使用。
## 1. Linux环境搭建
本文依据Windows系统的WSL下搭建Linux环境，且选择了Ubuntu系统。完整的搭建步骤还需要部署Cuda和cuDNN，但考虑到本机的GPU为Intel，不支持Cuda和cuDNN，因此跳过了这两个步骤。以下内容包括：下载WSL及Ubuntu、下载Pytorch。关于Ubuntu链接Vscode的内容将在第三章中介绍。

### 1.1 下载安装WSL与Ubuntu
打开主页搜索栏，搜索：
```
启用或关闭Windows功能
```
下拉至末尾处，选中
```
适用于Linux的Windows子系统
虚拟机平台
```
后点击确定，重启后即完成安装。

此外，也可以在Windows中以管理员身份打开Powershell，安装WSL和Ubuntu：
``` powershell
wsl --install -d Ubuntu
```

重启后搜索栏搜索Ubuntu并打开，第一次运行会要求设置：
```
UNIX username：Linux 用户名
password：Linux 密码
```
输入密码时屏幕没有回显，这是正常现象。

回到Windows Powershell后输入：
``` powershell
wsl -l -v
```
可检查是否顺利安装。

### 1.2 安装Anaconda
conda能够是在linux系统上搭建多个独立环境，常用的conda包含Mininconda和Anaconda，本文采用Anaconda。
首先，打开Ubuntu终端，使用wget命令下载Anaconda的安装脚本。请将链接替换为Anaconda官网的最新版本：
``` bash
wget https://repo.anaconda.com/archive/Anaconda3-2025.12-2-Linux-x86_64.sh
```
随后使用以下命令安装脚本。注意同样要替换为实际下载文件的名称：
``` bash
bash ./Anaconda3-2025.12-2-Linux-x86_64.sh
```
安装向导会要求你审阅许可协议并选择安装位置。按Enter键滚动，当提示时输入yes以接受许可协议。然后，你可以接受默认的安装位置或指定新位置。

询问是否初始化 Conda 时输入 yes,然后执行以下命令来激活配置：
``` bash
source ~/.bashrc
```
激活后，会发现在命令行最开头出现了（base）字样

### 1.3 创建Python llm环境
接下来，将使用conda创建一个Python环境，称之为llm。执行以下命令：
``` bash
conda create -n llm python=3.11
conda activate llm
```
激活后，会发现在命令行最开头变成了（llm）字样，即成功进入llm环境。

若想要退出llm环境，输入以下命令即可：
``` bash
conda deactivate
```

### 1.4 安装Pytorch
本文采用的是Intel XPU 版 PyTorch。若需要安装NVIDIA版，推荐先安装CUDA与cuDNN以提高运行效率。请自行搜索相关教程。

在激活了llm环境后，按照 PyTorch 官方 Intel XPU 安装方式，使用 XPU 软件源：
```bash
python -m pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/xpu
```
安装完成后，检查导入、版本和显卡：
```bash
python -c "import torch; print('PyTorch:', torch.__version__); print('CUDA:', torch.version.cuda); print('XPU available:', torch.xpu.is_available()); print('XPU count:', torch.xpu.device_count()); print('GPU:', torch.xpu.get_device_name(0))"
```
检查依赖：
``` bash
python -m pip check
```

详细内容参考：[PyTorch Intel XPU 官方入门指南](https://docs.pytorch.org/docs/main/notes/get_start_xpu.html)

