## Docker

大型项目组件较多，运行环境也较为复杂，部署时会碰到一些问题：

- 依赖关系复杂，容易出现兼容性问题
- 开发、测试、生产环境有差异

**Docker如何解决依赖的兼容问题的？**

- 将应用的Libs（函数库）、Deps（依赖）、配置与应用一起打包
- 将每个应用放到一个隔离**容器**去运行，避免互相干扰

#### Docker与虚拟机

虚拟机（virtual machine）是在操作系统中模拟硬件设备，然后运行另一个操作系统，比如在 Windows 系统里面运行 Ubuntu 系统，这样就可以运行任意的Ubuntu应用了。

![image-20221212195323914](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221212195323914.png)

#### 镜像与容器

镜像（Image）：Docker将应用程序及其所需的依赖、函数库、环境、配置等文件打包在一起，称为镜像。

容器（Container）：镜像中的应用程序运行后形成的进程就是**容器**，只是Docker会给容器做隔离，对外不可见。

#### Docker和DockerHub

- DockerHub：DockerHub是一个Docker镜像的托管平台。这样的平台称为Docker Registry。

- 国内也有类似于DockerHub 的公开服务，比如 [网易云镜像服务](https://c.163yun.com/hub)、[阿里云镜像库](https://cr.console.aliyun.com/)等。

#### Docker架构

Docker是一个CS架构的程序，由两部分组成：

- 服务端(server)：Docker守护进程，负责处理Docker指令，管理镜像、容器等

- 客户端(client)：通过命令或RestAPI向Docker服务端发送指令。可以在本地或远程向服务端发送指令。

![image-20221212195933100](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221212195933100.png)

#### 安装Docker

参考CentOS7安装Docker.md文档

#### Docker基本操作

##### 镜像相关命令

- 镜像名称一般分两部分组成：[repository]:[tag]。

- 在没有指定tag时，默认是latest，代表最新版本的镜像

![image-20221212200307380](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221212200307380.png)

镜像操作有哪些？

- docker images
- docker rmi
- docker pull
- docker push
- docker save 
- docker load

![image-20221212200448065](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221212200448065.png)

##### 去docker hub查看Nginx的容器运行命令

```
docker run --name containerName -p 80:80 -d nginx
```

命令解读：

- docker run ：创建并运行一个容器
- --name : 给容器起一个名字，比如叫做mn
- -p ：将宿主机端口与容器端口映射，冒号左侧是宿主机端口，右侧是容器端口
- -d：后台运行容器
- nginx：镜像名称，例如nginx

##### docker run命令的常见参数

- --name：指定容器名称
- -p：指定端口映射
- -d：让容器后台运行

##### 查看容器日志的命令：

- docker logs
- 添加 -f 参数可以持续查看日志

##### 查看容器状态：

- docker ps
- 添加-a参数查看所有状态的容器

##### 删除容器：

- docker rm
- 不能删除运行中的容器，除非添加 -f 参数

##### 进入容器：

- 命令是docker exec -it [容器名] [要执行的命令]

- exec命令可以进入容器修改文件，但是在容器内修改文件是不推荐的

  ```shell
  docker exec -it mn bash
  ```

  

##### 创建一个redis容器，并支持数据持久化

1. 到DockerHub搜索Redis镜像
2. 查看Redis镜像文档中的帮助信息
3. 利用docker run 命令运行一个Redis容器

```shell
docker run --name redis -p 6379:6379 -d redis redis-server --appendonly yes
```

![image-20221212201358281](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221212201358281.png)

##### 容器与数据耦合的问题

![image-20221212201415640](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221212201415640.png)

#### 数据卷

**数据卷（volume）**是一个虚拟目录，指向宿主机文件系统中的某个目录。

![image-20221212201444105](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221212201444105.png)

##### 操作数据卷

数据卷操作的基本语法如下：

```
docker volume [COMMAND]
```

docker volume命令是数据卷操作，根据命令后跟随的command来确定下一步的操作：

**command：**

- create 创建一个volume
- inspect 显示一个或多个volume的信息
- ls 列出所有的volume
- prune 删除未使用的volume
- rm 删除一个或多个指定的volume

##### 挂载数据卷

我们在创建容器时，可以通过 -v 参数来挂载一个数据卷到某个容器目录

```shell
docker run ：就是创建并运行容器
-- name mn ：给容器起个名字叫mn
-v html:/root/htm ：把html数据卷挂载到容器内的/root/html这个目录中
-p 8080:80 ：把宿主机的8080端口映射到容器内的80端口
nginx ：镜像名称
```

数据卷挂载方式：

- -v volumeName: /targetContainerPath
- 如果容器运行时volume不存在，会自动被创建出来

```
提示：目录挂载与数据卷挂载的语法是类似的：
-v [宿主机目录]:[容器内目录]
-v [宿主机文件]:[容器内文件]
实现思路如下：
在将课前资料中的mysql.tar文件上传到虚拟机，通过load命令加载为镜像
创建目录/tmp/mysql/data
创建目录/tmp/mysql/conf，将课前资料提供的hmy.cnf文件上传到/tmp/mysql/conf
去DockerHub查阅资料，创建并运行MySQL容器，要求：
挂载/tmp/mysql/data到mysql容器内数据存储目录
挂载/tmp/mysql/conf/hmy.cnf到mysql容器的配置文件
设置MySQL密码
```

![image-20221212202010345](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221212202010345.png)

#### Dockerfile自定义镜像

镜像是将应用程序及其需要的系统函数库、环境、配置、依赖打包而成。

![image-20221212202142052](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221212202142052.png)

镜像是分层结构，每一层称为一个Layer

- BaseImage层：包含基本的系统函数库、环境变量、文件系统
- Entrypoint：入口，是镜像中应用启动的命令
- 其它：在BaseImage基础上添加依赖、安装程序、完成整个应用的安装和配置

##### 什么是Dockerfile

**Dockerfile**就是一个文本文件，其中包含一个个的**指令****(Instruction)**，用指令来说明要执行什么操作来构建镜像。每一个指令都会形成一层Layer。

| **指令**   | **说明**                                     | **示例**                    |
| ---------- | -------------------------------------------- | --------------------------- |
| FROM       | 指定基础镜像                                 | FROM centos:6               |
| ENV        | 设置环境变量，可在后面指令使用               | ENV key value               |
| COPY       | 拷贝本地文件到镜像的指定目录                 | COPY ./mysql-5.7.rpm /tmp   |
| RUN        | 执行Linux的shell命令，一般是安装过程的命令   | RUN yum install gcc         |
| EXPOSE     | 指定容器运行时监听的端口，是给镜像使用者看的 | EXPOSE 8080                 |
| ENTRYPOINT | 镜像中应用的启动命令，容器运行时调用         | ENTRYPOINT java -jar xx.jar |

更新详细语法说明，请参考官网文档： https://docs.docker.com/engine/reference/builder

#### DockerCompose

- Docker Compose可以基于Compose文件帮我们快速的部署分布式应用，而无需手动一个个创建和运行容器！

- Compose文件是一个文本文件，通过指令定义集群中的每个容器如何运行。

  ```
  version: "3.8"
  services:
    mysql:
      image: mysql:5.7.25
      environment:
       MYSQL_ROOT_PASSWORD: 123 
      volumes:
       - "/tmp/mysql/data:/var/lib/mysql"
       -  "/tmp/mysql/conf/hmy.cnf:/etc/mysql/conf.d/hmy.cnf"
    web:
      build: .
      ports:
       - "8090:8090"
  
  ```

  

- DockerCompose的详细语法参考官网：https://docs.docker.com/compose/compose-file/

#### Docker镜像仓库

镜像仓库（ Docker Registry ）有公共的和私有的两种形式：

- 公共仓库：例如Docker官方的 [Docker Hub](https://hub.docker.com/)，国内也有一些云服务商提供类似于 Docker Hub 的公开服务，比如 [网易云镜像服务](https://c.163.com/hub)、[DaoCloud](https://hub.daocloud.io/)[ ](https://hub.daocloud.io/)[镜像服务](https://hub.daocloud.io/)、[阿里云镜像服务](https://cr.console.aliyun.com/)等。
- 除了使用公开仓库外，用户还可以在本地搭建私有 Docker Registry。企业自己的镜像最好是采用私有Docker Registry来实现。

**在私有镜像仓库推送或拉取镜像**

推送镜像到私有镜像服务必须先tag，步骤如下：

- 重新tag本地镜像，名称前缀为私有仓库的地址：192.168.150.101:8080/

  ```
  docker tag nginx:latest 192.168.150.101:8080/nginx:1.0 
  ```

- 推送镜像

  ```
  docker push 192.168.150.101:8080/nginx:1.0 
  ```

- 拉取镜像

  ```
  docker pull 192.168.150.101:8080/nginx:1.0 
  ```

  

1. 推送本地镜像到仓库前都必须重命名(docker tag)镜像，以镜像仓库地址为前缀
2. 镜像仓库推送前需要把仓库地址配置到docker服务的daemon.json文件中，被docker信任
3. 推送使用docker push命令
4. 拉取使用docker pull命令