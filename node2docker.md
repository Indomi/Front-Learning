# **Node项目docker化**

## **1.`npm install`安装依赖**

- npm版本大于等于5，会自动生成`package-lock.json`文件，一起被拷贝进docker镜像中

## **2.创建`Dockerfile`文件**

- 定义基础镜像

```text
FROM node:8
```

- 在镜像中创建一个文件夹存放应用程序代码，即应用程序的工作目录

```text
#Create app directory
WORKDIR /usr/src/app
```

- 下一步使用npm安装依赖，使用通配符匹配两个package

```bash
COPY package*.json ./

RUN npm install
# If you are building your code for production
# RUN npm install --only=production
```

- 在docker镜像中使用`COPY`命令绑定应用程序

```text
COPY . .
```

- 用`EXPOSE`命令与docker镜像做映射

```text
EXPOSE 8080
```

- 定义运行时的cmd命令来运行应用程序

```text
CMD [ "npm", "start" ]
```

## **3.创建`.dockerignore`文件**

> 在构建镜像时，docker会获取所有位于`context`目录下的文件。为了增加docker构建的速度，可以在context目录中添加`.dockerignore`文件来排除不需要的文件与目录

- 避免本地模块和日志被拷贝入镜像中

```text
.git
node_modules
npm-debug.log
```

## **4.构建镜像**

- 进入Dockerfile所在文件目录，运行

```bash
docker build -t <ImageName> .
```

> 注意后面还有一个点

- 查看构建的镜像

```bash
docker images
```

## **5.运行镜像**

- 使用`-d`模式运行镜像将以分离模式运行Docker容器，使得容器在后台自助运行。开关符`-p`在容器中把一个公共端口映射到私有的端口

```bash
docker run -p 49160:8080 -d <ImageName>
```

- 打印应用程序的输出

```bash
# Get container ID
docker ps

# Print app output
docker logs <container Id>

# Example
Running on http://localhost:8080
```

- 如果要进入容器中，运行`exec`命令

```bash
# Enter the container
docker exec -it <container id> /bin/bash
```

## **6.测试**

- 测试应用程序连接

```bash
docker ps

# Example
ID          |IMAGE
ecce33Bjlask|<ImageName>
```

```bash
curl -i localhost:49160

HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: text/html; charset=utf-8
Content-Length: 12
ETag: W/"c-M6tWOb/Y57lesdjQuHeB1P/qTV0"
Date: Mon, 13 Nov 2017 20:53:59 GMT
Connection: keep-alive

Hello world
```

## **7.使用Nodemon实现热更新**

> [nodemon](https://www.npmjs.com/package/nodemon)是一款很受欢迎的包，它在运行时会监视目录中的文件，当任何文件发生了改变时，nodemon将会自动重启你的node应用

```bash
FROM node:8

# 创建 app 目录
WORKDIR /app

# 安装 nodemon 以实现热更新
RUN npm install -g nodemon

# 安装 app 依赖
# 使用通配符确保 package.json 与 package-lock.json 复制到需要的地方。（npm 版本 5 以上）
COPY package*.json ./

RUN npm install

# 打包 app 源码
COPY src /app

EXPOSE 8080
CMD [ "nodemon", "server.js" ]
```

## **8.优化**

### **8.1 使用COPY来代替ADD**

- 在`Dockerfile`中，除非需要自动解压 tar 文件，否则最好使用 COPY 来代替 ADD。

### **8.2 使用node原生命令**

```bash
#推荐使用
CMD [ "node", "app.js" ]

#来代替
CMD [ "npm", "app.js" ]
```

- 绕过`package.json`的`start`命令，这样可以减少在容器中运行的进程数量，同时还能让Node.js进程接收到`SIGTERM`与`SIGINT`等退出信号，如果是npm则会无视这些信号

### **8.3 使用`--init`标志**

- 用[tini](https://github.com/krallin/tini)轻量集初始化系统来包装进Node.js进程，它们也能响应一些`SIGTERM`(`CTRL-C`)之类的内核信号，例如：

```bash
docker run -it --init -p 8080:8080 -v $(pwd):/app \
            keel-docker-dev bash
```
