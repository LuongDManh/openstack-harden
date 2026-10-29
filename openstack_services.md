### Hiện tại mới chỉ thực hiện cài đặt: keystone, glance, neutron, placement, nova

1) Các harden activities đã thực hiện: cô lập API public ra một node riêng, truyền các requests từ API public sang API internal. 

2) Node proxy chưa sử dụng HAProxy, chỉ thử với apache2:
File **/etc/apache2/sites-available/keystone-public.conf**:

```bash
<VirtualHost *:80>
    ServerName openstack
    ServerSignature Off
    AllowEncodedSlashes NoDecode
    ProxyPreserveHost On
    ProxyRequests Off

    ProxyPass        /v3      http://10.0.0.11:5000/v3      nocanon
    ProxyPassReverse /v3      http://10.0.0.11:5000/v3

    ProxyPass        /image   http://10.0.0.11:9292/        nocanon
    ProxyPassReverse /image   http://10.0.0.11:9292/

    ProxyPass        /compute http://10.0.0.11:8774/v2.1/   nocanon
    ProxyPassReverse /compute http://10.0.0.11:8774/v2.1/

    ProxyPass        /network http://10.0.0.11:9696/        nocanon
    ProxyPassReverse /network http://10.0.0.11:9696/

    RewriteEngine On

    RewriteCond %{HTTP:Upgrade} =websocket [NC]
    RewriteRule ^/(.*)$ ws://10.0.0.11:6080/websockify [P,L]

    ProxyPass        /vnc/ http://10.0.0.11:6080/
    ProxyPassReverse /vnc/ http://10.0.0.11:6080/

    ProxyPass        /package.json http://10.0.0.11:6080/package.json
    ProxyPassReverse /package.json http://10.0.0.11:6080/package.json

    RequestHeader set X-Forwarded-Proto "http"
    RequestHeader set X-Forwarded-For "%{REMOTE_ADDR}s"
    RequestHeader set X-Forwarded-Host "%{HTTP_HOST}s"
</VirtualHost>
```

3) Các dịch vụ hoạt động bình thường, đã test. Test neutron sử dụng ip netns exec thì ping được internet

4) Đã thành công. 5 dịch vụ cơ bản này đã cài đặt cô lập được API public ra riêng 1 server. Tuy nhiên nếu proxy bị tấn công, hệ thống giảm an toàn.

### Mã hoá.