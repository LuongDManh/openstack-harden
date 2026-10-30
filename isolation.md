### Đã thực hiện:
1) Cô lập API public ra một node proxy riêng, user public chỉ truy cập / biết tới các url endpoint đó.
2) Cấu hình theo luồng hoạt động: Request từ API public trên card 192.168.1.60 / node proxy -> card dmz 10.0.2.10 / node proxy -> reverse proxy sang card 10.0.2.11 trên controller -> DNAT về địa chỉ url endpoint của api internal
3) Cài đặt firewall trên controller, chỉ đồng ý kết nối từ proxy qua các cổng cấu hình trong reverse proxy

### Cấu hình

1) Node proxy chưa sử dụng HAProxy, chỉ thử với apache2:
File **/etc/apache2/sites-available/keystone-public.conf**:

```bash
<VirtualHost *:80>
    ServerName openstack
    ServerSignature Off
    AllowEncodedSlashes NoDecode
    ProxyPreserveHost On
    ProxyRequests Off

    ProxyPass        /v3      http://10.0.2.11:5000/v3      nocanon
    ProxyPassReverse /v3      http://10.0.2.11:5000/v3

    ProxyPass        /image   http://10.0.2.11:9292/        nocanon
    ProxyPassReverse /image   http://10.0.2.11:9292/

    ProxyPass        /compute http://10.0.2.11:8774/v2.1/   nocanon
    ProxyPassReverse /compute http://10.0.2.11:8774/v2.1/

    ProxyPass        /network http://10.0.2.11:9696/        nocanon
    ProxyPassReverse /network http://10.0.2.11:9696/

    RewriteEngine On

    RewriteCond %{HTTP:Upgrade} =websocket [NC]
    RewriteRule ^/(.*)$ ws://10.0.2.11:6080/websockify [P,L]

    ProxyPass        /vnc/ http://10.0.2.11:6080/
    ProxyPassReverse /vnc/ http://10.0.2.11:6080/

    ProxyPass        /package.json http://10.0.2.11:6080/package.json
    ProxyPassReverse /package.json http://10.0.2.11:6080/package.json

    RequestHeader set X-Forwarded-Proto "http"
    RequestHeader set X-Forwarded-For "%{REMOTE_ADDR}s"
    RequestHeader set X-Forwarded-Host "%{HTTP_HOST}s"
</VirtualHost>
```
