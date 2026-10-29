# 什么是镜像？

## 讲解

我们已经知道容器是一个隔离进程，那它的文件和配置从哪里来？又该如何共享这套运行环境？

这就要用到容器镜像。**容器镜像**是一种标准化软件包，包含运行容器所需的全部文件、可执行程序、依赖库以及配置文件。

以 PostgreSQL 镜像为例：镜像里打包了数据库可执行程序、配置文件以及其他依赖项。如果是 Python Web 应用镜像，则会包含 Python 运行时、业务代码和全部依赖包。

镜像有两条重要特性：

1. **镜像不可变（immutable）**：镜像一旦构建完成，就不能被修改。你只能基于它构建新镜像，或者在它之上叠加变更层。
2. **容器镜像由多层（layers）组成**：每一层代表一组文件系统变更，用来新增、删除或者修改文件。

基于这两个特性，我们可以在已有镜像基础上做扩展。比如开发 Python 应用时，可以直接以 Python 基础镜像为起点，再新增分层来安装项目依赖、拷贝业务代码。这样你只需要专注应用本身，不用再去搭建 Python 环境。

### 查找镜像

Docker Hub 是默认的全球镜像仓库，用于存储和分发镜像。上面有超过十万个开发者制作的镜像，可以拉到本地直接运行。你可以在 Docker Hub 检索镜像，并且直接在 Docker Desktop 里使用。

Docker Hub 提供多种由 Docker 维护、背书的可信镜像内容，叫做 **Docker Trusted Content**。这类镜像适合直接搭建托管服务，或者作为自制镜像的起点，包含以下分类：

- **Docker Official Images（Docker 官方镜像）**：精选仓库集合，绝大多数开发者的起点，也是 Docker Hub 里安全性较高的镜像
- **Docker Hardened Images（Docker 加固镜像）**：精简、安全、可直接投入生产，几乎没有通用漏洞（CVE）；旨在缩小攻击面、简化合规流程，基于 Apache 2.0 协议开源免费
- **Docker Verified Publishers（Docker 认证发布方镜像）**：由商业厂商发布、经过 Docker 核验的高质量镜像
- **Docker-Sponsored Open Source（Docker 赞助开源镜像）**：由 Docker 开源项目计划资助的开源项目发布和维护的镜像

例如 Redis、Memcached 都是热门、开箱即用的 Docker 官方镜像。下载镜像后，短短几秒就能启动对应的服务。还有一类基础镜像，比如 Node.js 镜像，可以作为基底，在上面添加自己的文件和配置。对于对安全要求高的生产环境，Docker 加固镜像提供 Node.js、Python、Go 等热门镜像的精简安全版本。

## 动手实操

### 使用图形界面（GUI）

在这个实操中，你将学习在 Docker Desktop 图形界面搜索、拉取容器镜像。

#### 搜索并下载镜像

1. 打开 Docker Desktop 仪表盘，在左侧菜单栏选中 **Images（镜像）** 视图。
2. 点击 **Search images to run（搜索可运行镜像）** 按钮；如果找不到该按钮，使用页面顶部全局搜索框。
3. 在搜索框输入 `welcome-to-docker`，搜索完成后选中 `docker/welcome-to-docker` 镜像。
4. 点击 **Pull（拉取）** 下载镜像。

#### 查看镜像详情

镜像下载完成后，可以通过图形界面或者命令行查看镜像详细信息：

1. 在 Docker Desktop 仪表盘打开 **Images** 页面。
2. 选中 `docker/welcome-to-docker` 镜像，进入详情页。
3. 镜像详情页会展示镜像分层、预装的软件包和依赖库，以及扫描发现的安全漏洞。

---

### 使用命令行（CLI）

下面通过命令行搜索、拉取 Docker 镜像，并查看分层信息。

#### 搜索并下载镜像

1. 打开终端，使用 `docker search` 命令搜索镜像：

```
docker search docker/welcome-to-docker
```

输出示例：

```
NAME                       DESCRIPTION                                     STARS     OFFICIAL
docker/welcome-to-docker   Docker image for new users getting started w…   20
```

该输出展示 Docker Hub 上匹配的镜像信息。

2. 使用 `docker pull` 拉取镜像：

```
docker pull docker/welcome-to-docker
```

输出示例：

```
Using default tag: latest
latest: Pulling from docker/welcome-to-docker
579b34f0a95b: Download complete
d11a451e6399: Download complete
1c2214f9937c: Download complete
b42a2f288f4d: Download complete
54b19e12c655: Download complete
1fb28e078240: Download complete
94be7e780731: Download complete
89578ce72c35: Download complete
Digest: sha256:eedaff45e3c78538087bdd9dc7afafac7e110061bbdd836af4104b10f10ab693
Status: Downloaded newer image for docker/welcome-to-docker:latest
docker.io/docker/welcome-to-docker:latest
```

每一行代表一个正在下载的镜像分层。记住：每一层都是一组文件系统变更，用来提供镜像对应的功能。

#### 查看镜像信息

1. 使用 `docker image ls` 列出本地已下载镜像：

```
docker image ls
```

输出示例：

```
REPOSITORY                 TAG       IMAGE ID       CREATED        SIZE
docker/welcome-to-docker   latest    eedaff45e3c7   4 months ago   29.7MB
```

这条命令列出本机上所有 Docker 镜像；`docker/welcome-to-docker` 镜像大小约 29.7MB。

> 
> **镜像大小说明**
> 这里显示的是镜像解压后的大小，不是分层下载时的压缩包大小。

2. 使用 `docker image history` 查看镜像分层历史：

```
docker image history docker/welcome-to-docker
```

输出示例：

```
IMAGE          CREATED        CREATED BY                                      SIZE      COMMENT
648f93a1ba7d   4 months ago   COPY /app/build /usr/share/nginx/html # buil…   1.6MB     buildkit.dockerfile.v0
<missing>      5 months ago   /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B
<missing>      5 months ago   /bin/sh -c #(nop)  STOPSIGNAL SIGQUIT           0B
<missing>      5 months ago   /bin/sh -c #(nop)  EXPOSE 80                    0B
<missing>      5 months ago   /bin/sh -c #(nop)  ENTRYPOINT ["/docker-entr…   0B
<missing>      5 months ago   /bin/sh -c #(nop) COPY file:9e3b2b63db9f8fc7…   4.62kB
<missing>      5 months ago   /bin/sh -c #(nop) COPY file:57846632accc8975…   3.02kB
<missing>      5 months ago   /bin/sh -c #(nop) COPY file:3b1b9915b7dd898a…   298B
<missing>      5 months ago   /bin/sh -c #(nop) COPY file:caec368f5a54f70a…   2.12kB
<missing>      5 months ago   /bin/sh -c #(nop) COPY file:01e75c6dd0ce317d…   1.62kB
<missing>      5 months ago   /bin/sh -c set -x     && addgroup -g 101 -S …   9.7MB
<missing>      5 months ago   /bin/sh -c #(nop)  ENV PKG_RELEASE=1            0B
<missing>      5 months ago   /bin/sh -c #(nop)  ENV NGINX_VERSION=1.25.3     0B
<missing>      5 months ago   /bin/sh -c #(nop)  LABEL maintainer=NGINX Do…   0B
<missing>      5 months ago   /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B
<missing>      5 months ago   /bin/sh -c #(nop) ADD file:ff3112828967e8004…   7.66MB
```

输出展示全部分层、每层大小，以及构建该分层使用的命令。

> 
> **查看完整命令**
> 给命令加上 `--no-trunc` 参数，就能看到完整命令。注意：输出是表格格式，命令太长时阅读会很不方便。

在本教程中，你完成了镜像检索与拉取，同时了解了 Docker 镜像分层的概念。

## 拓展资源

- [Docker 可信内容](https://docs.docker.com/docker-hub/image-library/trusted-content/)
- [Docker Desktop 镜像视图介绍](https://docs.docker.com/desktop/use-desktop/images/)
- [Docker Build 概览](https://docs.docker.com/build/concepts/overview/)
- [Docker Hub](https://hub.docker.com)
