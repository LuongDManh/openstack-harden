# Thông số mạng các node:

### Controller:
a) 10.0.0.11, management network
b) 192.168.1.0/24, không set ip, giao toàn bộ cho Neutron
c) 10.0.1.11, storage network

### Compute:
a) 10.0.0.21, management network
b) 192.168.1.0/24, không set ip, giao toàn bộ cho Neutron
c) 10.0.1.21, storage network

### Storage:
a) 10.0.0.31, management network
b) 10.0.1.31, storage network

### Proxy:
a) 192.168.1.60, public network cho public API
b) 10.0.0.2, management network