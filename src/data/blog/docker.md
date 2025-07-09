---
author: 'Lanxinmob'
title: '暑期日记1'
postSlug: 'summer-diary-1'
featured: false
draft: false
tags:
  - '笔记'
  - 'wsl'
  - 'docker'
ogImage: ''
description: '跟着docker desktop的引导一步步学了点'
pubDatetime: 2025-07-06T09:00:00Z
toc: true
---

## wsl
- `wsl -d Ubuntu-22.04`
- `exit`

#### Linux的ext4和 Windows的NTFS
- 这两个文件系统的底层结构完全不同。
- 权限管理上，
  - ext4 是经典的UNIX权限模型，**所有者**，**用户组**，**其他**分别对应**读**，**写**，**执行**的权限。
  - NTFS 是基于**访问控制列表（ACL）**的权限模型，可以为任意数量的用户和组设置精细具体的权限。
- ext4 大小写敏感，NTFS 不大小写敏感但保留大小写。
- NTFS 内置文件压缩、加密等高级功能，而ext4 高效稳定适合处理大量小文件

## docker

- 学习 **Docker** 的最好方法就是 `learn in action`

- `git clone [url]`

- `docker build -t name / docker compose up -d`

- `docker start container-name`

   - `docker stop/restart/rm container-name`
   - `docker ps -a` 查看所有容器
   - `docker compose stop/down` 结束/删除容器
   
- `localhost:8089:3000`  
  - 8089是主机端口 3000是容器端口
  - 外部请求发往主机端口，docker将请求转发到容器的端口
  
- 使用 `docker compose watch` 这个命令可以启动“监视模式”，在修改代码后，让正在运行的服务自动更新，以便能实时预览到更改的效果 。

- 将配置存在**Compose**文件，可以轻松删除所有内容并重新启动。

  - 再次想启动时只要使用 `docker compose up` 命令，就可以重新启动。

  - 但是要注意之前的数据都会丢失，因为数据存储在容器内部，删除容器也就删除了其中的数据。

### Persist data

- 那么有什么方法可以保持数据吗

- docker可以使用数据卷(**volume**)来保存数据，数据卷是本地文件系统的一个位置，由docker负责管理。

    

   ```yaml
   volumes:
     volume-name:
   
     to-do-database:
       volumes: 
         - volume-name:/data/db
         #容器中的挂载路径 这里不在乎数据卷在本地的路径
   ```

   

- 创建并命名数据卷，然后挂载到容器内目录

  - `docker volume ls` 列出所有数据卷
  - `docker volume inspect volume-name` 查看详细信息
  - `docker volume rm volume-name` 删除数据卷
  - `docker volume prune` 删除所有未使用过的数据卷
  
### Access your local folder from a container

- 那么能不能通过容器访问本地文件系统中的数据呢

- 添加绑定挂载(**bind mount**)，将本地一个文件夹与docker共享

  ```yaml
  do-app:
      # ...
      volumes:
        - ./app:/usr/src/app
        #容器中路径
        - /usr/src/app/node_modules
        #防止绑定挂载覆盖容器的该目录
  ```
  
- `docker compose up -d` 后台模式启动，不占用终端，不会实时显示日志，区别于主要用于开发和调试的前台模式。

### Containerize the application

- 使用容器时需要使用一个 **Dockerfile** 文件来定义镜像，一个**compose.yaml**文件来定义如何运行镜像。
- 在项目文件夹中运行 `docker init` 的命令，**Docker** 会创建所有的必需文件。

- [Dockerfile reference⁠](https://docs.docker.com/engine/reference/builder/) and [Compose file reference⁠](https://docs.docker.com/compose/compose-file/)



