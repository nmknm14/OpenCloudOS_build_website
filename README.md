# OpenCloudOS搭建网站并启用frp反代

## 安装OpenCloudOS

- 首先需要在服务器上[安装OpenCloudOS](https://github.com/nmknm14/OpenCloudOS_install_steps)。

## 配置网站

通过以下步骤配置您的网站：

1. 登录到OpenCloudOS管理面板, 我是通过Windows上的软件[FinalShell](https://www.hostbuf.com/)连接到服务器的。
2. 放开防火墙端口80和443，以允许HTTP和HTTPS流量：

```bash
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --permanent --add-port=443/tcp
sudo firewall-cmd --reload
```
或者放行服务：
```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```
查看已经放行的端口：
```bash
sudo firewall-cmd --list-all
``` 
查看已经放行的服务：
```bash
sudo firewall-cmd --list-services
```

3. 安装并配置Web服务器Nginx：

```bash
sudo dnf install nginx
sudo systemctl start nginx
sudo systemctl enable nginx
```
4. 为网站创建目录并设置权限：
```bash
sudo mkdir -p /var/www/mywebsite
sudo chown -R nginx:nginx /var/www/mywebsite
```
5. 创建一个简单的HTML文件作为测试页面：
```bash
sudo nano /var/www/mywebsite/index.html
```
在文件中添加以下内容：
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>测试页面</title>
</head>
<body>
  <h1>你好，Nginx 已经工作正常！</h1>
  <p>这是一个测试页面。</p>
</body>
</html>
```
6. 创建你的website文件夹的配置文件：
```bash
sudo nano /etc/nginx/conf.d/mywebsite.conf
```
7. 配置网站的Nginx配置文件（在`/etc/nginx/nginx.conf`中）：

```bash
server {
    listen 80;
    server_name _;

    root /var/www/mywebsite;
    index index.html;

    access_log /var/log/nginx/mywebsite_access.log;
    error_log /var/log/nginx/mywebsite_error.log;
}
```
8. 测试Nginx配置是否正确：

```bash 
sudo nginx -t
```
9. 重新加载Nginx以应用更改：

```bash
sudo systemctl reload nginx
```
10. 放行所有端口
    
```bash
sudo systemctl disable --now firewalld
```

## 启用frp中转，反代网站

为了使您的网站能够通过frp进行访问，您需要进行以下配置：

