# 介绍
[相关文章](https://blog.samlog.club/archives/733/)
# 使用
1、在sentinel目录增加sentinel-1.conf、sentinel-2.conf配置文件

2、docker-compose up -d
# 哨兵配置
`#` 端口

port 26379

`#` 是否后台启动

daemonize no

`#` pid文件

pidfile "/var/run/redis-sentinel.pid"

`#` 日志

logfile "/var/log/sentinel.log"

`#` 工作目录

dir "/tmp"

`#` 定义主的别名 ip 端口 这里的2指的是需要至少2个Sentinel认为主Redis挂了才最终会采取下一步行为

`#`sentinel monitor mymaster 172.28.0.2 6379 2

`#` 如果mymaster 30秒内没有响应，则认为其主观失效

sentinel down-after-milliseconds mymaster 30000

`#` 如果master重新选出来后，其它slave节点能同时并行从新master同步数据的台数有多少个，显然该值越大，所有slave节点完成同步切换的整体速度越快，但如果此时正好有人在访问这些slave，可能造成读取失败，影响面会更广。最保守的设置为1，同一时间，只能有一台干这件事，这样其它slave还能继续服务，但是所有slave全部完成缓存更新同步的进程将变慢。

sentinel parallel-syncs mymaster 1

`#` 该参数指定一个时间段，在该时间段内没有实现故障转移成功，则会再一次发起故障转移的操作，单位毫秒

sentinel failover-timeout mymaster 180000

`#` 主从Redis密码

sentinel auth-pass mymaster 12345678

`#` 不允许使用SENTINEL SET设置notification-script和client-reconfig-script。

sentinel deny-scripts-reconfig yes

`#`每当一个Sentinel启动后，它就会修改并通知其它Sentinel同时修改自身的`sentinel.conf`文件，生成sentinel myid

`#`sentinel myid f30170cf94adb7678425b66558e50096f535f1d4

`#` sentinel config-epoch mymaster 0

`#` 文件最后会加上

`#` Generated by CONFIG REWRITE

`#`user default on nopass ~* +@all

`#`sentinel leader-epoch mymaster 0

`#`sentinel known-replica mymaster 172.28.0.4 6379

`#`sentinel known-replica mymaster 172.28.0.3 6379

`#`sentinel known-sentinel mymaster 172.28.0.5 26379 175177a51483a78d6381ee6e1ed74d541f1dff54

`#`sentinel current-epoch 0