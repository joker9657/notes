# nginx -s reload 出现的问题
首先我停止了 nginx 的服务,
```shell
$ service nginx stop
```
然后，查看配置文件是否正确,
```shell
$ nginx -t
 
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
最后，打算重新加载服务。
```shell
$ sudo nginx -s reload

nginx: [error] invalid PID number "" in "/run/nginx.pid"
```

复制粘贴搜索后，了解到 reload 命令**只能用于服务已运行状态的时候**
> nginx -s reload is only used to tell a running nginx process to reload its config. After a stop, you don't have a running nginx process to send a signal to. Just run nginx (possibly with a -c /path/to/config/file)

## 参考：
[Nginx Invalid PID number
](https://stackoverflow.com/questions/7646972/nginx-invalid-pid-number)