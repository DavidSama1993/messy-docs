# 理解镜像层

## 讲解

在《什么是镜像？》中我们学到：容器镜像由多层组成，并且镜像层一旦创建就不可变。但这到底意味着什么？这些层又是如何组合成容器可用的文件系统？

### 镜像层

镜像里的每一层都记录一组文件系统变更：新增、删除或者修改。我们来看一个假想镜像：

1. 第一层：添加基础命令与包管理器，例如 apt
2. 第二层：安装 Python 运行时和依赖管理工具 pip
3. 第三层：拷贝应用专属的 requirements.txt
4. 第四层：安装该应用的依赖包
5. 第五层：拷贝应用源码

这种分层设计的好处是：**镜像之间可以复用公共层**。举个例子，如果你再做一个 Python 应用，可以直接复用这套 Python 基础层。构建速度更快，分发镜像时占用的存储空间和网络带宽也更少。
你可以基于别人的镜像，复用它的底层，只添加自己应用需要的数据。

### 层的叠加

分层能力依赖**内容寻址存储**与**联合文件系统（union filesystem）**，原理如下：

1. 每一层下载完成后，会被解压到宿主机文件系统独立目录
2. 基于镜像启动容器时，联合文件系统会把多层堆叠在一起，对外呈现一份统一合并视图
3. 容器启动时，通过 `chroot` 将容器根目录切换到这个合并目录

联合文件系统创建时，除了镜像只读层之外，还会单独为运行中的容器创建一个读写目录。容器的文件修改都发生在这里，原始镜像层保持原样不受影响。所以同一个镜像可以同时启动多个容器。

## 动手实践

本实操使用 `docker container commit` 手动创建镜像层。实际开发中几乎不用这种方式，一般都是写 Dockerfile，但手动操作更容易理解底层原理。

### 创建基础镜像

1. 安装 Docker Desktop
2. 终端执行命令启动容器：

```
$ docker run --name=base-container -ti ubuntu
```

镜像拉取、容器启动后，会进入容器内部 shell，提示符类似：

```
root@d8c5ca119fcd:/#
```

3. 在容器内安装 Node.js：

```
$ apt update && apt install -y nodejs
```

这条命令会在容器内下载安装 Node；在联合文件系统模型里，这些改动都保属于这个容器专属的读写目录。
4. 验证 Node 是否安装成功：

```
$ node -e 'console.log("Hello world!")'
```

控制台输出 `Hello world!` 即正常。
5. 把当前容器改动保存为新镜像层，新开终端执行 commit：

```
$ docker container commit -m "Add node" base-container node-base
```

6. 使用 `docker image history` 查看镜像分层历史：

```
$ docker image history node-base
```

输出示例：

```
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
9e274734bb25   10 seconds ago   /bin/bash                                       157MB     Add node
cd1dba651b30   7 days ago       /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>      7 days ago       /bin/sh -c #(nop)  ADD file:6089c6bede9eca8ec…   110MB
<missing>      7 days ago       /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
<missing>      7 days ago       /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
<missing>      7 days ago       /bin/sh -c #(nop)  ARG LAUNCHPAD_BUILD_ARCH     0B
<missing>      7 days ago       /bin/sh -c #(nop)  ARG RELEASE                  0B
```

第一行注释 `Add node` 就是我们刚生成、包含 Node.js 的层。
7. 基于这个新镜像启动容器，验证 Node：

```
$ docker run node-base node -e "console.log('Hello again')"
```

输出 `Hello again`，代表镜像内 Node 可用。
8. 基础镜像制作完成，删除临时容器：

```
$ docker rm -f base-container
```

> 
> **基础镜像定义**
> 基础镜像是构建其他镜像的底座。任何镜像都可以作为基础镜像；但有一类镜像专门作为构建基座，给应用提供起点。
> 本例的 `node-base` 一般不会直接部署服务，它只是供后续构建使用的底座。

### 构建应用镜像

现在我们基于刚才的 node-base 镜像继续扩展出新镜像。

1. 用 node-base 启动新容器：

```
$ docker run --name=app-container -ti node-base
```

2. 在容器内创建 Node 脚本：

```
$ echo 'console.log("Hello from an app")' > app.js
```

运行脚本：

```
$ node app.js
```

3. 新开终端，把容器改动提交成镜像，同时设置默认启动命令：

```
$ docker container commit -c "CMD node app.js" -m "Add app" app-container sample-app
```

这条命令不仅生成 `sample-app` 镜像，还写入配置：容器启动时自动执行 `node app.js`。
4. 查看新镜像分层历史：

```
$ docker image history sample-app
```

输出示例：

```
IMAGE          CREATED              CREATED BY                                      SIZE      COMMENT
c1502e2ec875   About a minute ago   /bin/bash                                       33B       Add app
5310da79c50a   4 minutes ago        /bin/bash                                       126MB     Add node
2b7cc08dcdbb   5 weeks ago          /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>      5 weeks ago          /bin/sh -c #(nop)  ADD file:07cdbabf782942af0…   69.2MB
<missing>      5 weeks ago          /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
<missing>      5 weeks ago          /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
<missing>      5 weeks ago          /bin/sh -c #(nop)  ARG LAUNCHPAD_BUILD_ARCH     0B
<missing>      5 weeks ago          /bin/sh -c #(nop)  ARG RELEASE                  0B
```

顶层注释是 `Add app`，下一层就是之前的 `Add node`。
5. 直接启动新镜像（已经内置 CMD）：

```
$ docker run sample-app
```

终端打印脚本输出。
6. 清理临时容器：

```
$ docker rm -f app-container
```

## 拓展资源

- `docker image history`：查看镜像分层历史
- `docker container commit`：把容器改动提交为新镜像
