# LAB 3 - NHẬN DIỆN VÀ ỨNG PHÓ CÁC MỐI ĐE DỌA ĐẾN AN TOÀN THÔNG TIN

## 1. Thông tin sinh viên
- Họ và tên: Nguyễn Lê Ngọc Châu
- MSSV: 1150070003
- Tên bài: Lab 3 - Nhận diện và ứng phó các mối đe dọa đến an toàn thông tin

## 2. Môi trường thực hành
- VMware Workstation Pro 26H1
- Windows 11 25H2 x64
- Python 3.14.7
- Wireshark 4.6.8 + Npcap
- Sysmon 15.22
- Autoruns 14.3
- Process Explorer 17.14
- Microsoft Defender và Windows Firewall được bật trong quá trình thực hành

Thư mục làm việc chính:
C:\LAB3

## 3. Nội dung đã thực hiện
### TH1 - Asset, Vulnerability, Threat, Risk
Xác định tài sản, lỗ hổng, mối đe dọa, rủi ro và biện pháp kiểm soát. Phân loại các nguồn đe dọa gồm hành động vô ý, hành động cố ý, thảm họa tự nhiên, lỗi kỹ thuật và lỗi quản lý.
Kết quả: PASS.

### TH2 - EICAR và Microsoft Defender
Tạo file kiểm thử EICAR và kiểm tra Microsoft Defender Protection History. Defender đã phát hiện mẫu kiểm thử.
Kết quả: PASS.

### TH3 - Xác thực và mật khẩu
Tạo tài khoản lab3user, thực hiện đăng nhập đúng và sai, kiểm tra Event ID 4624, 4625, 4648 và đổi mật khẩu để kiểm chứng credential cũ không còn sử dụng được.
Kết quả: PASS.

### TH4 - Persistence và Listener
Sử dụng Sysmon, Autoruns và Process Explorer để kiểm tra persistence.
Tạo:
- LAB3_Run_Demo
- LAB3_Persistence_Demo
Sau đó tạo HTTP listener tại:
127.0.0.1:8080
và xác định process python.exe đang sở hữu port.
Kết quả: PASS.

### TH5 - HTTP và HTTPS/TLS
Sử dụng Wireshark để so sánh HTTP và HTTPS.
Với HTTP, có thể đọc được Request URI chứa chuỗi TRAINING_ONLY.
Với HTTPS/TLS trên port 443, dữ liệu ứng dụng không đọc trực tiếp được như HTTP.
Kết quả: PASS.

### TH6 - DoS, DDoS và Mail Bombing
Chạy local_load_test.py với target:
127.0.0.1:8080
Phân tích ddos_sample.csv để nhận diện nhiều Source IP và phân tích mailbomb_sample.csv để phát hiện sender có số lượng email bất thường.
Kết quả: PASS.

### TH7 - Social Engineering và Phishing
Phân tích phishing_email.txt và social_engineering_cases.csv.
Các dấu hiệu chính gồm:
- Tạo cảm giác khẩn cấp
- Display name đáng tin giả
- Domain cần xác minh
- Reply-To khác From
- Yêu cầu truy cập link hoặc cung cấp credential
Các dạng Social Engineering gồm:
Phishing, Spear Phishing, Watering Hole, Pretexting, Baiting và Quid Pro Quo.
Kết quả: PASS.

## 4. Cleanup
Sau khi hoàn thành bài lab đã thực hiện:
- Xóa LAB3_Run_Demo
- Xóa LAB3_Persistence_Demo
- Dừng listener port 8080
- Xóa tài khoản lab3user
- Kiểm tra Defender vẫn hoạt động
- Thu Autoruns sau cleanup
- Tính SHA-256 cho các file Evidence
Kết quả: PASS.

## 5. Một số lỗi gặp phải và cách khắc phục
- Sysmon không nằm trong C:\LAB3\Tools\Sysmon mà nằm tại C:\Windows\Sysmon.exe, nên sử dụng đúng đường dẫn thực tế.
- Autoruns không thấy LAB3_Run_Demo do đang bật Hide Microsoft Entries/Hide Windows Entries, khắc phục bằng cách bỏ chọn và Refresh.
- Wireshark không thấy TLS/443 do capture nhầm loopback interface, khắc phục bằng cách chuyển sang Ethernet.
- runas có lúc lỗi xác thực, khắc phục bằng cách kiểm tra lại tài khoản lab3user, mật khẩu và Secondary Logon.
- Khi tạo evidence_sha256.csv bị lỗi vì file đang được ghi cũng bị đưa vào danh sách hash. Khắc phục bằng cách loại evidence_sha256.csv khỏi đầu vào của Get-FileHash.
Toàn bộ bài thực hành được thực hiện trong phạm vi VM/localhost và không thực hiện DDoS, Mail Bombing, Spoofing hoặc MITM chủ động ra bên ngoài.
