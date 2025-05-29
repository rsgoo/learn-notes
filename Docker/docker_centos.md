#### 基于centos7.5 的Dockerfile 基础镜像

```dockerfile
FROM centos:centos7.5

RUN yum -y install which net-tools less psmisc

RUN set -x && \
    /usr/bin/cp -f /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
    mkdir -p /home/work && \
    useradd work -d /home/work && \
    chown -R work:work /home/work && \

ENV PATH=/opt/compiler/gcc-8.2.bin:$PATH

WORKDIR /home/work

USER work

```