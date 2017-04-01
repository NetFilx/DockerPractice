# Docker 使用

​	用命令行一行一行构建Docker的image是比较稳妥的方法，但是比较麻烦，所以我这里就使用Dockerfile来构建image啦。

先给出Dockerfile的示例：

```dockerfile
FROM hub.c.163.com/library/ubuntu:latest
MAINTAINER limbo wyh19960405@gmail.com

# 更换源，不然下载gg
RUN cp /etc/apt/sources.list /etc/apt/sources.list.bac && \
    echo "deb http://mirrors.163.com/ubuntu/ trusty main restricted universe multiverse" > /etc/apt/sources.list && \
    echo "deb http://mirrors.163.com/ubuntu/ trusty-security main restricte universe multiverse" >> /etc/apt/sources.list && \
    echo "deb http://mirrors.163.com/ubuntu/ trusty-updates main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb http://mirrors.163.com/ubuntu/ trusty-proposed main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb http://mirrors.163.com/ubuntu/ trusty-backports main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb-src http://mirrors.163.com/ubuntu/ trusty main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb-src http://mirrors.163.com/ubuntu/ trusty-security main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb-src http://mirrors.163.com/ubuntu/ trusty-updates main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb-src http://mirrors.163.com/ubuntu/ trusty-proposed main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb-src http://mirrors.163.com/ubuntu/ trusty-backports main restricted universe multiverse" >> /etc/apt/sources.list

RUN apt-get update
RUN apt-get install -y nginx
COPY ./www /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

现在来一行一行解释：

- FROM表示从哪个仓库拉取什么基础镜像，因为伟大的墙的原因，这里我使用的是网易蜂巢的一个镜像，使用的是最新的ubuntu镜像
- MAINTAINER 表示这个镜像文件的作者
- RUN表示在容器内部的shell里面执行命令，可以出现**多次**。这里先是更换了容器中的ubuntu的源，提高下载速度，更新源，再下载nginx服务器。
- COPY是将目标文件拷到容器中的/usr/share/nginx/html,因为这个是nginx的启动目录
- EXPOSE 表示需要对外开放80端口
- CMD表示命令，用于开启nginx，CMD只能在Dockerfile中出现**一次**

在Dockerfile的同级目录下建立一个www目录，在目录下新建一个index.html，在其中加入下列内容：

```html
<html>
<head>
Learn Docker
</head>
<body>
<h1>Enjoy Docker!</h1>
</body>
</html>
```

打开终端，进入Dockerfile的目录中，执行下列命令

```Shell
 docker build -t first .
```

docker build 表示使用Dockerfile来构建一个镜像，-t表示后面跟着的是这个镜像的标签，后面的 . 表示Dockerfile就在当前目录

接下来再在终端输入

```Shell
docker run -p 80:80 -d first
```

docker run 用来运行一个容器，-p表示参数，-d表示后台运行，first就是我们刚刚构建的镜像。

此时，就可以访问你的服务器啦，看到Enjoy Docker表示ok

## 注意

1. 一定要换仓库的地址，也就是FROM后面的那个地址，不然下载时间比你走十分钟去吃一碗面在搭讪一个小姐姐还长。
2. 记得换源，不然和上面的情况一样
3. Dockerfile比较麻烦的，可能写不对需要一步步调试，但是这个还是方便的，毕竟Docker Hub速度感人，不可能把东西都放上面然后在一次一次拉