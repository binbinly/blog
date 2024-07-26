# 使用Certbot申请免费 HTTPS 证书及自动续期

## 安装

```shell
yum install -y certbot 
```

## 申请证书

```shell 
# 泛域名：
certbot certonly -d *.example.com --manual --preferred-challenges dns

# 主域名：
certbot certonly -d example.com --manual --preferred-challenges dns
```

## nginx 配置

```nginx
server {
    listen 443 ssl;
    server_name  _;
    root /usr/share/nginx/html;

    # 这里是你证书的位置
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
    ssl_prefer_server_ciphers on;

    location / {
      ...
    }
}
```

## 续期

### 手动续期

```shell
certbot certonly -d example.com --manual --preferred-challenges dns
```

### 自动续期

```shell
certbot renew --quiet --agree-tos --post-hook "nginx -s reload"
--quiet 选项用于抑制 certbot 命令的输出
--post-hook 选项用于在证书续订后执行
```