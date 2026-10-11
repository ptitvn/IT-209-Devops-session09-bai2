# BÁO CÁO BÀI TẬP: BÀI 2
## Khởi chạy Web Server Nginx với Port Mapping

---

### 1. Thông tin chung
- **Môn học:** IT-209 - DevOps
- **Session:** Session 09 - Containerization & Docker
- **Bài thực hành:** Bài 2 - Khởi chạy Web Server Nginx với Port Mapping
- **Đường dẫn nộp bài GitHub:** `homework/session_09/ex2/README.md`
- **Mục tiêu:**
  - Chạy thử một Web Server Nginx dạng container ở chế độ ngầm (`-d`).
  - Ánh xạ cổng mạng (`-p 8080:80`) từ máy host sang container.
  - Kiểm tra kết nối và phản hồi từ Nginx bằng lệnh `curl`.
  - Thực hành dọn dẹp container (`stop` và `rm`) sau khi kiểm tra xong.

---

### 2. Yêu cầu & Ràng buộc kỹ thuật

| Tham số / Yêu cầu | Giá trị thiết lập | Mô tả chi tiết |
| :--- | :--- | :--- |
| **Base Image** | `nginx:alpine` | Sử dụng phiên bản Nginx xây dựng trên nền tảng Alpine Linux siêu nhẹ. |
| **Container Name** | `my-web` | Tên định danh dễ quản lý thay vì Docker sinh tên ngẫu nhiên. |
| **Port Mapping** | `-p 8080:80` | Ánh xạ cổng `8080` của máy tính cá nhân (Host) vào cổng `80` của container Nginx. |
| **Mode** | `-d` (Detached) | Khởi chạy container ở chế độ nền (ngầm), không chiếm giữ Terminal. |
| **Kiểm tra truy vấn** | `curl http://localhost:8080` | Gửi HTTP request kiểm tra phản hồi từ web server. |
| **Kết quả mong đợi** | Chứa dòng chữ `"Welcome to nginx!"` | Xác nhận Nginx phục vụ trang web thành công. |
| **Dọn dẹp** | `docker stop my-web && docker rm my-web` | Dừng và xóa container để giải phóng tài nguyên hệ thống. |

---

### 3. Các bước thực hiện và Kết quả thực tế

#### Bước 1: Khởi chạy container Nginx ngầm với Port Mapping
Lệnh thực thi:
```bash
docker run -d -p 8080:80 --name my-web nginx:alpine
```

Kết quả đầu ra Terminal:
```text
Unable to find image 'nginx:alpine' locally
alpine: Pulling from library/nginx
Digest: sha256:df221db836e1754089190208cee7eeda94f233197056426eda74a43ab1abeac2
Status: Downloaded newer image for nginx:alpine
afde8183f5bdb75b0feebaf75d578bff1436e4b18898a07ed3daaad28ac8114b
```

Kiểm tra trạng thái container đang hoạt động bằng `docker ps`:
```bash
docker ps
```
```text
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                                     NAMES
afde8183f5bd   nginx:alpine   "/docker-entrypoint.…"   52 seconds ago   Up 52 seconds   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   my-web
```
*Nhận xét:* Container `my-web` đang ở trạng thái `Up`, cổng `8080` trên máy host đã được ánh xạ vào cổng `80/tcp` trong container.

---

#### Bước 2: Kiểm tra trang web bằng lệnh `curl`
Lệnh thực thi:
```bash
curl http://localhost:8080
```

Kết quả trả về mã HTML:
```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

*Đánh giá:* Lệnh `curl` trả về mã HTML có dòng chữ **`Welcome to nginx!`**, khớp 100% với kết quả mong đợi của bài toán.

---

#### Bước 3: Dọn dẹp container (Stop & Remove)
Lệnh thực thi:
```bash
docker stop my-web && docker rm my-web
```

Kết quả đầu ra Terminal:
```text
my-web
my-web
```

Kiểm tra xác nhận container đã được dọn dẹp sạch sẽ:
```bash
docker ps -a --filter "name=my-web"
```
```text
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
*Nhận xét:* Không còn container `my-web` nào tồn tại trên hệ thống, tài nguyên đã được giải phóng hoàn toàn.

---

### 4. Giải thích chi tiết các lệnh & cờ tham số (Flags)

1. **`docker run`**: Lệnh khởi tạo và chạy một container mới từ image chỉ định.
2. **`-d` (`--detach`)**: Chạy container ở chế độ background (chạy ngầm). Terminal sẽ lập tức trả về Container ID dài 64 ký tự và không bị khóa tiến trình.
3. **`-p 8080:80` (`--publish`)**: Cấu hình Port Forwarding (ánh xạ cổng). 
   - `8080`: Cổng trên Host OS (máy cá nhân).
   - `80`: Cổng lắng nghe mặc định của Nginx bên trong container.
   - Khi có request gửi tới `http://localhost:8080`, Docker Proxy sẽ chuyển tiếp request này vào cổng `80` của container.
4. **`--name my-web`**: Gán tên tường minh cho container là `my-web`, giúp thao tác kiểm tra, dừng, xóa dễ dàng hơn qua tên thay vì chuỗi ID ngẫu nhiên.
5. **`nginx:alpine`**: Image được tải từ Docker Hub. Hậu tố `:alpine` chỉ phiên bản siêu nhỏ gọn, tiết kiệm băng thông và tài nguyên CPU/RAM.
6. **`docker stop my-web`**: Gửi tín hiệu `SIGTERM` (sau 10s nếu không phản hồi sẽ gửi `SIGKILL`) để tắt các tiến trình trong container một cách an toàn.
7. **`docker rm my-web`**: Xóa container khỏi Docker daemon để giải phóng bộ nhớ lưu trữ và tên container.

---

### 5. Kết luận
- Toàn bộ các yêu cầu từ khởi chạy nền, ánh xạ cổng, kiểm tra HTTP response bằng `curl` đến dọn dẹp container đều được thực hiện chính xác và đạt kết quả mong đợi.
