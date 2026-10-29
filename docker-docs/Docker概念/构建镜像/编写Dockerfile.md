# 编写 Dockerfile

## 讲解

Dockerfile 是一份文本文件，用于构建容器镜像。它向镜像构建器提供一系列指令：要执行哪些命令、拷贝哪些文件、设置启动命令等等。

下面这份 Dockerfile 示例，可以构建出一个可直接运行的 Python 应用镜像：

```
FROM python:3.13
WORKDIR /usr/local/app

# 安装应用依赖
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

# 拷贝源码
COPY src ./src
EXPOSE 8080

# 创建应用用户，避免容器以 root 用户运行
RUN useradd app
USER app

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8080"]
```

### 常用指令

Dockerfile 里最常用的指令：

- `FROM <image>`：指定基础镜像，本次构建基于该镜像扩展
- `WORKDIR <path>`：指定工作目录，后续文件拷贝、命令执行都在此目录下
- `COPY <宿主机路径> <镜像内路径>`：告诉构建器，把宿主机文件拷贝到容器镜像中
- `RUN <command>`：告诉构建器执行指定命令
- `ENV <name> <value>`：设置环境变量，容器运行时可以读取
- `EXPOSE <port-number>`：在镜像上声明想要对外开放的端口（仅声明，不会实际开放端口）
- `USER <user-or-uid>`：设置后续所有指令默认使用的执行用户
- `CMD ["<command>", "<arg1>"]`：设置使用该镜像启动容器时默认执行的命令

想要查看全部指令和详细说明，可以查阅 [Dockerfile 官方参考文档](https://docs.docker.com/engine/reference/builder/)。

## 动手实践

就像前面的例子，Dockerfile 的编写流程一般是：

1. 选定基础镜像
2. 安装应用依赖
3. 拷贝源码/二进制程序
4. 配置镜像运行参数

本次实操，我们编写 Dockerfile 构建一个简单 Node.js 应用。不熟悉 JS 也没关系，不影响跟着操作。

### 准备工作

下载压缩包：[https://github.com/docker/getting-started-todo-app/archive/refs/heads/build-image-from-scratch.zip，解压到本地目录。](https://github.com/docker/getting-started-todo-app/archive/refs/heads/build-image-from-scratch.zip%EF%BC%8C%E8%A7%A3%E5%8E%8B%E5%88%B0%E6%9C%AC%E5%9C%B0%E7%9B%AE%E5%BD%95%E3%80%82)

不想下载压缩包的话，也可以克隆项目仓库，切换到 `build-image-from-scratch` 分支：
[https://github.com/docker/getting-started-todo-app](https://github.com/docker/getting-started-todo-app)

### 创建 Dockerfile

拿到项目后，开始编写 Dockerfile：

1. 安装 Docker Desktop
2. 查看项目内容
进入 `getting-started-todo-app/app/`，里面已经自带一份 Dockerfile，可以用任意代码编辑器打开。
3. 删除原有 Dockerfile
本次练习从零开始，删掉它，自己新建。
4. 在 `getting-started-todo-app/app/` 文件夹新建文件，命名为 `Dockerfile`

> 
> **Dockerfile 扩展名说明**
> Dockerfile **没有后缀名**。部分编辑器会自动给文件加上后缀，或者提示缺少后缀，需要留意。
5. 在 Dockerfile 中指定基础镜像：

```
FROM node:22-alpine
```

6. 使用 `WORKDIR` 设置工作目录，后续命令、文件拷贝都以这个目录为准：

```
WORKDIR /app
```

7. 使用 `COPY`，把本机项目里所有文件拷贝进镜像：

```
COPY . .
```

8. 使用 `RUN` 执行 yarn，安装生产依赖：

```
RUN yarn install --production
```

9. 最后用 `CMD` 设置容器默认启动命令：

```
CMD ["node", "./src/index.js"]
```

最终完整 Dockerfile：

```
FROM node:22-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "./src/index.js"]
```

> 
> ⚠️ 这份 Dockerfile 还不能直接上生产
> 这份示例是简化版本，并没有遵循所有最佳实践。虽然可以成功构建应用，但构建缓存利用、镜像安全性都还有优化空间。
> 后面你会学到：如何充分利用构建缓存、使用非 root 用户运行、多阶段构建。

## 拓展资源

- [Dockerfile 指令参考](/reference/dockerfile/)
- [Dockerfile 最佳实践](/develop/develop-images/dockerfile_best-practices/)
- [基础镜像介绍](/build/building/base-images/)
- Gordon：Docker 的 AI 助手，可以帮项目生成 Dockerfile，自动适配你的语言和框架

