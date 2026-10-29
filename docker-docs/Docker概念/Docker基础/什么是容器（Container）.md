# 什么是容器？

## 讲解

设想你正在开发一个很棒的 Web 应用，它包含三个主要组件：React 前端、Python API 和 PostgreSQL 数据库。如果你要开发这个项目，就得安装 Node、Python 和 PostgreSQL。

怎么保证你使用的版本，和团队里其他开发者、CI/CD 流程、线上生产环境保持一致？

如何确保应用所需的 Python（或 Node、数据库）版本，不受本机已安装软件的影响？该怎么处理潜在的版本冲突？

容器就此登场！

什么是容器？简单来说，容器是为应用各个组件创建的**隔离进程**。每个组件——React 前端、Python API 服务、数据库，都运行在独立隔离环境中，和你机器上其他程序完全隔开。

容器的优势：

- **自包含（Self-contained）**：每个容器自带运行所需一切依赖，不依赖宿主机预先安装的软件。
- **隔离（Isolated）**：容器彼此隔离运行，对宿主机和其他容器影响极小，提升应用安全性。
- **独立（Independent）**：容器可单独管理。删除一个容器，不会影响其他容器。
- **可移植（Portable）**：随处可运行！在开发机上正常运行的容器，在数据中心、云端都能以完全相同的方式运行。

### 容器 vs 虚拟机（VM）

不深入细节来讲：虚拟机是一套完整操作系统，自带独立内核、硬件驱动、程序与应用。只为隔离一个应用就启动一台虚拟机，资源开销很大。

而容器仅仅是一个**隔离进程**，附带它运行需要的全部文件。多个容器共用宿主机内核，因此可以用更少基础设施跑更多应用。

> 
> **虚拟机 + 容器协同使用**
> 
> 
> 容器和虚拟机经常搭配使用。举个例子，云环境里分配出来的机器一般是虚拟机。但不再是一台虚拟机只跑一个应用，装有容器运行时的虚拟机可以运行多个容器化应用，提升资源利用率、节约成本。

## 动手实操

在本实操环节，你将学习通过 Docker Desktop 图形界面运行 Docker 容器。

### 使用图形界面

按照下面步骤运行容器：

1. 打开 Docker Desktop，点击顶部导航栏的**搜索（Search）**输入框。
2. 在搜索框输入 `welcome-to-docker`，点击 **拉取（Pull）** 按钮。
3. 镜像拉取成功后，点击 **运行（Run）**。
4. 展开**可选设置（Optional settings）**。
5. **容器名称（Container name）**填写：`welcome-to-docker`。
6. **宿主机端口（Host port）**填写：`8080`。
7. 点击 **Run** 启动容器。

恭喜！你已经成功运行第一个容器 🎉

### 查看容器

进入 Docker Desktop 仪表盘的 **Containers（容器）**页面，就能看到全部容器。

这个容器内部运行了一台 Web 服务，展示一个简单网页。复杂项目里，我们会把不同模块放到不同容器：前端、后端、数据库各自独立容器。

### 访问前端页面

启动容器时，我们把容器内部端口映射到本机。可以理解为配置一条通路，让我们能够连接到隔离的容器环境。

本容器的前端服务通过 `8080` 端口访问。你可以点击容器条目【端口（Port(s)）】列的链接，或者浏览器打开 `http://localhost:8080`。

### 探索容器内部

Docker Desktop 支持查看、操作容器内部内容，你可以自行体验：

1. 进入仪表盘【Containers】页面
2. 选中刚才创建的容器
3. 切换到【Files】标签页，浏览容器隔离的文件系统

### 停止容器

`docker/welcome-to-docker` 容器会持续运行，直到手动停止：

1. 进入【Containers】页面
2. 找到目标容器
3. 在【Actions（操作）】列选择 **Stop（停止）**

---

### 使用命令行（CLI）

通过命令行运行容器步骤：

1. 打开终端，执行 `docker run` 命令启动容器：

```
$ docker run -d -p 8080:80 docker/welcome-to-docker
```

命令输出的一长串字符就是完整容器 ID。

恭喜！你启动了第一个容器 🎉

#### 查看正在运行的容器

用 `docker ps` 确认容器是否正常运行：

```
docker ps
```

输出示例：

```
 CONTAINER ID   IMAGE                      COMMAND                  CREATED          STATUS          PORTS                      NAMES
 a1f7a4bb3a27   docker/welcome-to-docker   "/docker-entrypoint…"   11 seconds ago   Up 11 seconds   0.0.0.0:8080->80/tcp       gracious_keldysh
```

> 
> 💡提示
> `docker ps` **只展示正在运行的容器**。想查看已经停止的容器，增加 `-a` 参数：`docker ps -a`

#### 访问前端页面

和图形界面方式一致，访问 `http://localhost:8080`

#### 停止容器

1. 先用 `docker ps` 获取容器 ID
2. 将容器 ID / 容器名称传给 `docker stop`

```
docker stop <the-container-id>
```

> 
> 💡提示：使用 ID 时不需要输入完整ID，输入足够区分其他容器的前缀即可。
> 示例：上面这个容器可以直接执行
> 
> 
> ```
> docker stop a1f
> ```

## 拓展资源

- [运行容器](https://docs.docker.com/engine/containers/run/)
- [容器概览](https://www.docker.com/resources/what-container/)
- [为什么选择 Docker？](https://www.docker.com/why-docker/)
