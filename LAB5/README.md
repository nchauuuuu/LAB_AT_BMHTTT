# LAB 5 - THIẾT LẬP VÀ KIỂM THỬ TƯỜNG LỬA pfSense

## 1. Thông tin sinh viên
- Họ và tên: Nguyễn Lê Ngọc Châu
- MSSV: 1150070003
- Lớp: 11_TTMT
- Tên bài: Lab 5 - Thiết lập và kiểm thử tường lửa pfSense

## 2. Môi trường thực hành
- VMware Workstation
- pfSense CE
- Windows Server 2022
- IIS Web Server
- Máy LAN: 10.0.0.2/8
- Máy LAN-Test: 10.0.0.3/8
- Máy DMZ-Web: 172.16.0.2/16
- Mạng WAN: Bridged Adapter
- Mạng LAN: VMnet1
- Mạng DMZ: VMnet2

Cấu hình pfSense:
- LAN: 10.0.0.1/8
- DMZ: 172.16.0.1/16
- WAN: nhận địa chỉ IP từ mạng ngoài

## 3. Nội dung đã thực hiện

### Cấu hình mô hình mạng pfSense
Cài đặt pfSense trên VMware và cấu hình 3 card mạng gồm WAN, LAN và DMZ.

Thiết lập:
- WAN kết nối mạng ngoài bằng Bridged Adapter
- LAN sử dụng VMnet1
- DMZ sử dụng VMnet2
- LAN pfSense: 10.0.0.1/8
- DMZ pfSense: 172.16.0.1/16

Kiểm tra các interface trên console và WebGUI pfSense.
Kết quả: PASS.

### Cấu hình DMZ-Web và IIS
Tạo máy Windows Server 2022 trong vùng DMZ với cấu hình:
- IP: 172.16.0.2/16
- Gateway: 172.16.0.1

Cài đặt Web Server IIS và kiểm tra bằng:
http://localhost

Trang mặc định IIS hiển thị thành công.
Kết quả: PASS.

### Outbound NAT và LAN Rule
Kiểm tra Outbound NAT trên pfSense để cho phép các mạng nội bộ truy cập Internet.

Disable các rule mặc định:
- Default allow LAN to any IPv4
- Default allow LAN IPv6 to any

Sau khi thay đổi rule, thực hiện Reset States để xóa các state cũ của firewall.

Tạo rule cho phép LAN truy cập Internet và kiểm tra kết nối.
Kết quả: PASS.

### TH1 - Chặn ICMP nhưng vẫn cho phép Web/DNS
Cấu hình các firewall rule:
- Block ICMP từ mạng LAN
- Allow DNS TCP/UDP port 53
- Allow HTTP TCP port 80
- Allow HTTPS TCP port 443

Kiểm tra:
- Ping 8.8.8.8 bị chặn
- DNS vẫn phân giải được tên miền
- HTTPS vẫn truy cập được website

Kết quả: PASS.

### TH2 - Chỉ cho một host LAN truy cập Internet
Cấu hình:
- Pass host 10.0.0.2 ra Internet
- Block các host còn lại trong mạng 10.0.0.0/8

Rule Pass cho 10.0.0.2 được đặt phía trên rule Block.

Kiểm tra:
- 10.0.0.2 truy cập Internet thành công
- 10.0.0.3 bị chặn truy cập Internet

Kết quả: PASS.

### TH3 - Cô lập DMZ khỏi LAN
Trước khi áp dụng rule Block, kiểm tra từ DMZ-Web:
172.16.0.2 -> 10.0.0.2

Kết quả baseline cho thấy DMZ có thể truy cập LAN.

Sau đó tạo rule:
- Block 172.16.0.0/16 -> 10.0.0.0/8

Kiểm tra sau khi áp dụng rule:
- DMZ không ping được 10.0.0.2
- DMZ vẫn ping được 8.8.8.8

DMZ được cô lập khỏi LAN nhưng vẫn có thể truy cập Internet.
Kết quả: PASS.

### TH4 - Port Forward WAN đến DMZ-Web
Cấu hình NAT Port Forward trên pfSense:
- Interface: WAN
- Protocol: TCP
- WAN Port: 8080
- Redirect Target IP: 172.16.0.2
- Redirect Target Port: 80

Từ máy thật truy cập:
http://192.168.0.13:8080

pfSense chuyển tiếp lưu lượng vào IIS trên máy DMZ-Web và trang IIS hiển thị thành công.
Kết quả: PASS.

### TH5 - Firewall Logging
Bật chức năng logging cho rule Block DMZ -> LAN.

Tạo lưu lượng ICMP từ:
172.16.0.2 -> 10.0.0.2

Kiểm tra tại:
Status -> System Logs -> Firewall

Firewall Log ghi nhận gói tin bị chặn với đúng địa chỉ nguồn và địa chỉ đích.
Kết quả: PASS.

## 4. Kết quả
Sau khi hoàn thành bài lab:
- pfSense hoạt động với đầy đủ 3 vùng WAN, LAN và DMZ
- LAN và DMZ truy cập Internet thông qua pfSense
- Firewall rule hoạt động đúng theo thứ tự từ trên xuống
- Có thể chặn ICMP nhưng vẫn cho phép DNS và Web
- Có thể giới hạn quyền truy cập Internet theo từng host
- DMZ được cô lập khỏi LAN
- Dịch vụ IIS trong DMZ có thể được truy cập từ WAN bằng Port Forward
- Firewall Logging ghi nhận được các gói tin bị chặn

Kết quả tổng thể: PASS.

## 5. Một số lỗi gặp phải và cách khắc phục
- LAN và DMZ có lúc không ping được gateway do card mạng VMware bị mất kết nối. Khắc phục bằng cách kiểm tra lại VMnet1, VMnet2 và reconnect Network Adapter.
- WebGUI pfSense có lúc không truy cập được mặc dù ping gateway thành công. Khắc phục bằng cách restart webConfigurator.
- Khi thay đổi firewall rule, kết quả cũ vẫn còn do pfSense là stateful firewall. Khắc phục bằng cách vào Diagnostics -> States -> Reset States.
- DMZ và LAN có lúc không ping chéo được. Sử dụng Packet Capture trên pfSense để kiểm tra đường đi của gói tin và xác định vị trí traffic bị chặn.
- Máy tính không đủ RAM để chạy nhiều máy ảo cùng lúc. Khắc phục bằng cách giảm RAM VM, tắt hoặc suspend các VM không cần thiết và chỉ chạy các máy cần cho từng tình huống.
- VMware không suspend được VM do ổ đĩa thiếu dung lượng. Khắc phục bằng cách dọn Temporary Files và giải phóng dung lượng ổ đĩa.
- WAN pfSense có lúc không nhận được IP do VMnet0 bridge nhầm vào Microsoft Wi-Fi Direct Virtual Adapter. Khắc phục bằng cách bridge VMnet0 vào đúng card Wi-Fi vật lý.
- Port Forward ban đầu không truy cập được từ máy thật do WAN pfSense chưa kết nối đúng với mạng vật lý. Sau khi sửa Bridged Adapter, WAN nhận IP và truy cập IIS qua port 8080 thành công.

