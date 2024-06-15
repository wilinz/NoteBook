# 使用caddy给网站自动配置https，支持非80端口以及CDN代理

使用caddy给网站自动配置https，支持非80端口以及CDN代理，并自动更新证书

1. 将你的域名DNS服务商迁移到 Cloudflare

2. 创建一个Cloudflare Api key，用于caddy自动配置TLS

   点击“头像”->“My Profile”->“API Tokens”->“Create Token”->"Edit zone DNS(Use template)"

   ![image-20231122192834087](https://raw.githubusercontent.com/wilinz/picgo/main/image/202311221928288.png)

3. SSH连接服务器

   ```shell
   ssh root@xxx.com
   ```

4. 下载包含 cloudflare dns 插件的caddy

   ```shell
   # linux 是系统
   # amd64 是系统架构
   # github.com/caddy-dns/cloudflare 是插件的页面地址
   
   wget -O caddy 'https://caddyserver.com/api/download?os=linux&arch=arm&p=github.com/caddy-dns/cloudflare'
   
   # 授权
   chmod 0777 ./caddy
   ```

5. 编写 Caddyfile 

   ```
   vim ./Caddyfile
   ```

   ```caddyfile
   # 这是一个全局配置
   {
           acme_dns cloudflare <替换成之前创建的 cloudflare ApiKey, 没有尖括号>
   }
   
   # 这是一个反向代理api
   api.xxx.com {
           reverse_proxy localhost:6140
   }
   # 这是一个静态网站
   www.xxx.com {
        root * /var/www/caddy
        file_server
        encode gzip
   }
   ```

6. 试运行

   ```shell
   caddy run --config ./Caddyfile
   ```

7. 浏览器访问你的网站地址试试效果

8. Ctrl + c 终止 caddy, 然后在后台运行 caddy

   ```shell
   caddy start --config ./Caddyfile
   ```

   
