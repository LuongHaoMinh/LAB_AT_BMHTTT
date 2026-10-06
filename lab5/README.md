BÁO CÁO THỰC HÀNH LAB 5

- Họ và tên: Lương Hào Minh
- MSSV: 1150080066
- Bài thực hành: Lab 5 – Xây dựng, cấu hình và quản trị Tường lửa pfSense trên môi trường ảo hóa

1. Môi trường thực hành
- Hệ điều hành / Máy ảo:
  - Tường lửa / Router: pfSense 2.7.2-RELEASE (nền FreeBSD 64-bit) trên VMware Workstation.
  - Máy nguồn / Máy quản trị (LAN): Windows Server 2025 (hoặc Windows 11 / Ubuntu) trên VMware Workstation.
- Cấu hình Card mạng ảo (VMware Virtual Network):
  - WAN (Interface em0): Chế độ NAT (nhận IP động DHCP từ VMware).
  - LAN (Interface em1): Chế độ Custom / Host-only (VMnet2) (dải IP nội bộ cô lập).
- Công cụ / Dịch vụ sử dụng:
  - pfSense Console (FreeBSD menu)
  - WebGUI pfSense (HTTP/HTTPS qua trình duyệt Web)
  - Ping, Tracert, Network & Sharing Center trên Windows Server 2025

2. Cách dựng môi trường
1. Khởi tạo và cấu hình phần cứng VM pfSense:
   - Tạo VM mới chọn loại hệ điều hành Other / FreeBSD 64-bit (không chọn Linux/Ubuntu).
   - Thêm đủ 2 Card mạng (Network Adapters): Adapter 1 gán dải WAN (NAT), Adapter 2 gán dải LAN nội bộ (VMnet2 / Host-only).
2. Cài đặt và gán Interface pfSense:
   - Tiến hành cài đặt pfSense từ file ISO vào đĩa ảo.
   - Tại màn hình Console pfSense, gán interface em0 cho WAN và em1 cho LAN.
3. Thiết lập địa chỉ IP ban đầu (Console Option 2):
   - Đặt IP tĩnh cho interface LAN là 192.168.1.1/24.
   - Kích hoạt dịch vụ DHCP Server trên LAN (cấp dải IP từ 192.168.1.100 đến 192.168.1.200).
4. Kết nối máy trạm LAN (Windows Server 2025):
   - Chuyển card mạng của Windows Server 2025 sang dải VMnet2 (trùng với LAN pfSense).
   - Đặt IP tĩnh 192.168.1.10/24, Default Gateway 192.168.1.1, DNS 192.168.1.1.
5. Kiểm tra thông mạng và truy cập WebGUI:
   - Kiểm tra kết nối từ Windows Server 2025 bằng lệnh ping 192.168.1.1.
   - Đăng nhập giao diện web-based tại https://192.168.1.1 với tài khoản admin / pfsense.

3. Tiến độ và kết quả các tình huống
- Tình huống 1 – Khởi tạo Interface & Đặt IP Console (Mục 1-4): Gán đúng card mạng WAN/LAN, đặt địa chỉ IP lớp LAN 192.168.1.1/24 và truy cập thành công WebGUI từ máy trạm LAN — PASS
- Tình huống 2 – Cấu hình Tường lửa (Firewall Rules): Tạo các Rule quy định lưu lượng trên giao diện LAN (cho phép/chặn ICMP Ping, cho phép truy cập HTTP/HTTPS, chặn port chỉ định) và áp dụng thành công — PASS
- Tình huống 3 – Cấu hình NAT / Port Forwarding: Cấu hình Port Forwarding trên interface WAN trỏ port 80/3389 về địa chỉ IP của Windows Server 2025 (192.168.1.10) — PASS


4. Lỗi gặp phải và cách khắc phục
- Lỗi chọn sai loại OS khi tạo VM: Ban đầu chọn nhầm hệ điều hành Linux (Ubuntu 64-bit) dẫn đến pfSense bị lỗi nhân boot; khắc phục bằng cách chọn lại loại hệ điều hành là Other > FreeBSD 64-bit.
- Lỗi thiếu Card mạng (Network interface mismatch): Khi khởi chạy pfSense màn hình báo No link-up detected hoặc thiếu interface; khắc phục bằng cách vào VM Settings thêm Network Adapter thứ 2 và gán chính xác em0 cho WAN, em1 cho LAN.
- Lỗi không vào được WebGUI (ERR_ADDRESS_UNREACHABLE): Trình duyệt trên Windows Server 2025 báo không thể kết nối tới https://192.168.1.1; khắc phục bằng cách đưa card mạng của cả 2 máy về cùng một dải VMnet2 (Host-only) và đặt lại IP tĩnh 192.168.1.10/24 cho Windows Server 2025.
- Lỗi Cảnh báo Chứng chỉ Bảo mật (SSL Warning): Trình duyệt Edge/Chrome cảnh báo kết nối không an toàn (Your connection is not private); khắc phục bằng cách nhấn Advanced > chọn Proceed to 192.168.1.1 (unsafe) để tiếp tục vào trang đăng nhập.