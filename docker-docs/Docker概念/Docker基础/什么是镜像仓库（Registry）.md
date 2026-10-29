# 什么是镜像仓库（registry）？

## 讲解

现在你已经了解容器镜像是什么、它如何工作，你可能会有疑问：这些镜像存放在哪里？

你可以把容器镜像保存在自己电脑上，但如果你想分享给其他人，或者在另一台机器上使用该镜像该怎么办？这就是**镜像仓库（image registry）**的作用。

镜像仓库是集中存储、分发容器镜像的服务，可以是公开的，也可以是私有仓库。**Docker Hub** 是任何人都可以使用的公开镜像仓库，也是 Docker 默认的仓库。

Docker Hub 虽然很常用，但目前还有很多其他容器镜像仓库，包括亚马逊弹性容器仓库（Amazon Elastic Container Registry，ECR）、Azure容器仓库（Azure Container Registry，ACR）、谷歌容器仓库（Google Container Registry，GCR）。你甚至可以在本地环境或者企业内部搭建私有镜像仓库，例如 Harbor、JFrog Artifactory、GitLab 容器仓库等。

### 镜像仓库（Registry） vs 镜像仓库仓库（repository）

在使用镜像仓库的时候，你经常会看到 registry 和 repository，很多人以为二者是同一个东西。它们虽然有关联，但并不完全等同。

**registry**：集中存放、管理容器镜像的服务；
**repository**：registry 内部一组相关容器镜像的集合。你可以把它理解成一个文件夹，按项目来组织镜像。一个 repository 内包含一个或多个容器镜像。

下图展示 registry、repository、镜像三者的关系：

```
flowchart TB
  subgraph Registry[镜像仓库 Registry]
    subgraph A["仓库 A（Repository A）"]
      A1["project-a:v1.0"]
      A2["project-a:v2.0"]
    end
    subgraph B["仓库 B（Repository B）"]
      B1["project-b:v1.0"]
      B2["project-b:v1.1"]
      B3["project-b:v2.0"]
    end
  end
```

> 
> 💡提示
> Docker 个人版方案提供1个私有仓库，公开仓库数量不限。如果需要无限数量私有仓库，可以升级到 Docker 团队版。

## 动手实践

在本实操教程中，你将学习构建 Docker 镜像，并推送到 Docker Hub 的仓库中。

### 注册免费的 Docker 账号

1. 如果还没有账号，前往 Docker Hub 页面注册账号，并完成邮箱验证。
你可以直接使用谷歌账号或者 GitHub 账号登录认证。

### 创建你的第一个镜像仓库

1. 登录 Docker Hub
2. 点击右上角 **Create repository（创建仓库）**
3. 选择命名空间（一般就是你的用户名），仓库名称填写 `docker-quickstart`
4. 可见性设置为 **Public（公开）**
5. 点击 **Create（创建）**

完成！你已经成功创建第一个仓库🎉。
当前仓库是空的，接下来我们推送镜像进去。

### 在 Docker Desktop 中登录

1. 如果还没安装，下载并安装 Docker Desktop
2. 在 Docker Desktop 图形界面右上角点击 **Sign in（登录）**

### 拉取 Node.js 示例代码

想要构建镜像，首先要有项目。我们直接使用官方示例 Node.js 项目：github.com/dockersamples/helloworld-demo-node
这个仓库里已经写好了 Dockerfile，用来构建镜像。暂时不用深究 Dockerfile 的细节，后面章节会讲解。

1. 执行命令拉取代码：

```
git clone https://github.com/dockersamples/helloworld-demo-node
```

2. 进入项目目录

```
cd helloworld-demo-node
```

3. 构建镜像，把 `YOUR_DOCKER_USERNAME` 替换成你自己的 Docker 用户名

```
docker build -t <YOUR_DOCKER_USERNAME>/docker-quickstart .
```

> 
> ⚠️注意
> `docker build` 末尾的点 `.` 不能省略，它告诉 Docker去哪里寻找 Dockerfile

4. 查看刚刚构建好的镜像

```
docker images
```

输出示例：

```
REPOSITORY                                 TAG       IMAGE ID       CREATED         SIZE
<YOUR_DOCKER_USERNAME>/docker-quickstart   latest    476de364f70e   2 minutes ago   170MB
```

5. 启动容器测试镜像（替换成你的用户名）

```
docker run -d -p 8080:8080 <YOUR_DOCKER_USERNAME>/docker-quickstart
```

浏览器访问 [http://localhost:8080](http://localhost:8080) 验证服务是否正常。

6. 使用 `docker tag` 给镜像打标签。镜像标签用来标记版本

```
docker tag <YOUR_DOCKER_USERNAME>/docker-quickstart <YOUR_DOCKER_USERNAME>/docker-quickstart:1.0
```

7. 使用 `docker push` 将打好标签的镜像推送到 Docker Hub

```
docker push <YOUR_DOCKER_USERNAME>/docker-quickstart:1.0
```

8. 打开 Docker Hub，进入你的仓库页面，切换到 **Tags**，就能看到刚刚上传的镜像。

在本教程中，你完成了 Docker 账号注册、创建 Docker Hub 仓库，并且完成镜像构建、打标签、推送镜像。

## 拓展阅读资源

- Docker Hub 快速入门
- 管理 Docker Hub 仓库

