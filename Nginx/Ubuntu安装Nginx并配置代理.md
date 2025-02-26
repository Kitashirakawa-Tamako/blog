
# 下载并安装
更新软件包列表：
```
sudo apt update
```
安装Nginx：

```
sudo apt install nginx
```
安装完成后，Nginx将自动启动。可以使用以下命令检查Nginx的状态：

```
sudo systemctl status nginx
```
如果一切正常，可以将看到Nginx正在运行。

# 修改配置
修改Nginx的默认配置文件`/etc/nginx/sites-available/default`。

找到server块，其中包含listen 80。将其替换为：

```
location / {
    proxy_pass http://localhost:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
}
```
这将把80端口的所有请求转发到本地的3000端口，并添加一些必要的代理头。

检查Nginx配置是否正确：

```
sudo nginx -t
```

如果没有错误消息，表示配置文件已通过验证。

重启Nginx服务以使更改生效


```
sudo systemctl restart nginx
```
现在，Nginx将会将80端口的请求转发到3000端口。
