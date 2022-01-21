## Hướng dẫn cài đặt💥
### Cài đặt remi repo:❤❤❤

```
yum -y update
yum -y install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
```
- Cài đặt Redis:🔑

```
yum --enablerepo=remi install redis


rpm -qi redis
```
- Cấu hình firewall:

```
firewall-cmd --add-port=6379/tcp --permanent

firewll-cmd --reload
```
- Cấu hình sysctl:

- Cấu hình file /etc/sysctl.conf

```
vm.overcommit_memory=1
```
- Update cấu hình:

```
sysctl vm.overcommit_memory=1
```
- Cấu hình systemctl và start service:

```
systemctl enable --now redis
```
- Cấu hình máy chủ Master🚗🚗
- Mở file cấu hình /etc/redis.conf và thực hiện cập nhật các cấu hình sau:

```
#bind 127.0.0.1

maxmemory-policy noeviction

tcp-keepalive 60

appendonly yes

appendfilename "appendonly.aof"

protected-mode no
```
- Restart service:

```
systemctl restart redis.service
```
- Cấu hình máy chủ Slave🎉
- Mở file cấu hình /etc/redis.conf và thực hiện cập nhật các cấu hình sau:

```
bind 127.0.0.1

replicaof 192.168.0.1 6379

appendonly yes

appendfilename "appendonly.aof"

protected-mode no
```
- Restart service:🖥🖥✈

```
systemctl restart redis.service
```
- Cấu hình Sentinel
- Mở file cấu hình /etc/redis-sentinel.conf, xóa các cấu hình cũ và cấu hình như sau:

```
protected-mode no

port 26379

daemonize yes

pidfile "/var/run/redis-sentinel.pid"

logfile "/var/log/redis/sentinel.log"

dir .

sentinel deny-scripts-reconfig yes

sentinel monitor mymaster 192.168.0.1 6379 2

sentinel down-after-milliseconds mymaster 5000

sentinel failover-timeout mymaster 10000
```
- 😎😎 Cấu hình service và start service:

```
systemctl enable --now redis-sentinel.service
```
- Kiểm tra replication 🔐
- Trên máy chủ master:

```
# redis-cli

127.0.0.1:6379> info replication

# Replication

role:master

connected_slaves:2

slave0:ip=192.168.0.2,port=6379,state=online,offset=575099851,lag=0

slave1:ip=192.168.0.3,port=6379,state=online,offset=575099851,lag=0

master_replid:7df345aed5b6aae7512865c77ee784913d3678d7

master_replid2:d4584c75d2c5e03284e98511dba94cc095ada933

master_repl_offset:575099851

second_repl_offset:16516

repl_backlog_active:1

repl_backlog_size:1048576

repl_backlog_first_byte_offset:574051276

repl_backlog_histlen:1048576
```
- Trên 2 máy chủ slave:🛠🛠

```
$ redis-cli 

127.0.0.1:6379> info replication

# Replication

role:slave

master_host:192.168.0.1

master_port:6379

master_link_status:up

master_last_io_seconds_ago:1

master_sync_in_progress:0

slave_repl_offset:575120130

slave_priority:100

slave_read_only:1

connected_slaves:0

master_replid:7df345aed5b6aae7512865c77ee784913d3678d7

master_replid2:0000000000000000000000000000000000000000

master_repl_offset:575120130

second_repl_offset:-1

repl_backlog_active:1

repl_backlog_size:1048576

repl_backlog_first_byte_offset:574071555

repl_backlog_histlen:1048576
```

**Giờ bạn thực hiện stop redis trên máy chủ 192.168.0.1, nếu 1 trong 2 máy chủ 192.168.0.2 hoặc 192.168.0.3 được up thành máy chủ redis master là quá trình cài đặt diễn ra thành công**
😎😎😎
