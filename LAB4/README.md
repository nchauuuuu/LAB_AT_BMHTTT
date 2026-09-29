# LAB 4 - KHẢO SÁT VÀ ĐÁNH GIÁ BỀ MẶT MẠNG BẰNG NMAP

## 1. Thông tin sinh viên
- Họ và tên: Nguyễn Lê Ngọc Châu
- MSSV: 1150070003
- Lớp: 11_TTMT
- Tên Lab: LAB 4 - Khảo sát và đánh giá bề mặt mạng bằng Nmap
  
---

## 2. Phiên bản và môi trường thực hành
- Phần mềm ảo hóa: VMware Workstation
- Máy quét: Kali Linux
- Máy đích: Metasploitable 2
- Máy kiểm thử Hardening: Windows 11
- Công cụ chính: Nmap 7.99
- Mạng thực hành: VMnet1 - Host-Only
- Subnet: 172.16.16.0/24
  
### Địa chỉ IP
| Máy | Vai trò | IP |
|---|---|---|
| Kali Linux | Máy quét | 172.16.16.128 |
| Metasploitable 2 | Máy đích | 172.16.16.129 |
| Windows 11 | Máy kiểm thử Hardening | 172.16.16.130 |

---

## 3. Cách dựng môi trường
1. Tạo hoặc import các máy ảo Kali Linux, Metasploitable 2 và Windows 11 vào VMware Workstation.
2. Cấu hình các máy ảo sử dụng mạng VMnet1 - Host-Only.
3. Cấu hình subnet VMnet1 là `172.16.16.0/24`.
4. Khởi động Kali Linux và Metasploitable 2.
5. Kiểm tra địa chỉ IP:
   - Kali Linux: `ip -br addr`
   - Metasploitable 2: `ifconfig`
6. Kiểm tra kết nối giữa các máy bằng `ping`.
7. Sử dụng Kali Linux làm máy quét Nmap.
8. Chỉ thực hiện quét trên các máy ảo trong mạng Host-Only của bài lab.
   
---

## 4. Các tình huống đã thực hiện
### 4.1. Host Discovery
Thực hiện phát hiện các host đang hoạt động trong mạng:
`sudo nmap -sn 172.16.16.0/24`
Kết quả: xác định được các host đang hoạt động trong mạng lab.

**Trạng thái: PASS**

---

### 4.2. TCP SYN Scan
Thực hiện SYN scan để xác định các cổng TCP trên Metasploitable 2.
Kết quả: phát hiện nhiều cổng ở trạng thái `open`.

**Trạng thái: PASS**

---

### 4.3. TCP Connect Scan
Thực hiện TCP Connect Scan và so sánh với SYN Scan.
Kết quả: xác định được các dịch vụ TCP đang lắng nghe trên máy đích.

**Trạng thái: PASS**

---

### 4.4. FIN / Xmas / NULL Scan
Thực hiện các kỹ thuật FIN, Xmas và NULL Scan.
Kết quả: quan sát được các trạng thái như `open|filtered` và sự khác biệt về cách phản hồi của máy đích.

**Trạng thái: PASS**

---

### 4.5. ACK Scan
Thực hiện ACK Scan để khảo sát chính sách lọc.
Kết quả: quan sát được trạng thái `unfiltered`/`filtered` tùy cổng và chính sách lọc.

**Trạng thái: PASS**

---

### 4.6. UDP Scan
Thực hiện quét UDP có kiểm soát trên Metasploitable 2.
Kết quả: quan sát được các trạng thái `open`, `closed` và `open|filtered`.

**Trạng thái: PASS**

---

### 4.7. Service Version Detection
Sử dụng tùy chọn `-sV` để nhận diện dịch vụ và phiên bản.
Một số dịch vụ quan sát được gồm:
- Apache HTTP Server
- OpenSSH
- Samba
- MySQL
- PostgreSQL
- FTP
- Telnet
- VNC
- IRC
  
**Trạng thái: PASS**
  
---

### 4.8. OS Detection
Sử dụng tùy chọn `-O` để nhận diện hệ điều hành.
Kết quả: Nmap xác định máy Metasploitable 2 thuộc họ Linux.
**Trạng thái: PASS**

---

### 4.9. Aggressive Scan
Sử dụng tùy chọn `-A` để tổng hợp thông tin về:
- Hệ điều hành
- Phiên bản dịch vụ
- NSE scripts
- Network distance
- 
**Trạng thái: PASS**
  
---

### 4.10. NSE - SMB OS Discovery
Thực hiện NSE script `smb-os-discovery`.
Kết quả thu được:
- OS: Unix
- Samba: 3.0.20-Debian
- Computer name: metasploitable
- Domain: localdomain
- FQDN: metasploitable.localdomain

**Trạng thái: PASS**

---

### 4.11. NSE - MS17-010
Thực hiện script `smb-vuln-ms17-010` trên máy Metasploitable 2.
Kết quả không hiển thị kết luận `VULNERABLE`, do đó không kết luận máy đích chắc chắn tồn tại lỗ hổng MS17-010 chỉ dựa trên lần kiểm tra này.
**Trạng thái: PASS - Đã thực hiện kiểm tra**

---

### 4.12. Xuất kết quả Nmap
Kết quả quét được lưu thành các tệp:
- `ket_qua.txt`
- `ket_qua.xml`
- `ket_qua.html`
- `smb.txt`
XML được chuyển sang HTML bằng `xsltproc` để thuận tiện cho việc xem báo cáo.

**Trạng thái: PASS**

---

## 5. Before / After Hardening
Máy Windows 11 có địa chỉ:
`172.16.16.130`

### Before Hardening
Một HTTP server thử nghiệm được mở trên TCP port 8000 và firewall được cấu hình tạm thời cho phép kết nối.
Kết quả quét từ Kali:
`8000/tcp open http-alt`

### Thực hiện Hardening
- Dừng HTTP server thử nghiệm.
- Xóa firewall rule `LAB4 HTTP 8000`.
- Kiểm tra lại port 8000 trên Windows.
- Quét lại từ Kali bằng cùng phép kiểm tra TCP/8000.

### After Hardening
Kết quả:
`8000/tcp filtered http-alt`

### So sánh
| Nội dung | Before | After |
|---|---|---|
| TCP/8000 | open | filtered |
| HTTP test server | Đang hoạt động | Đã dừng |
| Firewall rule 8000 | Allow | Đã xóa |
| Bề mặt dịch vụ | Có thể truy cập | Đã giảm |

**Trạng thái: PASS**

---

## 6. Lỗi gặp phải và cách khắc phục
### Lỗi 1: Kali không ping được Metasploitable 2
**Hiện tượng:**
`Destination Host Unreachable`

**Nguyên nhân:**
Hai máy ảo chưa giao tiếp đúng qua cùng mạng Host-Only.

**Cách khắc phục:**
- Kiểm tra Network Adapter của hai VM.
- Đặt cả Kali và Metasploitable 2 về `VMnet1 (Host-Only)`.
- Kiểm tra lại địa chỉ IP.
- Sau khi cấu hình đúng, Kali ping thành công `172.16.16.129`.

**Kết quả: PASS**

---

### Lỗi 2: Không thể chạy đồng thời nhiều máy ảo
**Hiện tượng:**
VMware báo:
`Not enough physical memory is available to power on this virtual machine`

**Nguyên nhân:**
RAM máy thật không đủ để chạy đồng thời Kali, Metasploitable 2 và Windows 11.
**Cách khắc phục:**
- Chỉ chạy các VM cần thiết tại từng thời điểm.
- Tắt Metasploitable 2 khi chuyển sang kiểm thử Windows 11.
- Giảm lượng RAM cấp cho VM nếu cần.
  
**Kết quả: PASS**

---

### Lỗi 3: TCP/8000 ban đầu ở trạng thái filtered
**Hiện tượng:**
Kali quét Windows 11 nhận:
`8000/tcp filtered http-alt`

**Nguyên nhân:**
Windows Firewall chưa cho phép kết nối inbound đến TCP/8000.

**Cách khắc phục:**
- Chạy HTTP server thử nghiệm trên Windows.
- Tạo inbound firewall rule cho TCP/8000.
- Kiểm tra lại trạng thái LISTENING.
- Quét lại từ Kali.

Kết quả Before Hardening:
`8000/tcp open http-alt`
**Kết quả: PASS**

---

### Lỗi 4: Cú pháp chạy Python HTTP Server bị sai
**Hiện tượng:**
Python báo:
`argument port: invalid int value`

**Nguyên  nhân:**
Nhập sai cú pháp/giá trị port khi khởi động HTTP server.
**Cách khắc phục:**
Nhập lại đúng port `8000` và kiểm tra trạng thái LISTENING trước khi quét.
**Kết quả: PASS**

---

## 7. Kết quả tổng thể
| Hạng mục | Kết quả |
|---|---|
| Cấu hình mạng Host-Only | PASS |
| Xác định IP | PASS |
| Kiểm tra kết nối | PASS |
| Host Discovery | PASS |
| TCP Scan | PASS |
| UDP Scan | PASS |
| Service Detection | PASS |
| OS Detection | PASS |
| NSE SMB | PASS |
| Xuất kết quả | PASS |
| Before/After Hardening | PASS |

**KẾT QUẢ LAB 4: PASS**

---

## 8. Ghi chú

Bài thực hành được thực hiện hoàn toàn trên các máy ảo trong mạng Host-Only phục vụ mục đích học tập và nghiên cứu. Không thực hiện quét đối với hệ thống công cộng hoặc hệ thống không được cấp quyền.
