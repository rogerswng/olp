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

**Tips: Ubuntu Server 18.04 提示 cannot import module 'sysconfig'，通过安装 python3-distutils 解决**

**3.3 更换 pip 源**  

由于 pip 源默认是官方的 pypi 源，也会非常慢，因此我们更换下 pip 源。
pip的配置文件路径为 ~/.pip/pip.conf。我们修改这个文件（若没有则创建目录及文件）
```
## 创建目录及文件
cd ~
sudo mkdir .pip
cd .pip
sudo touch pip.conf

## 编辑文件
sudo vi pip.conf
```
在文件中输入以下内容
```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```
保存，完成

#### 4 安装各种 Python 依赖

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

**Tips: Ubuntu Server 18.04 提示**

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

requests 库是 Python 中一套新型的网络请求库。安装 Flask 时应该已经作为依赖安装过了，可以再安装看一下以防缺失
```
sudo pip3 install requests
```

**pysnowflake**
pysnowflake 是我们生成全局唯一递增 id 的工具。
```
sudo pip3 install pysnowflake
```

#### 5 安装 MySQL
首先需要确认系统中没有安装过 mysql，执行命令 mysql -V，如果提示没有安装 mysql 则可继续下述操作。如果有安装过，则需要先彻底卸载 mysql。

**5.1 安装 MySQL**
```
sudo apt-get install mysql-server
```
这里我们选择只安装命令行版本的 mysql，最核心的部分。

安装过程中会提示我们给 root 用户设置密码，直接设置就好。

安装完毕后我们使用 service 命令开启 MySQL 服务
```
service mysql start
```
类似地，当我们需要停止或重启 mysql 服务时，使用 service mysql stop/restart 即可

**5.2 配置 MySQL**

首先我们进入 mysql console，使用命令 mysql -u root -p，并输入密码，最好设置为 rootroot。代码中写死了这个密码。也可以设置别的，但是需要在数据库配置文件和后端代码中改一下 dbConfig.py

接下来，我们首先创建 olp 数据库

```
create database olp;
```

接下来我们来处理下编码问题，首先查看下当前的字符编码

```
show variables like "char%";
```

可以看到 server charset 为 latin1，这可能导致我们存储的中文出现乱码的情况。我们在配置文件当中进行修改。

```
sudo vi /etc/mysql/mysql.conf.d/mysql.conf
## 在这个文件中增加一行

charset_set_server = utf8
```

重启 mysql 服务

```
service mysql restart
```

**5.3 表的初始化（非必要，可以等到配置全部结束再做）**

**Tips：需要先把源代码克隆到本地**

我们使用代码中的 dbInitialize.py 脚本进行数据库表的初始化。
```
## 首先进入到 dbInitialize.py 目录下
sudo python3 dbInitialize.py
```

等待完成即可

#### 6 Nginx 的安装与配置
**6.1 安装 Nginx**

执行下述命令：
```
sudo apt-get install nginx

## 安装完毕后我们可以查看下下面两个文件
cat /etc/nginx/nginx.conf
## 上面的文件是 nginx 的基础配置文件
cat /etc/nginx/sites-enabled/default
## 上面的文件是 server 配置
```

**6.2 配置 Nginx**

需要配置几项，修改文件 /etc/nginx/sites-enabled/default

1. 配置反向代理

Gunicorn 监听本机 8000 端口，对 /api 请求进行转发

```
## 增加下述内容
location /api {
  proxy_pass http://127.0.0.1:8000;
}
```

2. 配置上传最大请求体积

上传视频可能会有比较大的请求，对 /api 请求的体积设置为 1000M

```
## 在刚才的 location 中增加下述内容
client_max_body_size 1000M;
```

3. 配置静态转发

由于 vue 打包后的静态文件也在 /static/ 下，因此需要修改 Flask 的静态目录为 /statics/，gunicorn 的静态文件在 /statics/ 请求下

```
## 增加下述内容
location /statics {
  proxy_pass http://127.0.0.1:8000;
}
```

4. Vue 使用 history 模式，需要配置转发时 try_files 转到 index

```
## 在 location / 中增加修改 try_files
location / {
  try_files $uri / /index.html;
}
```

保存文件

**6.3 完成配置**

重启 Nginx 服务，在 Terminal 输入命令

```
# 第一句检查是否有语法错误
nginx -t 
# 重启服务
nginx -s reload
```

#### 7 Gunicorn 相关
**7.1 Gunicorn 运行**

```
## 进入到 app.py 的目录下运行, log 使用 debug 模型打 log 出来会很详细
gunicorn --reload --log-level=debug app:application
```

**7.2 彻底关闭 gunicorn**

```
pstree -ap | grep gunicorn
kill -9 {process_id}
```

#### 8 其他
**8.1 开启 snowflake 服务**

```
## 后台运行 snowflake 服务
nohup snowflake_start_server &

```

#### 9 代码相关

首先从仓库拉取代码到本地，使用 git clone 或从仓库下载 zip 再解压
```
# 进入到我们想要放的目录下，如 /usr/local/
cd /usr/local
git clone --depth=1 https://github.com/rogerswng/olp-latest.git
```

运行后端代码使用 gunicorn，进入刚才 clone 下来的代码目录，找到 api-dist 目录
```
cd olp-latest-master
cd api-dist
# 检查该目录下 static 文件夹是否重命名为 statics，没有的话使用 mv static statics 重命名
# 可以使用 ls 命令看下该目录下是否有 app.py 文件，使用下面的命令开启 api 服务
gunicorn --reload --daemon --access-logfile gunicorn.access.log app:application
```

前端代码的打包文件放在了 olp-latest/olp-pages/dist 目录下，我们可以在 nginx 配置文件中将根目录修改为这个目录，或者将这个目录下的文件 cp 到指定目录下。我们使用第一个方案，直接修改根目录。

```
# 找到 nginx 配置文件
sudo vi /etc/nginx/sites-enabled/default
# 将第一个 server {} 中的 root 配置进行修改，默认的值为 root /var/www/html;修改为我们的目录
server {
  ...
  root /usr/local/olp-latest-master/olp-pages/dist;
  ...
}
# 保存，检查语法错误，重启
nginx -t
nginx -s reload
```
