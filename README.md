# Redis Cluster (Predixy, docker-compose)

> 6300번대는 master, 6400번대는 slave 로 구분

## git clone
```bash
$ git clone 
```

## redis 용 docker network 생성
```bash
$ docker network create --gateway 172.18.0.1--subnet 172.18.0.0/16 redis-net
```

## predixy cluster.conf 파일 내용 수정
```diff
## redis cluster server pool define

ClusterServerPool {
    MasterReadPriority 60
    StaticSlaveReadPriority 50
    DynamicSlaveReadPriority 50
    RefreshInterval 1
    ServerTimeout 1
    ServerFailureLimit 10
    ServerRetryTimeout 1
    KeepAlive 120
    Servers {
+        + redis-node1:6300
+        + redis-node2:6301
+        + redis-node3:6302
+        + redis-node11:6400
+        + redis-node21:6401
+        + redis-node31:6402
    }
}
```

## docker-compose 로 서비스 실행
```bash
$ docker-compose up -d
```

## master, slave 노드 추가
```bash
# 6300, 6301, 6302 master 노드 추가
$ redis-cli --cluster create redis-node1:6300 redis-node2:6301 redis-node3:6302

# 6400, 6401, 6402 slave 노드 추가
$ redis-cli --cluster add-node redis-node11:6400 redis-node1:6300 --cluster-slave
$ redis-cli --cluster add-node redis-node21:6401 redis-node2:6301 --cluster-slave
$ redis-cli --cluster add-node redis-node31:6402 redis-node3:6302 --cluster-slave
```

## docker 재시작
```bash
$ docker restart predixy
```