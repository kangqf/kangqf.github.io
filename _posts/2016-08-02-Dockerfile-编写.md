---
title: Dockerfile 编写
categories:
  - config
tags:
  - Docker
date: 2016-08-02 22:20:54
---

## Dockerfile 的编写。我们可以使用 docker build 来创建一个新的镜像。为此，首先需要创建一个 Dockerfile，包含一些如何创建镜像的指令。Dockerfile里面的每一条指令创建镜像的一层，只是有的层有tag而有的层是没有的。

1. Dockerfile 的基本语法
  * 使用 # 来注释
  * 基础镜像信息，FROM 指令告诉 Docker 使用哪个镜像作为基础镜像。
  * 维护者的信息，基本格式是`MAINTAINER docker_user docker_user@email.com`
  * 镜像操作指令，类似RUN之类的指令将对镜像执行跟随的命令。每运行一条RUN 指令，镜像添加新的一层，并提交。
  * 容器启动时的执行指令，类似CMD 指令，来指定运行容器时的操作命令。
编写完成 Dockerfile 后可以使用 docker build 来生成镜像可以看到 build 进程在执行操作。它要做的第一件事情就是上传这个 Dockerfile 内容， 因为所有的操作都要依据 Dockerfile 来进行。然后，Dockfile中的指令被一条一条的执行。每一步都创建了一个新的容器，在容器中执行指令并提交修改（就跟之前介绍过的docker commit一样）。当所有的指令都执行完毕之后，返回了最终的镜像id。所有的中间步骤所产生的容器都被删除和清理了。
<!-- more -->
2. 常用的指令
  1. EXPOSE，指定要暴露的端口号。格式为`EXPOSE <port> [<port>...]`表示容器对外暴露的端口号，可以在 Docker run 的时候指定宿主机的一个端口与该端口映射从而实现容器的端口暴露到外网中。
  2. ENV，格式为 `ENV <key> <value>`。指定一个环境变量，会被后 续 RUN 指令使用，并在容器运行时保持。
  3. ADD，格式为 `ADD <src> <dest>` 。该命令将复制指定的 <src> 到容器中的 <dest>。其中<src>可以是Dockerfile所在目录的一个相对路径；也可以是一个URL；还可以是一个 tar 文件（ 自动解压为目录）。
  4. COPY，格式为 `COPY <src> <dest>`。复制本地主机的 <src> （ 为 Dockerfile 所在目录的相 对路径）到容器中的<dest>。当使用本地目录为源目录时，推荐使用 COPY。
  5. ENTRYPOINT，有两种格式：
    * `ENTRYPOINT ["executable", "param1", "param2"]`
    * `ENTRYPOINT command param1 param2`（shell中执行）
  每个 Dockerfile 中只能有一个 ENTRYPOINT，当指定多个时，只有最后一个起效。
  6. VOLUME，格式为`VOLUME ["/data"]`创建一个可以从本地主机或其他容器挂载的挂载点,一般用来存放数据库和需要保持的数据等。
  7. ONBUILD，格式为`ONBUILD [INSTRUCTION]`。配置当所创建的镜像作为其它新创建镜像的基础镜像时， 所执行的操作指令。就是当前镜像被其他镜像FROM的时候会执行的指令。
3. 通过 Dockerfile 创建镜像。`sudo docker build -t myrepo/myapp /tmp/test1/`类似这样的命令可以创建镜像。-t 指定tag，后面的目录指定Dockerfile 的路径。
