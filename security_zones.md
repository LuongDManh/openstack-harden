# I. Security zones trong OpenStack
### Có 4 loại zones:
1. Public domain
2. Guest domain
3. Management plane
4. Data plane

# II. Phân chia thành phần
### Public domain:
Các dịch vụ được cung cấp cho người dùng cuối:
```bash
Horizon dashboard
Public API endpoints
```
### Guest domain:
Các thành phần:
```bash
Card mạng ảo
Switch ảo phục vụ self-service network
Instance
```
### Management plane:
1. Các tiến trình xử lí logic
2. Cơ sở dữ liệu
3. Message Queue
4. Lệnh điều khiển nội bộ (nova-scheduler....)
5. Internal API endpoints
### Data plane:
1. Các cổng giao tiếp của lưu trữ
2. Các thành phần lưu trữ

# III. Phân chia network
Đề xuất: 
#### 3 NICs cho controller:
1. Management network: NAT, chỉ phục vụ các lưu lượng thuộc management plane.
2. Provider network: có kết nối internet, phục vụ mạng provider do Neutron quản lý.
3. Storage network: phục vụ API, tiến trình điều khiển cho storage
#### 3 NICs cho compute:
1. Management network
2. Provider network
3. Storage network
#### 2 NICs cho storage
1. Management network
2. Storage network
#### 2 NICs cho proxy node:
1. Public network: có internet, mã hoá tls, api public chỉ gán trên mạng này
2. Management network: như controller

# IV. Tiến trình
Cô lập các tiến trình, đề xuất cô lập dựa trên network mà các tiến trình sử dụng. Ưu tiên sử dụng giải pháp cô lập sẵn có: Ansible Lxc.