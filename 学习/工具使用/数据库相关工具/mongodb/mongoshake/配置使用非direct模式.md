## 参考文档
https://yq.aliyun.com/articles/719704
https://blog.51cto.com/lee90/2390883

## 收集器
### 配置
```shell
mongo_urls = mongodb://用户名:密码@mongoip:27017
mongo_connect_mode = secondaryPreferred
collector.id = mongoshake-testtyz 
sync_mode = oplog  #增量模式
system_profile = 9203 #restful端口，查看统计情况
log.dir =   #log日志存储目录
log.file = collector.log
log.buffer = true   # 
filter.namespace.black =    #黑名单配置
filter.namespace.white =    #白名单配置 eg: test.abc;test.testb
filter.pass.special.db =   #特性场景想同步admin,mongoshake等表，可以设置
oplog.gids =   #没啥用，阿里云上的mongodb才有用
shard_key = collection  #按表hash，如果没有索引，建议用id进行hash,同步性能更高
worker = 1  #内部发送的worker数目，测试用暂时不需要设置很高
worker.batch_queue_size = 64
adaptive.batching_max_size = 1024
fetcher.buffer_capacity = 256
worker.oplog_compressor = none #是否启用压缩，非direct模式可以启用减少网络消耗
tunnel = tcp  #通道模式
tunnel.address = 127.0.0.1:9520 # 非direct模式需要启用receiver进行解析
context.storage = database  #存储checkpoint到数据库
context.storage.url =
context.address = ckpt_default_version208_monitor #存储checkpoint的表名
context.start_position = 2019-11-18T00:00:01Z # 强制从某一天开始拉取
master_quorum = false  #开启主备mongoshake拉取同一个源端，此参数需要开启
transform.namespace =   #转换命名空间
dbref = false  # 有dbref操作，需要开启，比较消耗性能
replayer.dml_only = true  # 是否开启ddl同步,false代表开启
replayer.executor.upsert = false #如果_id不在目的库存在，是否修改update语句为insert语句
replayer.executor.insert_on_dup_update = false # 如果_id在目的库已经存在，是否insert语句为update语句
replayer.conflict_write_to = none #如果存在冲突，记录下冲突的文档
replayer.durable = true 
replayer.collection_parallel = 1 #并发最大拉取的表个数
replayer.document_parallel = 1 #同一个表内部并发的进程数
replayer.document_batch_size = 256 #目的端写入的batch大小
replayer.collection_drop = false # 如果同步时目的库存在，是否先删除目的库再进行同步
```

### 启动
```shell
 ./bin/collector.linux -conf=conf/collector.conf
```

## 接收器
### 为什么要使用接收器
如果把收集器的tunnel.address直接设置为用户平台，那么用户平台接受的数据是一堆乱码，是有控制信息在里面的，用户不太好直接剥离，所以需要receiver进行对接，但是目前receiver对接后是把内容直接输出的，需要自己修改receiver/replayer.go,需要用户懂golang语言
mongoshake(collector)=>tunnel=>receiver=>user's platform

### 配置
```shell
log.level = info
log.file = receiver.log
log.buffer = true
system_profile = 9500
tunnel = tcp
tunnel.address = 127.0.0.1:9520  #tunnel地址必须和收集器的保持一致
replayer = 1  #这个和收集器的worker数量保持一致
```

### 启动
```shell
./bin/receiver.linux -conf=conf/receiver.conf -verbose
```
