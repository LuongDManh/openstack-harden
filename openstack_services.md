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

    ProxyPass        /v3      http://10.0.0.11:5000/v3      nocanon
    ProxyPassReverse /v3      http://10.0.0.11:5000/v3

    ProxyPass        /image   http://10.0.0.11:9292/         nocanon
    ProxyPassReverse /image   http://10.0.0.11:9292/

    ProxyPass        /compute http://10.0.0.11:8774/v2.1/    nocanon
    ProxyPassReverse /compute http://10.0.0.11:8774/v2.1/
    
    ProxyPass        /vnc     http://10.0.0.11:6080/        nocanon
    ProxyPassReverse /vnc     http://10.0.0.11:6080/

    ProxyPass        /network   http://10.0.0.11:9696/         nocanon
    ProxyPassReverse /network   http://10.0.0.11:9696/

    RequestHeader set X-Forwarded-Proto "http"
    RequestHeader set X-Forwarded-For   "%{REMOTE_ADDR}s"
    RequestHeader set X-Forwarded-Host  "%{HTTP_HOST}s"
</VirtualHost>
```

3) Các dịch vụ hoạt động bình thường, đã test. Test neutron sử dụng ip netns exec thì ping được internet

### Kết quả
Lệnh curl, thực hiện từ node proxy thì đều nhận response 2xx/HTTP
Tuy nhiên truy cập thử instance từ máy khác qua console url thì lại không connect được.