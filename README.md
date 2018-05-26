# Consul
Consul検証

とりあえずメモ
```
$ docker pull progrium/consul
少し時間がかかりますが、終わったらまず1つ目のノードを立ち上げます。

$ docker run -d --name node1 -h node1 progrium/consul -server -bootstrap-expect 3
2台目のノードからは、この1台目のノードに接続する形になりますので、IPアドレスを取得します。

$ JOIN_IP="$(docker inspect -f '{{ .NetworkSettings.IPAddress }}' node1)"
これで $JOIN_IP にIPアドレスが入ります。では続けてノードを立ち上げます。

$ docker run -d --name node2 -h node2 progrium/consul -server -join $JOIN_IP
$ docker run -d --name node3 -h node3 progrium/consul -server -join $JOIN_IP
これでサーバ同士はつながったのですが、外部からその状態を見ることはできません。ということで4台目はサーバではなくクライアントとして立ち上げます。

$ docker run -d -p 8400:8400 -p 8500:8500 -p 8600:53/udp -h node4 progrium/consul -join $JOIN_IP
これで8400、8500番ポートを使ってConsulの状態を見られるようになりました。例えばcurlで次のように入力します。
```
