# Gin Web Docker

```shell
git clone https://github.com/piupuer/gin-web-docker
cd gin-web-docker
chmod +x control.sh
```

## Redis sentinel

### Change Master/Slave IP

Single machine multiple instance example

```shell
export REDIS_MASTER_IP=10.13.2.252
export LOCAL_IP=10.13.2.252
# start port
# export REDIS_PORT=6379
```

### Initialization

```shell
./control.sh sentinel 3
```

Waiting:

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/redis/run_sentinel.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/redis/run_sentinel.jpeg?raw=true" width="600" />
</a>

### Status

#### View container

```shell
docker ps | grep redis
```

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/redis/ps.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/redis/ps.jpeg?raw=true" width="600" />
</a>

#### Master

```shell
docker exec -it redis-master redis-cli -a 123456

keys *

# show REPLICATION info
info REPLICATION

# success example:
# connected_slaves:2
```

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/redis/master.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/redis/master.jpeg?raw=true" width="600" />
</a>

#### Slave

```shell
docker exec -it redis-slave2 redis-cli -p 6381 -a 123456

keys *

# show REPLICATION info
info REPLICATION
```

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/redis/slave.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/redis/slave.jpeg?raw=true" width="600" />
</a>

## Loki

### Initialization

```shell
./control.sh loki
```

Waiting:

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/run.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/run.jpeg?raw=true" width="600" />
</a>

### Grafana

#### Login

Default username/password: admin/admin  
<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_login.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_login.jpeg?raw=true" width="600" />
</a>

#### Skip

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_login_skip.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_login_skip.jpeg?raw=true" width="600" />
</a>

#### Add source

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_add_source.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_add_source.jpeg?raw=true" width="600" />
</a>

##### Select Loki

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_select_loki.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_select_loki.jpeg?raw=true" width="600" />
</a>

##### Gateway

URL: loki-gateway:3100

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_set_loki_uri.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_set_loki_uri.jpeg?raw=true" width="600" />
</a>

Save:
<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_save.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_save.jpeg?raw=true" width="600" />
</a>

#### Logs

Explore:  
<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_explore.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_explore.jpeg?raw=true" width="600" />
</a>

Select labels:  
<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_labels.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/loki/grafana_labels.jpeg?raw=true" width="600" />
</a>
