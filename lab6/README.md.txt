BÁO CÁO THỰC HÀNH LAB 6 
- Họ và tên: Lương Hào Minh
- MSSV: 1150080066
- Bài thực hành: Lab 6 – Xác thực Active Directory và Chính sách truy cập trên pfSense

1. Môi trường thực hành
- Hệ điều hành / Máy ảo:
  - Tường lửa / Router: pfSense 2.7.2-RELEASE (nền FreeBSD 64-bit) trên VMware Workstation.
  - Domain Controller (DC01): Windows Server 2022 trên VMware Workstation.
- Cấu hình Card mạng ảo (VMware Virtual Network / Virtual Network Editor):
  - WAN: Chế độ NAT / VMnet8 (nhận IP động DHCP).
  - MGMT: Chế độ Host-only / VMnet1 (192.168.56.0/24) dùng riêng để quản trị WebGUI từ máy thật.
  - LAN: Chế độ Custom / VMnet2 (10.6.0.0/24) cho mạng nội bộ chứa Domain Controller và các Client.
  - DMZ: Chế độ Custom / VMnet3 (172.16.6.0/24) cho mạng vùng đệm chứa Web server mô phỏng.

2. Các bước thực hiện đã hoàn thành (Đến Phần 4)

Bước 1: Tạo và cấu hình các mạng ảo trên VMware
- Thiết lập các dải mạng ảo riêng biệt (VMnet1 cho MGMT, VMnet2 cho LAN, VMnet3 cho DMZ và VMnet8 cho WAN) để phân tách hoàn toàn lưu lượng theo mô hình Lab 6.

Bước 2: Cấu hình 4 Card mạng ảo cho pfSense-LAB6
- Thêm và gán chính xác 4 Network Adapters cho máy ảo pfSense:
  - Adapter 1: Gán vào mạng WAN.
  - Adapter 2: Gán vào VMnet1 (MGMT).
  - Adapter 3: Gán vào VMnet2 (LAN).
  - Adapter 4: Gán vào VMnet3 (DMZ).

Bước 3: Đặt địa chỉ IP cho các Interface pfSense (Console Option 2)
- Cấu hình IP tĩnh và tắt dịch vụ DHCP trên các interface nội bộ để kiểm soát policy:
  - WAN: Nhận IP động qua DHCP (192.168.250.x).
  - MGMT: Đặt IP 192.168.56.254/24 (Tắt DHCP Server).
  - LAN: Đặt IP 10.6.0.1/24 (Tắt DHCP Server).
  - DMZ: Đặt IP 172.16.6.1/24 (Tắt DHCP Server).
- Kiểm tra truy cập trang quản trị WebGUI thành công từ máy thật qua địa chỉ https://192.168.56.254.

Bước 4: Chuẩn bị Domain Controller (DC01)
- Cấu hình IP cho DC01: Đặt IP tĩnh 10.6.0.10/24, Gateway 10.6.0.1, Preferred DNS 10.6.0.10.
- Cài đặt dịch vụ: Cài đặt Active Directory Domain Services (AD DS) và DNS Server, tạo Domain mới có tên lab6.test.
- Cấu trúc tài khoản trong Active Directory:
  - Tạo Organizational Unit (OU) tên LAB6-Users.
  - Tạo 2 user đại diện người dùng: sv_a và sv_b.
  - Tạo một Service Account chuyên dụng svc_pfsense_ldap (với mật khẩu riêng, không cấp quyền Domain Admin) để phục vụ cho việc bind LDAP trên pfSense sau này.

3. Lỗi gặp phải và cách khắc phục (Đã xử lý)
- Lỗi không truy cập được WebGUI pfSense từ máy thật (ERR_CONNECTION_TIMED_OUT):
  - Nguyên nhân: Card mạng Adapter 2 của pfSense bị gán nhầm sang mạng khác hoặc card ảo VMnet1 trên máy thật chưa được kích hoạt.
  - Khắc phục: Kiểm tra lại phần cứng máy ảo pfSense đảm bảo Adapter 2 trỏ đúng VMnet1, đồng thời bật (Enable) card VMware Network Adapter VMnet1 trên máy thật.