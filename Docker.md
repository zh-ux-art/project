## Docker

### Docker基本组成

![2030366-20200630103057155-2072939834](https://gitee.com/zh-study/git-images/raw/master/img/2030366-20200630103057155-2072939834.png)

#### run流程![2030366-20200630103121945-102937605](https://gitee.com/zh-study/git-images/raw/master/img/2030366-20200630103121945-102937605.png)

#### 底层原理

```markdown
docker是一个client-server结构的系统，docker的守护进程运行在主机上，通告socket从客户端访问dockerserver接收到docker-client的指令，就会执行这个命令
```

![2030366-20200630103135524-144836814](https://gitee.com/zh-study/git-images/raw/master/img/2030366-20200630103135524-144836814.png)

### Docker安装

#### Docker的基本组成:

##### 镜像(Image):

docker镜像就好比是一个模板，可以通过这个模板来创建容器服务，通过这个镜像可以创建多个容器(最终服务运行或者项目运行就是在容器中的)

##### 容器(Container):

Docker利用 容器技术，独立运行在一个或者一个组应用，通过镜像来创建的

启动，停止，删除，基本命令

可以理解为一个简易的linux系统

##### 仓库(respository)

仓库就是存放镜像的地方

仓库分为公有仓库和私有仓库

Docker Hub

阿里云

#### Docker安装

```shell
#1.卸载旧版本
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
#2.需要的安装包
 yum install -y yum-utils
#3.设置镜像仓库 默认是dockhub
 yum-config-manager \
    --add-repo \
http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
#更新yum软件包索引
yum makecache fast
#4.安装docker相关的 docker-ce 社区版 docker-ee 企业版
yum install docker-ce docker-ce-cli containerd.io
#5.启动docker
systemctl start docker
#6.查看docker版本
docker version
#7.测试安装是正确
docker run hello-world
#8.查看镜像
docker images
#9.卸载docker
rm -rf /var/lib/docker
rm -rf /var/lib/containerd
```

10. 配置镜像加速器

您可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://xijmiato.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
### 常用命令

#### 镜像命令

##### docker  images 查看所有本地主机上镜像

-a  ,-- all列出所有镜像

-q --quiet 只显示id

##### docker  search 搜索镜像

-f , --filter 过滤查询

eg:--filter =stars=3000 

##### docker pull 拉取镜像

docker pull mysql:[tag]  tag指定版本，默认最新版

##### docker rmi -f 删除镜像

docker rmi -f  imageid 删除指定镜像

docker rmi -f  $(docker imgaes -aq)删除全部

#### 容器命令

有了镜像才可以创建容器

docker pull centox

##### 新建容器并启动

````shell
docker run [可选参数] image
#参数说明
--name="Name" 容器名字，用来区分容器
-d			  后台方式运行
-it			  使用交互方式运行，进入容器查看内容
-p			  指定容器的端口 -p 8080:8080
		-p ip:主机端口:容器端口
		-p 主机端口:容器端口(常用)
		-p 容器端口
-P			   随机指定端口

#测试 启动并进入容器
[root@izbp1gct6bthpkmwyuarm5z ~]# docker run -it centos /bin/bash
[root@7045010bff14 /]# ls 查看看容器内的centos，基础版本，很多命令不完善
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

#从容器中退出主机
exit
````

##### 列出所有的运行的容器

```shell
[root@izbp1gct6bthpkmwyuarm5z ~]# docker ps  查看运行中的容器
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@izbp1gct6bthpkmwyuarm5z ~]# docker ps -a 查看运行容器(含曾经运行过的)
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS                       PORTS     NAMES
7045010bff14   centos         "/bin/bash"              9 minutes ago   Exited (127) 8 minutes ago             elated_faraday
8dbe30025f87   87eca374c0ed   "docker-entrypoint.s…"   2 hours ago     Exited (0) 2 hours ago                 mystifying_einstein
72e9c30efb6b   87eca374c0ed   "docker-entrypoint.s…"   2 hours ago     Exited (1) 2 hours ago                 modest_mirzakhani
[root@izbp1gct6bthpkmwyuarm5z ~]# docker ps -a -n=1 -n:指定查看容器的个数
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                       PORTS     NAMES
7045010bff14   centos    "/bin/bash"   10 minutes ago   Exited (127) 8 minutes ago             elated_faraday
[root@izbp1gct6bthpkmwyuarm5z ~]# docker ps -aq  -q:查看容器id
7045010bff14
8dbe30025f87
```

##### 退出容器

```shell
exit #直接容器停止并退出
ctrl+p+q #容器不停止退出
```

##### 删除容器

```shell
docker rm 容器id   #删除指定的容器，不能删除正在运行的容器，如果要强制删除 rm -f
docker rm -f $(docker ps -aq) #删除所有的容器
docker ps -a -q|xargs docker rm #删除所有的容器
```

启动和停止容器操作

```shell
docker start 容器id   	#启动容器
docker restart 容器id     #重启容器
docker stop 容器id		#停止当前正在运行的容器
docker kill 容器id 		#强制停止当前容器
```

#### 常用其他命令

##### 后台启动容器

```shell
#命令 docker run -d 镜像名
[root@izbp1gct6bthpkmwyuarm5z ~]# docker run -d centos

#问题docker ps,发现centos停止了
[root@izbp1gct6bthpkmwyuarm5z ~]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
#常见的坑，docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
#nginx 容器启动后，发现自己没有提供服务,就会立刻停止，就是没有程序了
```

##### 查看日志

```shell
#docker logs -f -t --tail 10 容器id   没有日志

#编写shell脚本
[root@izbp1gct6bthpkmwyuarm5z ~]# docker run -d contos /bin/sh -c "while true;do echo zh;sleep 1;done";

#显示日志
-tf #显示日志
--tail number #要显示的日志条数
[root@izbp1gct6bthpkmwyuarm5z ~]# docker logs -tf --tail 10 d5a24ff3d186
```

##### 查看容器中进程信息 ps

```shell
#命令 docker top 容器id
[root@izbp1gct6bthpkmwyuarm5z ~]# docker top d5a24ff3d186
UID        PID        PPID        C         STIME         TTY          TIME                
root      20574       20554       0         10:51          ?          00:00:00           
root      30176       20574       0         10:59          ?          00:00:00           
```



##### 查看镜像元数据

```shell
#docker inspect 容器id
[root@izbp1gct6bthpkmwyuarm5z ~]# docker inspect d5a24ff3d186
[
    {
        "Id": "d5a24ff3d1862a919ed1aef45b236fdac43e4db86b8a3ae83a78296f85a8b0f3",
        "Created": "2021-05-12T02:51:44.666957951Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo zh;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 20574,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-05-12T02:51:45.203107794Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:300e315adb2f96afe5f0b2780b87f28ae95231fe3bdd1e16b9ba606307728f55",
        "ResolvConfPath": "/var/lib/docker/containers/d5a24ff3d1862a919ed1aef45b236fdac43e4db86b8a3ae83a78296f85a8b0f3/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/d5a24ff3d1862a919ed1aef45b236fdac43e4db86b8a3ae83a78296f85a8b0f3/hostname",
        "HostsPath": "/var/lib/docker/containers/d5a24ff3d1862a919ed1aef45b236fdac43e4db86b8a3ae83a78296f85a8b0f3/hosts",
        "LogPath": "/var/lib/docker/containers/d5a24ff3d1862a919ed1aef45b236fdac43e4db86b8a3ae83a78296f85a8b0f3/d5a24ff3d1862a919ed1aef45b236fdac43e4db86b8a3ae83a78296f85a8b0f3-json.log",
        "Name": "/quizzical_napier",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/3758945911efeefc928e6298e48ead7d124e49a8b0638354976b6ba0a3c060a5-init/diff:/var/lib/docker/overlay2/63d6e6daa81d12e43952a8469204c19c134470e105b8f40d32174e2ac13ba10e/diff",
                "MergedDir": "/var/lib/docker/overlay2/3758945911efeefc928e6298e48ead7d124e49a8b0638354976b6ba0a3c060a5/merged",
                "UpperDir": "/var/lib/docker/overlay2/3758945911efeefc928e6298e48ead7d124e49a8b0638354976b6ba0a3c060a5/diff",
                "WorkDir": "/var/lib/docker/overlay2/3758945911efeefc928e6298e48ead7d124e49a8b0638354976b6ba0a3c060a5/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "d5a24ff3d186",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo zh;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20201204",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "ccc73c59448ca3b917f17505b7fbfc03a6adc652ef2082f64106f95742049ef9",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/ccc73c59448c",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "852cadf7fb53d1d6f1e2895fc49226e59276fb6014c36455329cb58451ff5135",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.3",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:03",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "84057aab7e7a87da62ce6fad9a143b7e7918cd488ced4e4b28d84ed62c89fca1",
                    "EndpointID": "852cadf7fb53d1d6f1e2895fc49226e59276fb6014c36455329cb58451ff5135",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.3",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:03",
                    "DriverOpts": null
                }
            }
        }
    }
]

```

##### 进入当前正在运行的容器

```shell
#命令
#方式一
docker exex -it 容器id bashshell
[root@izbp1gct6bthpkmwyuarm5z ~]# docker exec -it 09fb3a0d4f78 /bin/bash
#方式二
docker attach 容器id
#测试
[root@izbp1gct6bthpkmwyuarm5z ~]# docker attach 09fb3a0d4f78

#docker exec #进入容器后开启一个新的终端，可以在里面操作(常用)
#docker attach #进入容器正在执行的终端，不会启动新的进程
```

##### 从容器中拷贝文件到主机

```shell
#命令
docker cp 容器id:/path/ /path

[root@izbp1gct6bthpkmwyuarm5z home]# docker cp 37c7396345c1:/home/test.java /home

#拷贝是一个手动过程，未来可以使用-v卷的技术，可以实现，自动同步 
```

#### 小结

![docker](https://gitee.com/zh-study/git-images/raw/master/img/docker.png)

```shell
attach					# 当前shell下attach连接指定运行镜像
build					# 通过dockerfile定制镜像
commit					# 提交当前容器为新的镜像
cp					# 从容器中拷贝指定文件或者目录到宿主机中
create					# 创建一个新的容器，同run 但不启动容器
diff					# 查看docker 容器变化
events					# 从docker 服务获取容器实时事件
exec					# 在已存在的容器上运行命令
export					# 导出容器的内容作为一个 tar 归档文件[对应import]
history					# 展示一个镜像形成历史
images					# 列出系统当前镜像
import        			        # 从tar包中的内容创建一个新的文件系统映像[对应export]
info					# 显示系统相关信息
inspect					# 查看容器详细信息
kill					# kill 指定容器
load					# 从一个tar 包中加载一个镜像[对应save]
login 					# 注册或者登陆一个docker源服务器
logout					# 从当前docker registry退出
logs					# 输出当前容器日志信息
port					# 查看映射端口对应的容器内部源端口
pause     				# 暂停容器
ps					# 列出容器列表
pull					# 从docker镜像源服务器拉取指定镜像或者库镜像
push					# 推送指定镜像或者库镜像至docker源服务器
restart					# 重启运行的容器
rm					# 移除一个或者多个容器
rmi					# 移除一个或多个镜像[无容器使用该镜像才可删除，否则需要删除相关容器才可继续或 -f 强制删除]
run 					# 创建一个新的容器并运行一个命令
save					# 保存一个镜像为一个tar包[对应load]
search					# 在docker hub 中搜索镜像
start 					# 启动容器
stop					# 停止容器
tag					# 给源中镜像打标签
top					# 查看容器中运行的进程信息
unpause					# 取消暂停容器
version					# 查看docker版本号
wait					# 截取容器停止时的退出状态值
```

#### 作业练习

##### Docker安装nginx

```shell
#1.搜索镜像 
docker search nginx 
#2.下载镜像
docker pull nginx
#3.运行测试
#-d 后台运行
#--name 给容器命名
#-p 宿主机端口，容器内部端口
docker run -d --name nginx01 -p 3344:80 nginx
curl localhost:3344

#进入容器
[root@izbp1gct6bthpkmwyuarm5z ~]# docker exec -it nginx01 /bin/bash
root@d83b9c85b5c7:/# ls
bin  boot  dev  docker-entrypoint.d  docker-entrypoint.sh  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@d83b9c85b5c7:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
```

思考：我们每次改动nginx配置文件，都需要进入容器内部，十分麻烦，我要是可以在容器外部提供一个映射路径，达到在容器修改文件名，容器内部就可以自动修改？-v数据卷

##### Docker安装tomcat

```shell
#官方的使用
docker run -it --rm tomcat:9.0
#docker run -it --rm 一般用来测试，用完就删除

#下载并启动
docker run -d -p 3355:8080 --name tomcat01 tomcat:9.0
 
#进入容器
docker exec -it tomcat01 /bin/bash

#存在问题，1.linux命令少了2.没有webapps 阿里云镜像的原因，默认是最小的镜像，所有不必要的东西都提出吊
#保证最小可运行的环境

#复制webapps.dist下文件到webapps
cp -r webapps.dist/* webapps
```

思考问题：我们以后要部署项目，如果每次都要进入容器是不是十分麻烦？要是可以在容器外部提供一个映射路径，webapps，在外部放置项目，就可以自动同步到内部

Docker安装es+kibana

```shell
#es 暴露的端口很多
#es 十分耗内存
#es 的数据一般需要放置到安全目录中 挂载
#--net somework ？网络配置

#启动 elasticsearch
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2/id

#存在问题:1.占用大量内存
#docker status 查看cpu状态

#解决方案:修改配置文件，增加内存限制 -e 环境配置修改
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=sing-node" -e ES_JAVA_OPTS="-Xms64m -Xmx256m" elasticsearch:7.12.1

curl localhost:9220
```

### Docker可视化

- portainer(先用这个)

```shell
#protainer:docker图形化界面管理工具
docker run -d -p 8088:9000 \
--restart=always \-v /var/run/docker.sock:/var/run/docker.sock \--privileged=true portainer/portainer
#访问测试:http://ip:8080
```

- Rancher(CI/CD再用)



### Dokcer镜像讲解

#### commit镜像

```shell
docker commit 提交容器成为一个新的副本

#命令和git原理类似
docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名:[tag]
```

#### 实战测试

```shell
#启动tomcat
docker run -it -p 8888:8080 tomcat:9.0

#进入容器
docker exec -it  id /bin/bash

#拷贝webapps.dist中文件至webapps下
cp -r webapps.dist webapps

#commit修改过的容器为一个新的镜像
docker commit -a ="zh" -m="add webapps app" 
```

![ ](https://gitee.com/zh-study/git-images/raw/master/img/image-20210516105041969.png)

如果想要保存当前容器的状态，可以通过commit来提交，获得一个镜像

### 容器数据卷

```shell
#-v 路径映射  主机路径:容器路径
#-p 端口映射
docker run -it -v /home/test:/home centos
#将主机上的/home/test与容器下的/home相映射，类似双向绑定
#可以解决容器删除后，数据丢失，通过映射到主机，实现数据持久化
#解决问题
	#1.数据持久化
	#2.通过映射路径，方便修改配置文件
```

![image-20210516113851940](https://gitee.com/zh-study/git-images/raw/master/img/image-20210516113851940.png)

#### 实战测试:安装mysql

思考:mysql的数据持久化问题

```shell
#获取镜像
docker pull mysql:5.7

#mysql启动时需要配置密码
#官方测试: docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

#启动容器
-d 后台运行
-p 端口映射
-v 卷挂载
-e 环境配置
--name 容器名
docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=zh123456 --name mysql01 mysql:5.7
```

#### 具备/匿名挂载

```shell
#匿名挂载
-v 容器内路径
docker run-d -P --name nginx01 -v /etc/nginx nginx

DRIVER    VOLUME NAME
local     0c20e2bd630293063f20fa6ab7f5c351ae3dc4892583c0e8ddc9b2f3d1cca48e

#具名挂载
-v 卷名:容器内路径
docker run -d -P --name nginx02 -v nginx:/etc/nginx nginx

DRIVER    VOLUME NAME
local     nginxlog

#查看指定卷
docker voluem inspect nginx
[root@izbp1gct6bthpkmwyuarm5z home]# docker volume inspect nginx
[
    {
        "CreatedAt": "2021-05-16T15:29:18+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/nginx/_data",
        "Name": "nginx",
        "Options": null,
        "Scope": "local"
    }
]
```

```shell
#三种挂载方式:匿名挂载、具名挂载、指定路径挂载
-v 容器内路径 			#匿名挂载
-v 卷名:容器内路径		   #具名挂载
-v /主机路径:容器内路径    #指定路径挂载
```

拓展

```shell
-v 容器内路径:ro/rw 改变读写权限
ro #read only 只读  只能通过宿主机操作，容器内部无法操作
rw #read write 可读可写
```

#### 自动挂载-DockerFile

Dockerfile就是用来构建docker镜像的构建文件、命令脚本

通过这个脚本可以生成镜像，镜像是一层一层的，脚本一个个的命令，每个命令都是一层

```shell
#[root@izbp1gct6bthpkmwyuarm5z home]# mkadir docker-test-volume

#[root@izbp1gct6bthpkmwyuarm5z docker-test-volume]# vi dockerfile

FROM centos //基于centos

VOLUME [""] //挂载卷

CMD echo "------end------" 

CMD /bin/bash  
#构建镜像
#-f 构建文件路径
#-t target
 docker build -f dockerfile -t zh/centos:1.0 .
 
#查看挂载路径
docker inspect 容器id
```

![image-20210516162952524](https://gitee.com/zh-study/git-images/raw/master/img/image-20210516162952524.png)

### 数据卷容器

多个容器间同步

![2030366-20200630103633379-1005173056](C:\Users\18273\Pictures\壁纸\2030366-20200630103633379-1005173056.png)

```shell
#启动三个容器，通过我们自己写的容器
docker run -it --name docker01 zh/centos:1.0  #父容器
docker run -it --name docker02 --volumes-from docker01 zh/centos:1.0 
docker run -it --name docker03 --volumes-from docker01 zh/centos:1.0
#三个容器的数据卷共享一个主机映射目录

#多个mysql实现数据共享
docker run -d -p 3306:3306 -v /etc/mysql/conf.d  -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7

docker run -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 --volumes-from mysql01  mysql:5.7

#结论
   #容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止
   #但是一旦持久到本地，这个时候本地的数据是不会删除的
```

![image-20210517102713405](https://gitee.com/zh-study/git-images/raw/master/img/image-20210517102713405.png)

### DockerFile

dockerfile是用来构建镜像的文件，命令参数脚本

#### 构建步骤

##### 1.编写一个dockerfile文件

##### 2.docker build构建成为一个镜像

##### 3.docker run 运行镜像

##### 4.docker push 发布镜像(docker hub,阿里云镜像仓库)

#### Dockerfile构建过程

##### 基础知识

- 每个保留关键字(指令)都必须是大写字母
- 从上到下顺序执行
- 表示注释(#)
- 每个指令都会创建提交一个新的镜像，并提交

![2030366-20200630103737429-2119801149](https://gitee.com/zh-study/git-images/raw/master/img/2030366-20200630103737429-2119801149.png)

Dockfile是面向开发的，以后发布项目，作镜像，就需要编写dockerfile文件

Docker镜像逐渐成为企业交付的标准

Dockerfile:构建文件，定义了一切的步骤，源代码

Dockerimages:通告Dockerfile构建生成的镜像，最终发布运行的产品

Docker容器:容器就是镜像运行起来提供服务

#### Dockerfile指令

![2030366-20200630103751230-376026982](https://gitee.com/zh-study/git-images/raw/master/img/2030366-20200630103751230-376026982.png)

```shell
FROM              # 基础镜像，一切从这开始构建
MAINTAINER 		  # 镜像是谁写的，姓名+邮箱
RUN				  # 镜像构建的时候需要运行的命令
ADD				  # 添加内容
WORKDIR			  # 镜像的工作命令
VOLUME     		  # 挂载的目录
EXPOSE			  # 指定暴露端口		  
CMD				  # 指定容器启动的时候运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT        # 指定这个容器启动的时候要运行的命令可以追加命令
ONBUILD			  # 当构建一个被继承的 dockerfile ，这个时候就会运行ONBUILD的指令，触发指令
COPY              # 类似ADD，将我们的文件拷贝到镜像中
ENV				  # 构建的时候设置环境变量
```

#### 实战测试-centos

##### 创建一个自己的centos

```shell
#1.编写Dockerfile文件
vim mydockerfile-cnetos

#2.编写指令
FROM centos
MAINTAINER Z<1827339757@qq.com>  #作者信息(姓名+邮箱)

ENV MYPATH /usr/local  #环境变量
WORKDIR $MYPATH		   #工作路径

RUN yum -y install vim  
RUN yum -y install net-tools

EXPOSE 80
CMD echo $MYPATH
CMD echo echo "-----end-----"
CMD /bin/bash

#2.通过dockerfile构建镜像
# docker build -f dockerfile文件路径 -t 镜像名:[tag] .
docker build -f mydockerfile-centos -t mycentos:0.1 .

#docker history imageid 查看指定镜像的创建历史
```

#### cmd/entrypoint

```shell
#cmd和entrypoint所定义的都是容器启动时执行的命令
#不同点:
#cmd只有最后一个会生效，可被替代
#entrypoint会命令缀加
```

cmd

```shell
#dockerfile-cmd-test
FROM centos
CMD ["ls","-a"]

#构建cmdtest镜像
docker build -f dockerfile-cmd-test -t cmdtest .

#启动容器，启动容器时，会执行ls -a命令
docker run cmdtest
#错误缀加命令启动
docker run cmdtest -l #启动失败，因为-l命令，会替换ls -a命令,想要执行ls -al命令，应该如下
					  #docker run cmdtest ls -al
```

entrypoint

```shell
#dockerfile-entrypoint-test
FROM centos
ENTRYPOINT ["ls","-a"]

#构建entrypointtest镜像
docker build -f dockerfile-entrypoint-test -t entrypointtest .

#启动容器，容器启动时，执行ls -a命令
docker run entrypointtest
#命令缀加
docker run entrypointtest -l#最终执行命令 docker run entrypointtest ls -al
```

#### 实战测试-tomcat

```shell
#1.准备镜像tomcat压缩包，jdk压缩包
#2.编写dockerfile文件，官方命名Dockerfile,build时会自动寻找这个文件，不需要再加-f指定
#DockFile
FROM centos
MAINTAINER zh<1827339757@qq.com> #名字

COPY readme.txt /usr/local/readme.txt #复制当前目录下文件至容器

ADD jdk-8u251-linux-x64.tar.gz /usr/local #jdk压缩包和容器中的解压位置
ADD apache-tomcat-9.0.38.tar.gz /usr/local 

RUN yum -y install vim #镜像构建时需要执行的命令(安装vim命令)

ENV MYPATH /usr/local #设置环境变量
WORKDIR $MYPATH #设置工作目录

ENV JAVA_HOME /usr/local/jdk1.8.0_251 #设置环境变量
ENV CLASSPATH $JAVA_HOME/bin/dt.jar:$JAVA_HOME/lib/tools.jar  #设置环境变量，分隔符 :
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.38 #设置环境变量
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.38 #设置环境变量
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin #设置总的环境变量

EXPOSE 8080 #设置暴露端口

CMD /usr/local/apache-tomcat-9.0.38/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.38/bin/blogs/catalina.out #设置容器启动时间执行的命令


#3. 构建镜像
docker build -t diytomcat .


#4. 运行
docker run -d -p 3310:8080 --name zhtomcat -v /home/zh/tomcat/test:/usr/local/apache-tomcat-9.0.38/webapps/test -v /home/zh/tomcat/tomcatlogs:/usr/local/apache-tomcat-9.0.38/logs/ diytomcat


5、访问测试
docker run -d -p 3310:8080 --name zhtomcats -v /home/zh/tomcatt/test:/usr/local/apache-tomcat-9.0.38/webapps/test -v /home/zh/tomcat/tomcatlogs:/usr/local/apache-tomcat-9.0.38/logs zhrepo/divtomcat:1.0
```

### 发布镜像

#### DockerHub

```shell
#登录dockerhub账号
docker login -u 用户名
#上传镜像
docker push

#push可能遇到问题解决方法
#1.build时候添加dockehub用户名，
docker build -t zhrepo/mytomcat:0.1
#使用docker tag然后再次push
docker tag 镜像id zh/mytomcat:1.0
```

#### 阿里云

```shell
#登录阿里云Docker Registry
docker login --username=用户名 registry.cn-hangzhou.aliyuncs.com

#将镜像推送到Registry
docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/zhrepo/zhprojects:[镜像版本号]
docker push registry.cn-hangzhou.aliyuncs.com/zhrepo/zhprojects:[镜像版本号]

#拉取镜像
docker pull registry.cn-hangzhou.aliyuncs.com/zhrepo/zhprojects:[镜像版本号]
```

### docker-compose

部署项目:vueblog

需要环境:jdk,mysql,redis,nginx

nginx配置文件

```markdown

worker_processes  1;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    server {
        listen       80;
        server_name  localhost;

       location / {
            root  /usr/share/nginx/html;
            index  index.html index.htm;
            try_files $uri $uri/ /index.html;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}

```

```yml
#1.编写Dockerfile
FROM jdk:8

EXPOSE 8082

ADD vueblog-0.0.1-SNAPSHOT.JAR app.jar

RUN bash -c "touch /app.jar"

ENTRYPOINT ["java","-jar","/app.jar"]

#2.编写docker-compose
version: '3'

services:
  nginx:
    image: nginx:latest
    ports:
      - 80:80
    rest
    volumes:
      - /home/zh/myproject/nginx/html:/usr/share/nginx/html
      - /home/zh/myproject/nginx/nginx.conf:/etc/nginx/nginx.conf
    privileged: true
  mysql:
    image: mysql:8.0.2
    ports:
    - 3310:3306
    volumes:
      - /home/zh/myproject/mysql/data:/var/lib/mysql
      - /home/zh/myproject/mysql/conf.d:/etc/mysql/conf.d
    environment:
      - MYSQL_ROOT_PASSWORD=zh123456
  redis:
    image: redis:latest

  vueblog:
    image: vueblog:latest
    build: . #表示当前目录下的Dockerfile
    ports:
    - 8082:8082
    depends_on:
      - mysql
      - redis
#3.构建镜像
docker-compose build
#4.启动所有容器
docker-compose up -d
```
