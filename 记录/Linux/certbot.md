# certbot

工具本身比较复杂，需要http认证或者DNS认证，但是有插件，就可以用nginx自动认证了。

有nginx的情况下，用下面这一条就能申请：

```
sudo certbot --nginx -d <domain>
```

certbot delete可以删除证书，命令是交互式的。