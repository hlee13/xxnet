# xxnet

FROM centos

RUN set -ex; \ 
    yum install -y wget python3.8 sudo unzip

RUN wget https://ftp.tu-chemnitz.de/pub/linux/dag/redhat/el7/en/x86_64/rpmforge/RPMS/miredo-1.2.5-1.el7.rf.x86_64.rpm
RUN wget https://bootstrap.pypa.io/get-pip.py

RUN yum install -y miredo-1.2.5-1.el7.rf.x86_64.rpm

RUN mkdir -p /data/

RUN python3 get-pip.py
RUN pip install pyopenssl
RUN pip install supervisor

COPY 4.4.3 /data/xxnet.tar.gz


RUN cd /data/ && tar xf xxnet.tar.gz && mv XX* xxnet

COPY conf/xxnet/gae_proxy/config.json /data/xxnet/data/gae_proxy/config.json
COPY data/gae_proxy/CAkey.pem /data/xxnet/data/gae_proxy/CAkey.pem
COPY data/gae_proxy/CA.crt /data/xxnet/data/gae_proxy/CA.crt
COPY conf/xxnet/launcher/config.yaml /data/xxnet/data/launcher/config.yaml
COPY conf/xxnet/launcher/config.json /data/xxnet/data/launcher/config.json
COPY conf/xxnet/x_tunnel/client.json /data/xxnet/data/x_tunnel/client.json
COPY conf/supervisord/supervisord.conf /etc/supervisord.conf
# 
ENTRYPOINT ["supervisord", "--nodaemon", "--configuration", "/etc/supervisord.conf"]


安装完成后，COPY 证书到容器，避免每次重启都要重新信任证书
