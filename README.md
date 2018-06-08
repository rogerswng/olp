# olp
2018 Bachelor's Degree Graduation Design Beijing University of Posts and Telecommunications

### Tianxiao 的毕设项目配置指南

服务器运行环境 Ubuntu (Server) 16.04
后端运行环境 Python 3.x Based，请求处理服务器 Nginx，API 服务器 Gunicorn，数据库服务器 MySQL 5.x
前端运行环境 Node.js 可选

### 环境配置
#### 1. 获取 root 权限
各种安装涉及到一些文件夹的权限，推荐获取 root 权限在 root 下运行。Terminal 输入
```sudo -s```
获取 root 权限

#### 2. Python 环境安装
Ubuntu (Server，下简称Ubuntu) 一般都预装 Python2.x 和 Python3.x，为了保险可以查看下是否预装 Python3。Terminal 输入
```
python3 -V
```
如果可以打印出 Python 3.x 的版本信息则已预装 python3

#### 3. 安装 pip
在安装 Python 依赖时，使用 pip 会非常方便。为了将依赖和 Python 3.x 装到一起，我们安装 pip3。需要通过 apt-get 的方式安装。
由于默认 Ubuntu 的源都是在国外，可能会比较慢。我们首先进行 apt 源的替换。apt 源的配置文件路径为 /etc/apt/source.list
**3.1 更换 apt-get 源为清华大学镜像**
```
## 保险起见，先备份
sudo cp /etc/apt/source.list /etc/apt/source.list.bak
## 然后打开文件并修改内容
sudo vi /etc/apt/source.list
```
将文档内容完全覆盖为下面的内容
```
# 清华大学源
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main multiverse restricted universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main multiverse restricted universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-proposed main multiverse restricted universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main multiverse restricted universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main multiverse restricted universe
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main multiverse restricted universe
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main multiverse restricted universe
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-proposed main multiverse restricted universe
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main multiverse restricted universe
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main multiverse restricted universe
```
保存并执行更新
```
sudo apt-get update
```
**3.2 开始安装 pip3**
在 Terminal 输入
```
sudo apt-get install python3-pip
```
等待，直到安装完毕

**3.3 更换 pip 源** 
由于 pip 源默认是官方的 pypi 源，也会非常慢，因此我们更换下 pip 源。
pip的配置文件路径为 ~/.pip/pip.conf，由于我们使用的是 pip3，因此路径相应地为 ~/.pip3/pip3.conf。我们修改这个文件（若没有则创建目录及文件）
```
## 创建目录及文件
cd ~
sudo mkdir .pip3
cd .pip3
sudo touch pip3.conf

## 编辑文件
sudo vi pip3.conf
```
在文件中输入以下内容
```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```
保存，完成

**3.4 安装各种 Python 依赖**

**Gunicorn**
首先安装 Gunicorn，Gunicorn 是一款基于 wsgi 协议开发的服务器，非常好地支持 Python Web Application 的执行。在 Terminal 输入
```
sudo pip3 install gunicorn
```
等待，完成
**Flask，Flask-Restful**
Flask 和 Flask-RESTful 是我们后端构建 API 使用的两个网络框架。在 Terminal 分别输入
```
sudo pip3 install flask
sudo pip3 install flask-restful
```
等待，完成
**Flask-CORS**
Flask-CORS 是解决跨域请求的三方扩展。在 Terminal 输入
```
sudo pip3 install Flask-Cors
```
等待，完成
**PyMySQL**
PyMySQL 是 Python3.x 操作 MySQL 数据库的一套连接 API。在 Terminal 输入
```
sudo pip3 install pymysql
```
等待，完成
**DBUtils**
DBUtils 是我们用来建立数据库连接池的一套 API。在 Terminal 输入
```
sudo pip3 install DBUtils
```
**Requests**
requests 库是 Python 中一套新型的网络请求库。安装 Flask 时应该已经作为依赖安装过了，可以再安装看一下以防
