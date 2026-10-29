# 什么是 Docker Compose？
## 讲解
如果你一直在跟着前面的教程学习，之前运行的都是单容器应用。但现在你想要做更复杂的事情：运行数据库、消息队列、缓存或者其他各类服务。是把所有组件都装在同一个容器里，还是启动多个容器？如果使用多个容器，又该怎么把它们连通起来？

容器领域有一条最佳实践：**一个容器只做一件事，并且做好这件事**。虽然这条规则存在特例，但尽量不要让一个容器承担多项职责。

你可以执行多条 `docker run` 命令来启动多个容器。但很快你就会发现，你需要手动管理网络、一堆用于容器联网的启动参数等等；项目结束后，清理资源也会比较麻烦。

借助 Docker Compose，你可以在单个 YAML 文件中定义所有容器及其配置。只要把这个文件提交到代码仓库，任何人拉取仓库代码后，只需要一条命令就能把整套服务跑起来。

需要重点理解：Compose 是**声明式工具**——你只需要定义好服务配置，直接启动即可。不需要每次从零重建全部资源。修改配置后，再次执行 `docker compose up`，Compose 会自动比对文件变更，智能地应用更新。

> **Dockerfile 和 Compose 文件的区别**
> Dockerfile 提供构建容器镜像的指令；而 Compose 文件用于定义正在运行的容器服务。很多时候 Compose 文件会引用 Dockerfile，为某个服务构建镜像。

## 动手实践
本次实操，你将学习使用 Docker Compose 运行多容器应用。我们会用一个简单的待办清单应用，后端基于 Node.js，数据库使用 MySQL。

### 启动应用
按照下面步骤在本机运行待办清单应用：
1. 下载并安装 Docker Desktop。
2. 打开终端，克隆这个示例项目：
```console
git clone https://github.com/dockersamples/todo-list-app
```
3. 进入 `todo-list-app` 目录：
```console
cd todo-list-app
```
目录里有一个 `compose.yaml` 文件，所有核心配置都写在这里！它定义了组成整套应用的全部服务以及对应配置。每个服务可以指定镜像、端口、数据卷、网络和其他运行所需参数。你可以打开这份 YAML 文件，熟悉它的结构。

4. 使用 `docker compose up` 命令启动应用：
```console
docker compose up -d --build
```
执行后输出类似下面这样：
```console
[+] Running 5/5
 ✔ app 3 layers ...      Pulled          7.1s
  ✔ e6f4e57cc59e Download complete                          0.9s
  ✔ df998480d81d Download complete                          1.0s
  ✔ 31e174fedd23 Download complete                          2.5s
  ✔ 43c47a581c29 Download complete                          2.0s
[+] Running 4/4
  ✔ Network todo-list-app_default           Created         0.3s
  ✔ Volume "todo-list-app_todo-mysql-data"  Created         0.3s
  ✔ Container todo-list-app-app-1           Started         0.3s
  ✔ Container todo-list-app-mysql-1         Started         0.3s
```
这里发生了很多事情，重点说明：
- 从 Docker Hub 拉取了两个镜像：node 和 MySQL
- 自动为应用创建专属网络
- 创建数据卷，保证容器重启后数据库数据不会丢失
- 启动两个容器并自动加载全部配置

如果觉得内容有点多不用紧张，慢慢熟悉就好！

5. 全部启动完成后，浏览器访问 http://localhost:3000 打开页面。注意：应用完全就绪需要 10–15 秒。页面打不开就稍等一下再刷新。你可以新增待办项、勾选完成或者删除条目。

6. 在 Docker Desktop 图形界面中，你可以看到正在运行的容器，查看详细配置。

### 关闭清理服务
这套应用是通过 Docker Compose 启动的，结束使用时一键清理非常方便。
1. 在命令行使用 `docker compose down` 移除所有资源：
```console
docker compose down
```
输出示例：
```console
[+] Running 3/3
 ✔ Container todo-list-app-mysql-1  Removed        2.9s
 ✔ Container todo-list-app-app-1    Removed        0.1s
 ✔ Network todo-list-app_default    Removed        0.1s
```
> **数据卷持久化说明**
> 默认情况下，执行 compose down **不会自动删除数据卷**。这样你再次启动整套服务的时候，原有数据还在。
> 如果想要同时删除数据卷，带上 `--volumes` 参数：
> ```console
> docker compose down --volumes
> [+] Running 1/0
> ✔ Volume todo-list-app_todo-mysql-data  Removed
> ```

2. 也可以在 Docker Desktop 界面，选中这个应用栈，点击 **Delete** 删除容器。
> 图形界面注意事项：在界面里删除 Compose 应用，只会删掉容器；网络和数据卷需要你手动清理。

本次实操，你学会了使用 Docker Compose 启动、关停一套多容器应用。

## 拓展阅读资源
本文只是 Compose 的入门介绍，下面文档可以深入学习 Compose 和 Compose 文件编写：
- Docker Compose 总览
- Docker Compose 命令行总览
- Compose 的工作原理
