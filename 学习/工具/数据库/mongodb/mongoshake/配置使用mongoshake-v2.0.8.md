## 下载
https://codeload.github.com/alibaba/MongoShake/tar.gz/release-v2.0.8-20190930

## 配置go环境
```shell
mkdir /home/gocode/
export GOPATH=/home/gocode/
echo 'export GOPATH=/home/gocode/' >> /root/.bashrc
source /root/.bashrc
```

## 安装配置mongoshake
```shell
#解压mongoshake
tar zxvf 上面下载的tar.gz文件
mv 目录 mongoshake
cd mongshake/src/vendor
GOPATH=`pwd`/../..
#配置http代理(不一样拉不下来go的依赖库)
<a href="https://www.yuque.com/tianyunchong/watpi7/olxfqw" target="_blank">
go get -u -v github.com/kardianos/govendor
/home/gocode/bin/govendor sync
#编译生成二进制文件
cd ../../ && ./build.sh
#最后的清理工作
#去掉http代理
git config --global --unset http.proxy
```

## 遇到的问题
1. go: cannot find main module; see 'go help modules'
   ```shell
   export GO111MODULE=off
   ```
