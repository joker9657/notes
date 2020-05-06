把代理服务器地址写入shell配置文件`.bashrc`或者`.zshrc`\
直接在`.bashrc`或者`.zshrc`添加下面内容
```shell
$ export http_proxy="http://localhost:port"
$ export https_proxy="http://localhost:port"
```
以使用shadowsocks代理为例，ss的代理端口为`1086`,那么应该设置为
```shell
$ export http_proxy="socks5://127.0.0.1:1086"
$ export https_proxy="socks5://127.0.0.1:1086"
```
然后使用命令让配置即时生效
```shell
$ source .zshrc
```
