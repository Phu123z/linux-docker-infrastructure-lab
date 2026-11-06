# 🐧 Linux Infrastructure & Docker Deployment Lab

Bài lab cá nhân thực hành cài đặt Ubuntu Server trên môi trường ảo hóa VirtualBox, cấu hình mạng và triển khai Web Server Nginx + Redis bằng Docker Compose.

---

## 📌 Mục tiêu bài lab

- Làm quen với việc quản trị hệ điều hành **Ubuntu Server** không giao diện (CLI).
- Cấu hình mạng ảo (**NAT & Port Forwarding**) để máy chủ thật (Windows) kết nối được SSH và HTTP vào máy ảo.
- Sử dụng **Docker & Docker Compose** để đóng gói và chạy ứng dụng thay vì cài trực tiếp lên OS.
- Kiểm tra log, giám sát tài nguyên và xử lý các lỗi kết nối mạng thực tế.

---

## 🛠️ Mô hình triển khai (Architecture)

```text
[ Máy host Windows ]
    │ 
    ├── (SSH: ssh phu123x@127.0.0.1 -p 2222) ──► [ VirtualBox NAT ] ──► Ubuntu Server (Port 22)
    │
    └── (HTTP: [http://127.0.0.1:8081](http://127.0.0.1:8081)) ───────► [ VirtualBox NAT ] ──► Docker Nginx Container (Port 80)

🚀 Các bước đã thực hiện
1. Dựng máy ảo & Cấu hình Mạng

    Cài đặt Ubuntu Server 26.04 LTS trên Oracle VirtualBox.

    Cấu hình Port Forwarding trong VirtualBox:

        SSH: Forward cổng 2222 trên Windows vào cổng 22 của Ubuntu.

        HTTP Web: Forward cổng 8080 / 8081 trên Windows vào cổng Web của Ubuntu.

    Truy cập điều khiển từ xa bằng Windows Terminal / PowerShell thông qua SSH.

2. Quản trị Hệ thống Linux

    Phân quyền thư mục web /var/www/html bằng chown và chmod.

    Tạo user mới, phân quyền sudo và quản lý các dịch vụ hệ thống qua systemctl.

    Theo dõi thông số CPU/RAM/Disk bằng các lệnh top, free -h, df -h.

3. Triển khai Docker Compose (Nginx + Redis)

    Cài đặt Docker Engine và Docker Compose trên Ubuntu.

    Tạo file docker-compose.yml chạy 2 dịch vụ độc lập:

        Nginx (Alpine): Web Server nhận HTTP traffic (port 8081:80).

        Redis (Alpine): In-memory Database / Cache Server (port 6379:6379).

YAML

services:
  web:
    image: nginx:alpine
    ports:
      - "8081:80"
  cache:
    image: redis:alpine
    ports:
      - "6379:6379"

⚠️ Các lỗi đã gặp & Cách khắc phục (Troubleshooting)

Trong quá trình làm bài lab, mình có gặp một số sự cố thực tế và đã xử lý như sau:

    Lỗi Connection Refused khi SSH:

        Nguyên nhân: VirtualBox chưa nhận quy tắc Port Forwarding do máy ảo đang chạy.

        Khắc phục: Tắt máy ảo (sudo poweroff), kiểm tra lại Rule Port Forwarding 2222 -> 22 rồi bật lại.

    Lỗi Invoke-WebRequest / Script Execution Warning trên PowerShell:

        Nguyên nhân: PowerShell trên Windows coi curl là alias của Invoke-WebRequest nên yêu cầu xác nhận bảo mật.

        Khắc phục: Đổi sang gõ lệnh curl.exe http://127.0.0.1:8081 hoặc thêm tham số -UseBasicParsing.

    Lỗi gõ lệnh trực tiếp trong màn hình ảo VirtualBox bị mất phím/không paste được:

        Khắc phục: Chuyển sang SSH 100% từ Windows Terminal để dễ dàng thao tác copy/paste lệnh.
📸 Minh chứng kết quả :
Kiểm tra Container chạy ngầm (docker ps):
minhchung1.png
Test HTTP Response 200 OK từ PowerShell:
minhchung2.png
