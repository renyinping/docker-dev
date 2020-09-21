FROM debian:buster

ARG DOCKER_LANG=zh_CN
ARG DOCKER_TIMEZONE=Asia/Shanghai
ARG DOCKER_OTHERPACKAGES=" \
        vim wget curl"

ENV LANG=$DOCKER_LANG.UTF-8 \
    LANGUAGE=$DOCKER_LANG:UTF-8 \
    LC_ALL=$DOCKER_LANG.UTF-8

ARG DEBIAN_FRONTEND=noninteractive

WORKDIR /tmp
RUN mv /etc/apt/sources.list /etc/apt/sources.list-bak \
    && echo 'deb http://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free' >> /etc/apt/sources.list \
    && echo 'deb http://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free' >> /etc/apt/sources.list \
    && echo 'deb http://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free' >> /etc/apt/sources.list \
    && apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        apt-utils apt-file \
        locales locales-all \
    && echo 'LANG="zh_CN.UTF-8"' > /etc/default/locale \
    && echo 'LANGUAGE="zh_CN:zh"' >> /etc/default/locale \
    && locale-gen $LANG \
    && dpkg-reconfigure -f noninteractive locales \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        sudo openssh-server procps tini \
        $DOCKER_OTHERPACKAGES \
    && apt-get --purge autoremove \
    && apt-get autoclean \
    && apt-get clean \
    && mkdir -p /run/sshd \
    && sed -i 's/PermitRootLogin/#PermitRootLogin/g' /etc/ssh/sshd_config \
    && echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config \
    && echo 'root:root' | chpasswd \
    && useradd -s /bin/bash -G sudo -m a \
    && echo 'a:a' | chpasswd

VOLUME /data
WORKDIR /data
USER root
EXPOSE 22

ENTRYPOINT ["/usr/bin/tini", "--"]
CMD ["/usr/sbin/sshd", "-D"]

# docker build --rm --no-cache -t yinping/dev:buster .
# docker run --name=dev --hostname=dev -p 2222:22 -d yinping/dev:buster
