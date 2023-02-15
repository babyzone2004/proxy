# 虚拟机&k8s 搭建 ss

## 配置防火墙&VPC

1. 来源过滤条件：0.0.0.0/0

2. 协议和端口：tcp:8399,udp:8399

3. Vpc-外部 IP 地址-预留。保存为静态 ip 地址

## 配置 vps

1. 安装 ss 服务端：https://shadowsocks.org/guide/deploying.html#c-with-libev。

   1. 或者直接 docker 镜像，https://github.com/shadowsocks/shadowsocks-libev/blob/master/docker/alpine/README.md

2. 启动：https://github.com/shadowsocks/shadowsocks-libev#usage

   1. **[docker Mac](https://github.com/shadowsocks/shadowsocks-libev/blob/master/docker/alpine/README.md)：**`docker run -e PASSWORD=123123123 -p 8388:8388 -p 8388:8388/udp -d --restart always shadowsocks/shadowsocks-libev`

   2. **ubuntu:** docker run -e PASSWORD=123123123 -p 8388:8388 -p 8388:8388/udp -it ss:v1 /bin/bash

   3. **ubuntu: **进入系统，内部配置：sudo vim /etc/shadowsocks-libev/config.json

      ```shell
      {
        "server":["::1", "0.0.0.0"],
        "mode":"tcp_and_udp",
        "server_port":8399,
        "local_port":1080,
        "password":"123123",
        "timeout":86400,
        "method":"aes-256-gcm"
      }
      ```

3. 提升性能：https://shadowsocks.org/guide/advanced.html

4. 后台启动 nohup ss-server &

## 配置开机启动

1. 新建 start.sh，sudo chmod 777start.sh

```shell
#!/bin/bash
sudo nohup ss-server &
exit 0
```

/etc/systemd/system,新建 startSS.service

```shell
[Unit]
Description=Startup SS

[Service]
ExecStart=/home/ywxzone2004/start.sh

[Install]
WantedBy=multi-user.target
```

sudo chmod 777 startSS.service

```shell
# 手动运行 startSS.service
sudo systemctl start startSS.service
# 查看运行日志
systemctl status startSS.service
```

需配置预留 IP 地址，避免重启 IP 地址变化

## goggle clound

1. 安装 qcloud
2. gcloud auth configure-docker asia-east2-docker.pkg.dev
3. docker push asia-east2-docker.pkg.dev/coral-velocity-xxx/xxx/xxx:v1

## AWS

1. 配置网络：https://docs.aws.amazon.com/zh_cn/vpc/latest/userguide/VPC_Internet_Gateway.html
   1. 出入流量配置，公共 IP，子路由，互联网网关等
   2. 可以直接用 docker hub
2. 创建 fargate 集群
3. 创建任务
4. 示例：https://docs.aws.amazon.com/zh_cn/AmazonECS/latest/developerguide/getting-started-fargate-classic-console.html

## GKE

```shell
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl apply -f service.yaml
kubectl get services
```
