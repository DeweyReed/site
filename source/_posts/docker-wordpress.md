---
layout: post
title: Docker快速搭建Wordpress + HTTPS
date: 2018/3/20
categories: Website
---

###### 不出天大的事以后就不会修改维护的一篇Post

虽然最终选择了GithubPages + Hexo，但之前使用Docker搭的Wordpress + HTTPS。搭完后才发现维护起来又累又贵，就把整个流程贴出来留给有缘人吧。

甩锅申明：出了问题我不知道怎么解决，请问万能的Stackoverflow吧😉

好处：

1. 方便，命令几乎都是cd, ls和nano/vim。
1. 快速，不需要LAMP或者LNMP漫长的安装/编译过程。新机器建站和删库跑路都很快。
1. 干净，不污染其他文件夹，删除网站只需要一条Docker命令。
1. https，Let's Encrypt! 不仅是网站，还要加个绿色的小锁。

<!--more-->

坏处：

1. 出了问题不好解决，包括通过命令行修改网站、给网站装个监控什么的等等，都涉及有一定门槛儿的Docker命令。但对于正常地通过Wordpress面板的操作足够了。

## 准备工作

先把域名解析上，最后上小锁时需要。

新系统来了先

```bash
apt update
apt upgrade
```

老系统没有`apt`，用`apt-get`

## 安装Docker

Docker是将程序和其依赖放在容器中，再进行部署的开源软件……总之很方便就对了。

在其官网，不同系统有不同的安装教程。

[Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
[Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
[Fedora](https://docs.docker.com/install/linux/docker-ce/centos/)
[Ubuntu](https://docs.docker.com/install/linux/docker-ce/fedora/)

中文文档在

[Ubuntu](https://docs.docker-cn.com/engine/installation/linux/docker-ce/ubuntu/)
[Debian](https://docs.docker-cn.com/engine/installation/linux/docker-ce/debian/)
[Fedora](https://docs.docker-cn.com/engine/installation/linux/docker-ce/centos/)
[Ubuntu](https://docs.docker-cn.com/engine/installation/linux/docker-ce/fedora/)

举例：我的Debian8（注意对内核有要求）是：

```bash
apt-get install \
     apt-transport-https \
     ca-certificates \
     curl \
     gnupg2 \
     software-properties-common

curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | apt-key add -

# 可选，查看签名是否添加成功，在输出的内容中应该有下面那行内容。
apt-key fingerprint 0EBFCD88
# 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88

add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
   $(lsb_release -cs) \
   stable"

apt update
apt install docker-ce
```

## 安装Docker-compose

一次运行多个容器的工具。

官网有[安装指南](https://docs.docker.com/compose/install/)，中文的似乎没翻译。

举例：

```bash
curl -L https://github.com/docker/compose/releases/download/1.19.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

## 安装代理

安装之前，先新建一些文件夹，来存放我们网站的内容。

```bash
#/你的目录
#    /nginx-files
#    /database
#    /wordpress
#        /core/files
#        /wp-content
# 命令是：
mkdir nginx-files
mkdir database
mkdir wordpress
cd wordpress
mkdir core
mkdir wp-content
cd core
mkdir files
cd 回你的最上层目录
```

安装代理需要这么一个Github Repo: [Web Proxy using Docker, NGINX and Let's Encrypt](https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion)

它的README有安装教程，但有几个坑。

1. 下载Repo：`git clone https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion.git`
1. 【可选】如果不使用反向代理的话，可以删掉Repo中`/conf.d/realip.conf`
1. 【可选】可以修改`/conf.d/uploadsize.conf`中的`client_max_body_size`的值，默认是100MB，限制上传文件的最大大小，不够就加大。
1. 改名，`mv .env.sample .env`
1. 修改变量，`nano .env`，或者用`vim`。
1. 需要修改的变量有
    - `IP`：修改为机器的IP 
    - `NGINX_FILES_PATH`：修改为刚刚新建的`nginx-files`文件夹的路径
    - `USE_NGINX_CONF_FILES`：【可选】去掉变量前的井号。这里是要使用conf.d文件夹下的设置，包括之前第三步上传文件的最大大小。如果不在乎这几个设置也可以不管它（后果就是搭好后上传主题时，2MB就GG）
1. 运行`./start.sh`

## 安装Wordpress

又需要一个Github Repo：[Using Wordpress with SSL enabled integrated with NGINX proxy and autorenew LetsEncrypt certificates](https://github.com/evertramos/docker-wordpress-letsencrypt)

同样有几个坑。

1. 下载：`git clone https://github.com/evertramos/wordpress-docker-letsencrypt.git`

1. 【可选】新建一个`uploads.ini`的文件，内容是
    ```plain
    file_uploads = On
    memory_limit = 64M
    upload_max_filesize = 64M
    post_max_size = 64M
    max_execution_time = 600
    ```
    这里跟上一步中修改上传文件的最大大小是一致的，要么一起用，要么就不用，下几步的可选也是一样的，不用的话都可以跳过。这里的最大上传文件设置为64MB，不够随便改。
1. 【可选】修改`docker-compose.yml`，在`services - wordpress - volumes`下加一行`- ${PHP_UPLOAD_CONF}:/usr/local/etc/php/conf.d/uploads.ini`
1. 改名，`mv .env.sample .env`
1. 修改`.env`
    - `DB_PATH`: 修改为刚刚新建的`database`文件夹的路径
    - `MYSQL_ROOT_PASSWORD`: 挑一个数据库根密码
    - `MYSQL_DATABASE`: 数据库的名字，随便取，习惯上用`wordpress`
    - `MYSQL_USER`: 数据库用户，随便取，习惯上用`wordpress`
    - `MYSQL_PASSWORD`: 挑一个数据库密码
    - `WP_CORE`: 修改为刚刚新建的`core`文件夹的路径
    - `WP_CONTENT`: 修改为刚刚新建的`wp-content`文件夹的路径
    - `DOMAINS`: 修改为开头就应该解析上的域名
    - `LETSENCRYPT_EMAIL`: 修改为你的邮箱，用于Let's Encrypt
    - 【可选】如果执行了2、3步的话，最后加一行`PHP_UPLOAD_CONF=/刚刚新建的文件路径/uploads.ini`
1. 运行`docker-compose up -d`

等个几十秒一分钟，网站就可以使用了，带个https绿色小锁。

## 命令与支援

- 不带`-d`直接运行`docker-compose up`可以查看日志，用于排错。
- 在第二个Repo的文件夹中，运行`docker-compose down`可以停止Wordpress。
- `docker container ls [--all]`: 显示所有Container，最后不带`--all`的仅显示运行中的，带的话还显示已停止的。最后应该一共有5个Container。
- `docker rm [--force] linux_tweet_app`: 删除一个Container，带`--force`强制删除，无视是否运行。
