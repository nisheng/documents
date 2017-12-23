## linux 笔记

### 查找内容

```shell
cat /var/log/nginx/access.log | grep 123.179.118.160
tail -f  /var/log/nginx/access.log |grep adshare/list 
```

```shell
lsof -i:8080
```

```
前阵子搭建Hadoop时，配置了本机（localhost）的ssh的公钥到authorized_keys文件中，但是在ssh连接localhost时仍然提示需要输入密码，后来发现是$HOME/.ssh/authorized_keys这个文件的权限问题引起的。
其原因是，不能让所有者之外的用户对authorized_keys文件有写权限，否则，sshd将不允许使用该文件，因为它可能会被其他用户篡改。
命令行的演示如下：
chmod 644 authorized_keys
```

