---
title: Docker从入门到放弃
top: true
cover: true
mathjax: true
tags:
  - tools
  - docker
date: 2019-09-12 04:29:08
author: zongdaoming
summary: Docker的入门级使用
categories: tools
---


请参阅[Tensorflow官方文档][2]和[Docker官方文档][1]获得更加详细使用信息。

## 启动 TensorFlow Docker 容器
要启动配置 TensorFlow 的容器，请使用以下命令格式：
```py
docker run [-it] [--rm] [-p hostPort:containerPort] tensorflow/tensorflow[:tag] [command]
```
其中，
- `docker run`是使用一个镜像生成一个运行的容器；
- `-it`指交互模式，启动后终端在运行着的容器里面，与之对应的有-d后端运行模式，启动后终端交互在实体机，要想进入容器需要使用命令`docker exec -it container-name bash` `docker exec -it container-name`意为交互模式进入正在运行的一个容器，bash意为进入容器后使用的命令，这里用的是bash，这样进入容器后就能执行shell；
- `--rm`意为退出shell的时候自动删除容器，常在测试的时候使用，这样不用每次修改去删除已有的容器
- `-p hostport:containerPort`指的是端口映射，前面的是实体机的端口，后面是容器里面暴露出的端口，两边端口可以不一样，这样同一个镜像可以启动多个对应不同端口的服务，如`-p 8000:4000`
- ` tensorflow/tensorflow[:tag]`表示的是镜像的名字，冒号后面是tag,表示版本，如果不显示给出tag这每次都会从hub上拉取latest的镜像。如基础镜像jupyter常推荐使用的一个版本(jupyter镜像预装了很多常用的模块)：`jupyter/datascience-notebook:281505737f8a`。 **如果网络环境不好的话比较费时间，推荐显式给出tag，这样每次构建都会使用已有的镜像。**

参看[`here`](https://hub.docker.com/r/tensorflow/tensorflow/tags/)查看每个tensorflow镜像具体的版本。
下载tensorflow镜像的命令是：`docker pull tensflow/tensorflow:tag`,如：
- docker pull tensorflow/tensorflow                     # latest stable release
- docker pull tensorflow/tensorflow:devel-gpu           # nightly dev release w/ GPU support
- docker pull tensorflow/tensorflow:latest-gpu-jupyter  # latest release w/ GPU support and Jupyter

## 启用带权限控制的容器

`docker run -d -p 8000:4000 jupyter/datascience-notebook start-notebook.sh --NotebookApp.password='pwd'`

### 运行带有目录共享的容器
`docker run -it --rm  -p 8000:4000 -v /home/zongdaoming/cv/GraphSAGE/:/home/zongdaoming/work  jupyter/datascience-notebook start-notebook.sh --NotebookApp.password='pwd'`
1. 密码生成的两种方式 
```py
import IPython
IPython.lib.passwd()
```
```py
ipython
from noteboook.auth import passwd
passwd()
quit()
```

2. 生成jupyter notebook 的配置文件
`jupyter notebook --generate-config` 这时候会生成配置文件 在` ~/.jupyter/jupyter_notebook_config.py`,我们需要修改配置文件
```py
vim ~/.jupyter/jupyter_notebook_config.py
```
加入如下内容，其中sha1那一串秘钥是上面生成的那一串
```py
c.NotebookApp.ip='*'
c.NotebookApp.password = u'pwd'
c.NotebookApp.open_browser = False
c.NotebookApp.port =4000
```
4000表明要使用container的4000端口访问jupyter，然后保存退出

3. 打开jupyter notebook
```py
jupyter notebook  --allow-root
```

## 带有目录共享的容器
`docker run -it --rm -p 8888:8888 -v /home/zongdaoming/cv/GraphSAGE/notebook:/home/zongdaoming/   jupyter/datascience-notebook start-notebook.sh --NotebookApp.password='pwd'`
`

VOLUME (shared filesystems)
`-v, --volume=[host-src:]container-dest[:<options>]: Bind ` 如`"/home/zongdaoming/cv/GraphSAGE/notebook:/home/zongdaoming/"`

container-dest必须始终是绝对路径，例如/src/docs。host-src可以是绝对路径，也可以是名称值。如果为host-dir提供绝对路径，Docker绑定将挂载到指定的路径。如果提供一个名称，Docker将根据该名称创建一个已命名的卷。 例如，可以为host-src值指定/foo或foo。如果提供/foo值，Docker将创建绑定挂载。如果您提供foo规范，Docker将创建一个命名卷。

## 构建Dockerfile
Dockerfile由多条指令组成，每条指令在编译镜像时执行相应的程序完成某些功能，由指令+参数组成，以逗号分隔，#作为注释起始符，虽说指令不区分大小写，但是一般指令使用大些，参数使用小写。One example:
```py
FROM jupyter/datascience-notebook:281505737f8a
MAINTAINER zong "15901768536@163.com"
# pytorch
RUN conda install pytorch torchvision -c soumith
# spotlight(https://github.com/maciejkula/spotlight)
RUN conda install -c maciejkula -c soumith spotlight=0.1.2
```
其中，
- 指令：**FROM** 功能描述：设置基础镜像 语法：FROM < image>[:< tag> | @< digest>] 
- 指令：**MAINTAINER** 功能描述：设置镜像作者 语法：MAINTAINER < name> 
- 指令：**RUN** 语法：RUN < command> RUN [“executable”,”param1”,”param2”] 提示：RUN指令会生成容器，在容器中执行脚本，容器使用当前镜像，脚本指令完成后，Docker Daemon会将该容器提交为一个**中间镜像**，供后面的指令使用 
- 指令：**COPY**  功能描述：复制文件到镜像中 语法：COPY < src>… < dest>|[“< src>”,… “< dest>”] 提示：指令逻辑和ADD十分相似，同样Docker Daemon会从编译目录寻找文件或目录，dest为镜像中的绝对路径或者相对于WORKDIR的路径

从构建的镜像运行容器，命令如下：
```python
docker build -t graphsage:gpu -f Dockerfile.gpu .
nvidia-docker run -it graphsage:gpu bash
```
##其他命令
- docker search python # 搜寻镜像
- docker stop container-name/container-id # 停止运行着的容器
- docker rm container-name # 删除已有的容器，要先停止
- docker rmi image-name # 删除已有的镜像

[1]: https://docs.docker.com/engine/reference/run/#clean-up---rm
[2]: https://tensorflow.google.cn/install/docker
[3]: https://blog.csdn.net/jianchengss/article/details/78224778