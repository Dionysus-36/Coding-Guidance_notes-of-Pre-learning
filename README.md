# Coding-Guidance_notes-of-Pre-learning
基于Linux系统的人工智能深度学习的前置学习笔记。包括WSL的Ubuntu环境搭建、计算机网络相关概念、构建局域网网络代理、Vscode下的Ubuntu及其Git使用。
## chapter1 Linux环境搭建
本文依据Windows系统的WSL下搭建Linux环境，且选择了Ubuntu系统。完整的搭建步骤还需要部署Cuda和cuDNN，但考虑到本机的GPU为Intel，不支持Cuda和cuDNN，因此跳过了这两个步骤。以下内容包括：下载WSL及Ubuntu、下载Pytorch。关于Ubuntu链接Vscode的内容将在第三章中介绍。

### 1. 下载安装WSL与Ubuntu
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

### 2. 安装Anaconda
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

### 3. 创建Python llm环境
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

### 4. 安装Pytorch
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

## chapter2 Linux入门
### 1. Linux基础命令
#### 1.1 操作系统与虚拟机软件
操作系统：控制硬件+支持软件运行的计算机程序

虚拟机软件：能够虚拟出计算机的软件

虚拟机：一个虚拟的真实计算机。可以用于学习一个新的操作系统。

常用的虚拟机软件：
- Vmware
- VirtualBox

#### 1.2 Ubuntu操作系统
软件：菜单栏在最顶端、放大/缩小/取消位于左侧
目录结构：单一盘符
- / ：根目录
- /bin ：存放可执行二进制文件（ls，cd）
- /etc ：存放系统配置文件
- /home：用户家目录

#### 1.3 Linux内核
Linux内核：操作系统内部操作和控制硬件设备的核心程序
Linux发行版：内核的基础上加上常用软件所形成的产品

#### 1.4 查看目录命令
- 查看目录命令的使用

查看**当前所在目录**信息：
``` bash
ls
```
以树状方式显示目录信息：
``` bash
tree
```
注：使用tree指令需要安装命令，指令如下：
``` bash
sudo apt update
sudo apt install tree -y
```
- 查看当前目录路径
``` bash
pwd
```
- 清除终端内容
``` bash
clear
```

#### 1.5 切换目录命令
- 切换到**当前目录下的**指定目录
``` bash
cd 目录名
```
- 切换到主目录
``` bash
cd ~
```
- 切换到上一级目录
``` bash
cd ..
```
若要向前切换两级，则为：
``` bash
cd ../..
```
- 切换到当前目录
``` bash
cd .
```
- 切换到上一次目录
``` bash
cd -
```

#### 1.6 绝对路径与相对路径
绝对路径：从根目录算起的路径

例如：
- /home/User/Python
- /bin

相对路径：从当前目录算起的路径

例如：
- ./test/hello  向下寻找
- ../static/images  向上一级寻找

使用方法：
- 使用绝对路径切换到桌面
``` bash
cd /home/python/desktop
```
- 使用相对路径切换到桌面
``` bash
cd downloads
cd ../desktop
```
注：按一次Tab键自动补全；按两次Tab键显示所有子目录

#### 1.7 创建、删除文件及目录命令
- 创建指定文件：
``` bash
touch 文件名
```
- 创建指定目录
``` bash
mkdir 目录名
```
- 删除指定文件
``` bash
rm 文件名
```
- 删除指定目录（递归方式）
``` bash
rm 目录名 -r
```
- 删除空目录
``` bash
rmdir 目录名
```
*注：对应的路径方式与1.6相同*

#### 1.8 复制、移动文件及目录命令
- 拷贝文件到目录中
``` bash
cp 文件名 目录名
```
- 拷贝目录到目录中（递归方式）
``` bash
cp 目录名 目录名 -r
```
- 移动文件/目录到目录中
``` bash
mv 文件名/目录名 目录名
```
**注：mv移动指令不需要 -r 选项**
- 重命名（前提是名称2不存在！）
``` bash
mv 文件名1 文件名2
mv 目录名1 目录名2
```
*注：对应的路径方式与1.6相同*

#### 1.9 终端命令格式的组成
格式：command [-options][parameter]
- conmand：命令名。
- [-options]：选项。可以有0，1或多个。包括短选项（-r）和长选项（--help）
- [parameter]：参数。可以有0，1或多个。如目录名、文件名。

*注：一般情况下，选项和参数没有顺序要求。但scp命令必须先选项后参数。*

#### 1.10 查看命令帮助
主要有两种方式，可以使用--help：
``` bash
--help
rm --help
mv --help
```
也可以使用man打开手册：
``` bash
man
man rm
man mv
```

#### 1.11 ls命令选项
| 命令选项 | 说明 |
|:-----|:-----|
|-l|以列表方式呈现|
|-h|以大小单位显示，默认为字节|
|-a|显示隐藏文件和隐藏目录|

#### 1.12 mkdir和rm命令选项
- mkdir命令选项

| 命令选项 | 说明 |
|:-----|:-----|
|-p|创建所依赖的目录|
例如创建一个嵌套目录：
``` bash
mkdir AA/BB/CC -P
```

- rm命令选项

| 命令选项 | 说明 |
|:-----|:-----|
|-i|交互式提示|
|-r|递归删除目录及其内容|
|-f|强制删除，忽略不存在的文件，无需提示|
|-d|删除空目录|

#### 1.13 cp和mv命令选项
- cp命令选项

| 命令选项 | 说明 |
|:-----|:-----|
|-i|交互式提示|
|-r|递归拷贝目录及其内容|
|-v|显示拷贝后的路径描述|
|-a|保留文件原有权限（其他用户角色的权限）|

- mv命令选项

| 命令选项 | 说明 |
|:-----|:-----|
|-i|交互式提示|
|-v|显示移动后的路径描述|

### 2. Linux高级命令
#### 2.1 输出重定向
| 命令 | 说明 |
|:-----|:-----|
|>|如果文件存在会覆盖原有内容|
|>>|如果文件存在会追加写入文末|

#### 2.2 查看文件内容命令
- 查看小型文件
``` bash
cat
```
- 分屏查看大型文件
``` bash
more
```
注：/enter查看下一行；/q退出；/blank或/f查看下一页

- 启动文本编辑器窗口
``` bash
gedit
```

- 管道
例：查看bin目录的内容，用管道输出
``` bash
ls /bin | more
```

#### 2.3 连接命令
- 软连接

类似于Windows上的快捷方式，方法如下：
``` bash
ln -s 文件名/目录名 软链接名
```
**注：文件名/目录名用 绝对路径**

- 硬链接

类似于源文件的别名，直接指向文件数据，不依赖于源文件。可以防止文件的误删。
``` bash
ln 文件名 硬链接名
```

#### 2.4 文本搜索命令
- 文本搜索命令
``` bash
grep ['文本内容'] [文件名]
```
- grep的选项

| 命令选项 | 说明 |
|:-----|:-----|
|-i|忽略大小写|
|-n|显示匹配行号|
|-v|显示不包含匹配文本的所有行|

- grep结合正则表达式使用

| 命令选项 | 说明 |
|:-----|:-----|
|^|以指定字符串开头|
|$|以指定字符串结尾|
|.|匹配一个非换行符的字符|

例：
``` bash
grep '^a' 1.txt
grep 'b$' 1.txt -ni
grep 'a.b' 1.txt
```

- grep管道搜索

例：
``` bash
ls /home | grep 'lib'
```

#### 2.5 查找文件命令
- 在指定目录下查找文件/目录
``` bash
find
```
- find的选项

| 命令选项 | 说明 |
|:-----|:-----|
|-name|根据文件名/目录名搜索|

- find结合通配符使用（用于模糊搜索）

| 命令选项 | 说明 |
|:-----|:-----|
|*|代表0个或多个任意字符|
|?|代表任意一个字符|

例：
``` bash
find . -name "1*.txt"
find .. -name "2?.txt"
```
**注：通配符还可以结合ls、mv、cp使用，且不用双引号**

#### 2.6 压缩和解压缩命令
Linux系统支持的压缩格式包括：.gz .bz2 .zip

注：.gz和.bz2需要用tar命令来压缩和解压缩；.zip需要用zip来压缩，用unzip来解压缩

- tar命令
``` bash
tar [选项] [压缩包名] [文件名]
```
- tar命令选项

| 命令选项 | 说明 |
|:-----|:-----|
|-c|创建打包文件|
|-v|显示打包或解包的详细信息|
|-f|指定文件名称（必须放到所有选项后面）|
|-z|压缩或解压缩（.gz）|
|-j|压缩或解压缩（.bz2）|
|-x|解包|
|-C|解压缩到指定目录|

例：
``` bash
tar -czvf test.tar.gz *.txt
tar -zxvf test.tar.gz -C ./A
```

- zip和unzip命令

| 命令选项 | 说明 |
|:-----|:-----|
|-zip|压缩成.zip文件|
|-unzip|解压缩.zip文件|

- unzip命令选项

| 命令选项 | 说明 |
|:-----|:-----|
|-d|解压缩到指定目录|

例：
``` bash
zip test *.txt
unzip test.zip -d A
```

#### 2.7 修改文件权限命令
- 修改文件权限命令
``` bash
chmod [权限] [文件名]
```
包含字母法、数字法两种方式

- 角色说明

|角色| 说明 |
|:-----|:-----|
|u|user/该文件的所有者|
|g|group/用户组|
|o|other/其他用户|
|a|all/所有用户|

- 权限设置说明

|操作符| 说明 |
|:-----|:-----|
|+|添加权限|
|-|撤销权限|
|=|设置权限|

- 权限说明

|操作符| 说明 |
|:-----|:-----|
|r|可读，对应数字4|
|w|可写，对应数字2|
|x|可执行.对应数字1|
|-|无任何权限，对应数字0|

例：
``` bash
chmod u-r 1.txt
chmod u=rw,g=r,o=- 2.txt
chmod 644 3.txt
```

注：#的三个含义：1. 注释  2. 表示指定文件的编码格式  3. 指定文件解释器路径

#### 2.8 获取管理员权限的相关命令
- sudo命令的使用

| 命令 | 说明 |
|:-----|:-----|
|sudo|某个命令执行获取管理员权限|
|sudo -s|切换到root用户，获取管理员权限|
|exit|推出管理员权限|

- which命令的使用

which命令可查看命令路径：
``` bash
which [命令]
```

#### 2.9 用户相关操作
- 创建（添加）用户
``` bash
useradd
```
相关选项：

| 命令选项 | 说明 |
|:-----|:-----|
|-m|自动创建一个用户主目录|
|-g|在指定用户组下创建用户（主组）|

若要查看是否成功添加，则输入：
``` bash
cat /etc/passwd
```
最后一行出现新建的用户信息，则成功。（以上只是其中一种方式）

- 设置密码
``` bash
sudo password [用户名]
```

- 切换用户
``` bash
su - [用户名]
```

- 修改用户信息
``` bash
usermod
```
相关选项：

| 命令选项 | 说明 |
|:-----|:-----|
|-G|设置一个附加组|
|-g|修改用户组（主组）|

若想要给予新添用户sudo权限，则需增加sudo附加组，输入：
``` bash
sudo usermod -G sudo [新用户名]
```

#### 2.10 用户组相关操作
- 创建（添加）用户组
``` bash
groupadd [用户组名]
```

- 删除用户组
``` bash
groupdel [用户组名]
```
注：若删除用户组，需先删除其中的用户目录

#### 2.11 远程登录、远程拷贝命令
- 远程登录命令
``` bash
ssh
```
远程登录效果：

``` 文本内容
ssh客户端软件（客户端电脑）--(ssh)-> ssh服务端软件（服务端电脑）
```

安装ssh服务端/客户端软件指令：
``` bash
sudo apt-get install openssh-server
sudo apt-get install openssh-client
```
验证安装是否成功：
``` bash
apt list | grep open-server
apt list | grep open-client
```

- 远程拷贝命令
``` bash
scp
```
远程登录效果：

``` 文本内容
ssh客户端软件（客户端电脑）<-(sch)-> ssh服务端软件（服务端电脑）
```

#### 2.12 编辑器vim
- 介绍

vim是一款文本编辑器，可以直接用命令修改代码。

新建/打开vim方式：
``` bash
vim [文件名]
```

- vim的工作模式
  - 命令模式：缺省模式
  - 编辑模式：按i进入，按esc退出
  - 末行模式：按:进入，按esc退出，按w保存

其余命令：
``` bash
:q   退出
:x   保存退出
:q!  强制退出
```

常用命令：

| 命令 | 说明 |
|:-----|:-----|
|yy|复制光标所在行|
|p|粘贴一次|
|dd|删除当前行|
|u|撤销|

#### 2.13 软件的安装与卸载
Ubuntu软件安装与卸载主要有两种方式：**离线安装卸载**（deb文件格式安装）与 **在线安装卸载**（apt-get方式安装）

- deb文件格式安装

安装和卸载软件的deb安装包：
``` bash
dpkg
```
安装安装包对应软件：
``` bash
dpkg -i
```
离线卸载：
``` bash
sudo dpkg -r [安装包]
```

- apt-get方式安装

在线安装软件命令格式：
``` bash
sudo apt-get install [安装包]
```
在线卸载
``` bash
sudo apt-get remove [安装包]
```

- 安装软件镜像源

安装国内镜像源有助于大幅提升在线安装速度。常用的镜像源包括阿里镜像源、清华大学镜像源等。以清华大学镜像源为例，首先备份原来的软件源：
``` bash
sudo cp /etc/apt/sources.list.d/ubuntu.sources /etc/apt/sources.list.d/ubuntu.sources.backup
```

随后，编辑软件源：
``` bash
sudo nano /etc/apt/sources.list.d/ubuntu.sources
```
打开清华大学镜像源官网，找到Ubuntu，打开后复制对应内容，黏贴至ubuntu.sources中。具体内容请自行搜索后复制。

最后，记得更新配置：
``` bash
sudo apt update
```
并进行验证：
``` bash
apt-cache policy
```