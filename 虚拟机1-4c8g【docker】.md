

# 00 测试docker服务器准备

![image-20240203161358830](C:\Users\黄飞\Desktop\【001】虚拟机篇\虚拟机1-4c8g.assets\image-20240203161358830.png)

<img src="C:\Users\黄飞\Desktop\【001】虚拟机篇\虚拟机1-4c8g.assets\image-20240203161648540.png" alt="image-20240203161648540" style="zoom:50%;" />

```bash
ip a
find /etc/ -name ifcfg*
vim /etc/sysconfig/network-scripts/ifcfg-ens32
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="static"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="ens32"
DEVICE="ens32"
ONBOOT="yes"
IPADDR="192.168.0.3"
GATEWAY="192.168.0.2"
NETMASK="255.255.255.0"
DNS1="114.114.114.114"
```

```bash
# 使网卡配置生效
systemctl restart network
#ping宿主机ip【可以通】
ping 192.168.0.1
#ping百度【可以通】
ping www.baidu.com
#ping同网段虚拟机【可以通】
ping 192.168.0.4
```

使用`nmcli`网卡管理工具查看各网卡状态，

```bash
nmcli device status
```

# 01 使用脚本自动安装

在测试或开发环境中 Docker 官方为了简化安装流程，提供了一套便捷的安装脚本，CentOS 系统上可以使用这套脚本安装，另外可以通过 `--mirror` 选项使用国内源进行安装：

> 若你想安装测试版的 Docker, 请从 test.docker.com 获取脚本

```bash
curl -fsSL test.docker.com -o get-docker.sh
curl -fsSL get.docker.com -o get-docker.sh
sudo sh get-docker.sh --mirror Aliyun
sudo sh get-docker.sh --mirror AzureChinaCloud
```

执行这个命令后，脚本就会自动的将一切准备工作做好，并且把 Docker 的稳定(stable)版本安装在系统中。

```bash
# 启动docker
sudo systemctl enable docker
sudo systemctl start docker
```

默认情况下，`docker` 命令会使用 [Unix socket](https://en.wikipedia.org/wiki/Unix_domain_socket) 与 Docker 引擎通讯。而只有 `root` 用户和 `docker` 组的用户才可以访问 Docker 引擎的 Unix socket。出于安全考虑，一般 Linux 系统上不会直接使用 `root` 用户。因此，更好地做法是将需要使用 `docker` 的用户加入 `docker` 用户组。

建立 `docker` 组：

```bash
sudo groupadd docker
```

将当前用户加入 `docker` 组：

```bash
echo $USER
sudo usermod -aG docker $USER
sudo usermod -aG docker fate
```

退出当前终端并重新登录，进行如下测试。

```bash
docker run --rm hello-world
```

配置镜像加速

执行以下命令，查看是否在 `docker.service` 文件中配置过镜像地址。

```bash
systemctl cat docker | grep '\-\-registry\-mirror'
```

如果该命令有输出，那么请执行 `$ systemctl cat docker` 查看 `ExecStart=` 出现的位置，修改对应的文件内容去掉 `--registry-mirror` 参数及其值，并按接下来的步骤进行配置。

如果以上命令没有任何输出，那么就可以在 `/etc/docker/daemon.json` 中写入如下内容（如果文件不存在请新建该文件）：

```bash
touch /etc/docker/daemon.json
cat > /etc/docker/daemon.json <<- 'eof'
{
  "registry-mirrors": [
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com"
  ]
}
eof
# 不知道为啥我测试的都有点问题，这个两个网址
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 开启实验特性

一些 docker 命令或功能仅当 **实验特性** 开启时才能使用，请按照以下方法进行设置。

从 `v20.10` 版本开始，Docker CLI 所有实验特性的命令均默认开启，无需再进行配置或设置系统环境变量。

编辑 `/etc/docker/daemon.json`，新增如下条目

```bash
{
  "experimental": true
}
```

这个需要跟上面的镜像配置文件写入到一个花括号之间

# 02 docker的一些用法

```bash
# 获取容器的id
docker inspect --format '{{ .NetworkSettings.IPAddress }}' <CONTAINER ID or NAME>
# 批量清理已经停止的容器
docker container prune
# 查看镜像、容器、数据卷所占用的空间。
docker system df
# 列表中只会显示顶层镜像，如果希望显示包括中间层镜像在内的所有镜像的话，需要加 `-a` 参数。
docker image ls
# 查看下载的镜像
docker images --digests
docker run --name webserver -d -p 801:80 nginx
# 修改其内容
docker exec -it webserver bash
echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
exit
# 查看具体的改动。
docker diff webserver
# docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]]
# 其中 `--author` 是指定修改的作者，而 `--message` 则是记录本次修改的内容。这点和 `git` 版本控制相似，不过这里这些信息可以省略留空。
docker commit --author "Tao Wang <twang2218@gmail.com>"  --message "修改了默认网页"  webserver nginx:v2
```

# 03 案例

```bash
docker pull registry
docker run -d -p 5000:5000 registry
docker pull nginx:alpine
docker tag nginx:alpine 127.0.0.1:5000/nginx:alpine
docker push 127.0.0.1:5000/nginx:alpine
docker rmi 127.0.0.1:5000/nginx:alpine
docker pull 127.0.0.1:5000/nginx:alpine
# 查看仓库含有的镜像信息
curl 127.0.0.1:5000/v2/_catalog 
curl 127.0.0.1:5000/v2/nginx/tags/list
```

```bash
docker pull wordpress:5
docker pull mariadb:10
docker pull nginx:alpine
docker run  -d --rm \
     --env MARIADB_DATABASE=db \
     --env MARIADB_USER=wp \
     --env MARIADB_PASSWORD=123 \
     --env MARIADB_ROOT_PASSWORD=123 \
     --name=db \
     mariadb:10
docker exec -it db mysql -u wp -p123 
show databases;
use db;
show tables;
docker inspect --format '{{ .NetworkSettings.IPAddress }}' db
# 172.17.0.2
docker run -d --rm \
     --env WORDPRESS_DB_HOST=172.17.0.2 \
     --env WORDPRESS_DB_USER=wp \
     --env WORDPRESS_DB_PASSWORD=123 \
     --env WORDPRESS_DB_NAME=db \
     --name wp \
     wordpress:5
docker inspect --format '{{ .NetworkSettings.IPAddress }}' wp
# 172.17.0.3
cat > wp.conf <<- 'eof'
server {
  listen 80;
  default_type text/html;
  location / {
    proxy_http_version 1.1;
    proxy_set_header HOST $host;
    proxy_pass http://172.17.0.3;
  }
}
eof
docker run -d --rm -p 80:80 -v `pwd`/wp.conf:/etc/nginx/conf.d/default.conf --name ng nginx:alpine
# 清除容器
docker rm -f 62bd713c4c2c 5975d37f66d9 e0a0cdb47704 3b5fcc8b5f72
```























