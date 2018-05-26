# Consul
Consul検証


- dockerでConsul検証環境を用意する
```
$ docker pull progrium/consul
$ docker run -d --name node1 -h node1 progrium/consul -server -bootstrap-expect 3
$ JOIN_IP="$(docker inspect -f '{{ .NetworkSettings.IPAddress }}' node1)"
$ docker run -d --name node2 -h node2 progrium/consul -server -join $JOIN_IP
$ docker run -d --name node3 -h node3 progrium/consul -server -join $JOIN_IP
```

- サーバ同士は接続できているが、外部から見ることができないので4台目はサーバではなくクライアントとして立ち上げる
```
$ docker run -d -p 8400:8400 -p 8500:8500 -p 8600:53/udp -h node4 progrium/consul -join $JOIN_IP
```
--> 8400、8500番ポートを使ってConsulの状態を見られるようになった

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                                                                                        NAMES
63d31f9177d8        progrium/consul     "/bin/start -join 17…"   8 minutes ago       Up 8 minutes        53/tcp, 0.0.0.0:8400->8400/tcp, 8300-8302/tcp, 8301-8302/udp, 0.0.0.0:8500->8500/tcp, 0.0.0.0:8600->53/udp   clever_proskuriakova
5fe73e309550        progrium/consul     "/bin/start -server …"   9 minutes ago       Up 9 minutes        53/tcp, 53/udp, 8300-8302/tcp, 8400/tcp, 8500/tcp, 8301-8302/udp                                             node3
83254c65228f        progrium/consul     "/bin/start -server …"   9 minutes ago       Up 9 minutes        53/tcp, 53/udp, 8300-8302/tcp, 8400/tcp, 8500/tcp, 8301-8302/udp                                             node2
d756cb40036b        progrium/consul     "/bin/start -server …"   10 minutes ago      Up 10 minutes       53/tcp, 53/udp, 8300-8302/tcp, 8400/tcp, 8500/tcp, 8301-8302/udp
```



- consulをインストール@Mac
  - `$ brew cask install consul-cli`

- `$ consul members`                                                         
```
Node   Address          Status  Type    Build  Protocol  DC   Segment
node1  172.17.0.2:8301  alive   server  0.5.2  2         dc1  <all>
node2  172.17.0.3:8301  alive   server  0.5.2  2         dc1  <all>
node3  172.17.0.4:8301  alive   server  0.5.2  2         dc1  <all>
node4  172.17.0.5:8301  alive   client  0.5.2  2         dc1  <default>
```

- 管理画面接続
  - http://localhost:8500
