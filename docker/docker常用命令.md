#### 1.进入容器

```
docker exec -it <container_id> /bin/bash
```

#### 2.向仓库中推送镜像

**向仓库推送的时候,镜像的名称为用户名**

```
# docker login

# docker tag <existing-image> <hub-user>/<repo-name>[:<tag>]

# docker push
```
