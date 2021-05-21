## 安装配置Shadowsocks
```shell
yum install python-setuptools && easy_install pip
pip install shadowsocks
#使用
ssserver -p 444 -k password -m rc4-md5
#后台运行
sudo ssserver -p 444 -k password -m rc4-md5 --user nobody -d start
```

## 安装配置privoxy
```shell
yum install privoxy
#修改配置/etc/privoxy/config
listen-address  0.0.0.0:8118
enable-remote-toggle  1
forward-socks5 / 本机ip:444
#启动Privoxy
service privoxy restart
```

## 如何使用
```shell
git config --global http.proxy http://代理所在机器ip:8118
export http_proxy="http://代理所在机器ip:8118" export https_proxy=$http_proxy

# 去掉代理
git config --global --unset http.proxy
```