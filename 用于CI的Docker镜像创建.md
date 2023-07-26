## 注意

```bash
cd /a/path && tar -xf /other/path/xxx.tar.xz # 会找不到解压出来的文件
```

**TODO: Docker使用基于Ubuntu构建的CI镜像启动容器时，默认的PATH环境变量似乎无法自由指定。多种方式尝试下，PATH仍无法改变**

## 构建Docker镜像

```dockerfile
FROM ubuntu:22.04

COPY ./sources.http.list /etc/apt/sources.list
COPY ./node-v18.16.1-linux-x64.tar.xz /home/node/node-v18.16.1-linux-x64.tar.xz
COPY ./id_rsa /root/.ssh/id_rsa

RUN apt update
RUN apt install xz-utils -y
RUN apt install openssh-client -y    
RUN apt install vim -y    

# 安装nodejs
RUN tar -xf /home/node/node-v18.16.1-linux-x64.tar.xz -C /home/node/
RUN PATH=/home/node/node-v18.16.1-linux-x64/bin:$PATH && \
    echo "PATH=$PATH" >> /etc/profile && \
    # npm config set registry https://registry.npmmirror.com/ && \
    npm config set loglevel http

# 安装openssh
RUN eval $(ssh-agent -s) && \
    ssh-add ~/.ssh/id_rsa && \
    echo "Host *\\n\\tStrictHostKeyChecking no\\n" > ~/.ssh/config && \
    chmod -R 700 ~/.ssh
```

根据`Dockerfile`构建docker镜像
```bash
$ sudo docker build -t ci_env ./ # ./ 为Dockerfile存在的文件夹路径
```

检查构建出来的镜像
```bash
$ sudo docker run -it ci_env bash
```

## gitlab-runner中注册、使用构建出来的Docker镜像

[docker中gitlab-runner注册](https://docs.gitlab.com/runner/register/index.html#docker)

```toml
# config.toml
[[runners]]
  name = "branch-system-back-end"
  url = "https://git.dcyun.com/"
  token = "YzDfixP696idE4et6RHj" # gitlab-runner register时自动创建
  tls-ca-file = "/etc/gitlab-runner/certs/git.dcyun.com.crt" # 网站https的ca证书
  executor = "docker"
  pre_clone_script = "    git config --global http.sslverify false\n  " # git拉取代码时不验证ssl
  [runners.custom_build_dir]
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
  [runners.docker] # https://docs.gitlab.com/runner/configuration/advanced-configuration.html#the-runnersdocker-section
    tls_verify = false
    image = "ci_env:latest" # CI构建时使用的环境docker镜像
    pull_policy = "if-not-present" # 优先使用本地镜像，当本地找不到指定的镜像时，再去镜像源拉取
    privileged = false
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 0
```

[**What does unsafe-perm in npm actually do?**](https://geedew.com/What-does-unsafe-perm-in-npm-actually-do/)