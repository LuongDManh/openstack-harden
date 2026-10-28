# I. Chrony
Cấu hình để duy trì kết nối dù mất mạng + chỉ cho phép lắng nghe từ IP controller 
```bash
local stratum 10
bindaddress $manage_ip
allow $manage_cidr
```

# II. Database
Cấu hình thêm mã hoá TLS cho database
```bash
[mysqld]
bind-address = 10.0.0.11

default-storage-engine = innodb
innodb_file_per_table = on
max_connections = 4096
collation-server = utf8_general_ci
character-set-server = utf8

#ssl-ca = /etc/ssl/certs/internal-ca.pem
#ssl-cert = /etc/ssl/certs/mariadb-server-cert.pem
#ssl-key = /etc/ssl/private/mariadb-server-key.pem

#tls_version = TLSv1.2,TLSv1.3
```

# III. Memcached
Cấu hình thêm mã hoá TLS, sử dụng cổng 5671 (cổng hỗ trợ TLS)
```bash
# /etc/rabbitmq/rabbitmq.conf
listeners.tcp = none
listeners.ssl.1 = 10.0.0.11:5671

#ssl_options.cacertfile = /etc/rabbitmq/ssl/internal-ca.pem
#ssl_options.certfile = /etc/rabbitmq/ssl/rabbit-server-cert.pem
#ssl_options.keyfile = /etc/rabbitmq/ssl/rabbit-server-key.pem

#ssl_options.verify = verify_peer
#ssl_options.fail_if_no_peer_cert = false

#ssl_options.versions.1 = tlsv1.3
#ssl_options.versions.2 = tlsv1.2

loopback_users.guest = true
```

# IV. Message queue
Tắt UDP để chống flood, bind address để chỉ lắng nghe trên IP controller
```bash
# /etc/memcached.conf (Ubuntu)
-m 1024

-p 11211

-u memcache

-l 127.0.0.1,10.0.0.11

# tắt UDP
-U 0

-c 4096
```

# V. Etcd