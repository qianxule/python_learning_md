@[toc]

# 环境管理
## 博主使用的环境

Anaconda3-2023.03-Windows-x86_64
pycharm-professional-2023.2
centos7

## 环境设置

首先先将这两个软件下载完成
![[image/135e622056f8d0dfcea3280861793d9b_MD5.png]]
然后：
![[image/01f3f74923ab874d000a982cf00ab7db_MD5.png]]
第三步：
![[image/0f95311aeb9d9c126096947009da2f19_MD5.png]]

## conda常用指令
env_name是要自己写的环境名称
```python
# 查看conda是否安装成功 且 加入环境变量（去高级设置）
conda info 或者 conda --version

# 查看所有环境
conda env list

# 创建环境 个人建议是在这里就直接把python版本也下载了
conda create -n env_name python=3.x

# 启用/激活环境
activate env_name（或 source activate env_name）

# 删除指定环境
conda env remove -name env_name

# 退出环境
deactivate（或 source deactivate）

# 在当前环境安装/删除   XXX 包
conda install XXX
conda remove XXX
```

## pycharm与环境的连接（新2023版本后）

我这边先创建好一个环境之后
![[image/2126f0d9f6f3f9c691752aefa05dc351_MD5.png]]
在新建环境这个地方就可以找到，和以前不一样

![[image/f0600413f4ef61727d5d3146e3fcb73b_MD5.png]]


![[image/b1fc2741e378a88736dbeb1b5cf79c7d_MD5.png]]

![[image/41a3dd038ecd9b86a07293e0f09a38c3_MD5.png]]

## 设置国内镜像（源管理）
添加清华镜像源

```python
conda config --add channels http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/win-64/
```

### 常用操作

```python
显示插入的源通道
conda config --show-sources

添加某一镜像源
conda config --add channels 源名称或链接

移除某一镜像源
conda config --remove channels 源名称或链接 
```

## centos下载anaconda
1. 切换到root/要使用的用户，创建一个放下载文件的路径
2. 前往anaconda官网，进行查询最新版本的anaconda[链接](https://www.anaconda.com/download#downloads)，并且复制其下载链接
3. 到服务器下：

```python
wget https://repo.anaconda.com/archive/Anaconda3-2023.07-2-Linux-x86_64.sh
```
4. 再输入：

```python
bash Anaconda3-2023.07-2-Linux-x86_64.sh 
```
然后就开始安装了，按照英文的说法进行加载，会车，然后yes yes 大概这样子

5.启动

```python
source ~/.bashrc
```
可以看到：
![[image/bc83b9d0cd95f74ca441c30494143b12_MD5.png]]
即为成功
# 远程开发

![[image/2b9c7031814ddf8091b3e18dd2288a45_MD5.png]]

而且如果有ip绑定的话：
![[image/156d637cbc0f6bb0679bcaadd7a334c5_MD5.png]]

![[image/bb818ed5b8e2c88f9667fa3dcc1f8bd6_MD5.png]]




















