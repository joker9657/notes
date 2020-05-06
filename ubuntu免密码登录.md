# rsa密钥认证登陆

1. 在客户端生成公钥、私钥。
```
$ ssh-keygen -t rsa -C "xx@email" 
```
2. 在客户端将生成的公钥添加至服务端的 authorized_keys 文件里
```
$ ssh-copy-id root@192.168.0.1 // 需输入密码验证
```
3. 在服务端禁用密码登录
```
$ sudo vi /etc/ssh/sshd_config // 修改 PasswordAuthentication 为 no 后保存
$ sudo service sshd restart // 重启 ssh 服务
```
以上操作主要为了安全，其次是为了使用 [Envoy部署工具](https://learnku.com/docs/laravel/5.8/envoy/3944)。 



2020-03-21 更新内容  
当使用多个密钥时，容易出现一些问题。


Q：输入ssh-copy-id -i [公钥存储绝对路径] 添加公钥出现报错  
```
INFO: Source of key(s) to be installed: "/home/vagrant/.ssh/id_rsa_github.pub"

attempting to log in with the new key(s), to filter out any that are already installed

WARNING: All keys were skipped because they already exist on the remote system.
```
大致意思为公钥已添加过了，不必重复添加。
问题来了，使用 `ssh server_name@server_ip` 还是需要密码。

搜索一番后，可能是目标服务器sshd服务的公钥认证登录没开。
位置： `/etc/ssh/sshd_config`
```
# PubkeyAuthentication yes // #号说明被注释，未使用，去掉即可
```

敲下命令，又输出了 error..
```
identity_sign: private key /home/vagrant/.ssh/id_rsa contents do not match public
```
公钥跟私钥不匹配。想了下我生成的文件好像不是这个，进入到 ssh 文件夹。
```shell
cd ~/.ssh
sudo vi config // 添加个配置如下

Host server_ip
  HostName server_ip
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_rsa_github
```
保存后再 `ssh server_name@server_ip`， 终于成功;
```
Welcome to Alibaba Cloud Elastic Compute Service !
```

记于2020-04-09
本地ssh登录ghost服务器失败，错误如下：
```shell
OpenSSH_6.6.1, OpenSSL 1.0.1f 6 Jan 2014
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: /etc/ssh/ssh_config line 19: Applying options for *
debug1: Connecting to 10.0.1.64 [10.0.1.64] port 22.
debug1: Connection established.
debug1: identity file /home/koushatalebian/.ssh/id_rsa.pub type 1
debug1: identity file /home/koushatalebian/.ssh/id_rsa.pub-cert type -1
debug1: Enabling compatibility mode for protocol 2.0
debug1: Local version string SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-8
debug1: Remote protocol version 2.0, remote software version OpenSSH_6.0p1 Debian-4+deb7u2
debug1: match: OpenSSH_6.0p1 Debian-4+deb7u2 pat OpenSSH* compat 0x04000000
debug1: SSH2_MSG_KEXINIT sent
debug1: SSH2_MSG_KEXINIT received
debug1: kex: server->client aes128-ctr hmac-md5 none
debug1: kex: client->server aes128-ctr hmac-md5 none
debug1: sending SSH2_MSG_KEX_ECDH_INIT
debug1: expecting SSH2_MSG_KEX_ECDH_REPLY
debug1: Server host key: ECDSA e2:af:83:f8:df:e2:15:db:77:30:e1:6b:e7:dc:77:99
debug1: Host '10.0.1.64' is known and matches the ECDSA host key.
debug1: Found key in /home/koushatalebian/.ssh/known_hosts:10
debug1: ssh_ecdsa_verify: signature correct
debug1: SSH2_MSG_NEWKEYS sent
debug1: expecting SSH2_MSG_NEWKEYS
debug1: SSH2_MSG_NEWKEYS received
debug1: Roaming not allowed by server
debug1: SSH2_MSG_SERVICE_REQUEST sent
debug1: SSH2_MSG_SERVICE_ACCEPT received
debug1: Authentications that can continue: publickey
debug1: Next authentication method: publickey
debug1: Offering RSA public key: /home/koushatalebian/.ssh/id_rsa.pub
debug1: Authentications that can continue: publickey
debug1: No more authentication methods to try.
Permission denied (publickey).
```
百思不得其解，前天还好好地，正常登录，昨天就不行了，经过一系列尝试，甚至重新生成公、私钥，再将公钥传到服务器上依然`Permission denied (publickey).`，折腾了一个小时，突然才想起来去看看服务器登录日志，
```shell
Apr  9 21:13:58 10-9-46-181 sshd[4787]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=183.82.1.45
Apr  9 21:14:00 10-9-46-181 sshd[4787]: Failed password for invalid user postgres from 183.82.1.45 port 29570 ssh2
Apr  9 21:14:00 10-9-46-181 sshd[4787]: Received disconnect from 183.82.1.45 port 29570:11: Normal Shutdown, Thank you for playing [preauth]
Apr  9 21:14:00 10-9-46-181 sshd[4787]: Disconnected from invalid user postgres 183.82.1.45 port 29570 [preauth]
```
发现是这样的错误信息，翻译过来为身份验证被拒绝：所有权错误或文件模式，
```shell
> chmod g-w /home/your_user
> chmod 700 /home/your_user/.ssh
> chmod 600 /home/your_user/.ssh/authorized_keys
```
设置完就可以登录了，记录下防止日后再犯。



参考：

[ssh key exists on remote, but still prompts for password?](https://superuser.com/questions/904654/ssh-key-exists-on-remote-but-still-prompts-for-password)
[添加ssh信任，让你的登陆无需密码](https://rowe98.github.io/2019/05/29/add_ssh_rights/)
