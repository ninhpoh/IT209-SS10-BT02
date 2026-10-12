# Bài 2: Đóng gói ứng dụng Web Backend bằng Single-stage Dockerfile

## 1. Mục tiêu

- Xây dựng ứng dụng REST API đơn giản bằng Node.js và Express.
- Viết Dockerfile theo mô hình Single-stage Build.
- Đóng gói ứng dụng thành Docker Image.
- Khởi chạy container và ánh xạ cổng `8080` trên máy host vào cổng `3000` trong container.
- Kiểm tra trạng thái container, log và phản hồi HTTP.

## 2. Công nghệ sử dụng

- Docker Desktop
- Visual Studio Code
- Node.js 18 Alpine
- Express.js 4.18.2
- Windows PowerShell

## 3. Cấu trúc thư mục

```text
ex2/
├── Dockerfile
├── .dockerignore
├── package.json
├── package-lock.json
├── server.js
└── README.md
```

## 4. Mã nguồn ứng dụng

### File `package.json`

Khai báo thông tin ứng dụng, lệnh khởi chạy và thư viện Express.

### File `server.js`

Xây dựng REST API tại endpoint `/`. API trả về JSON:

```json
{
  "status": "success",
  "message": "Hello from Docker Single-stage!"
}
```

## 5. Nội dung Dockerfile

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install --omit=dev

COPY server.js ./

ENV PORT=3000

EXPOSE 3000

CMD ["node", "server.js"]
```

### Giải thích các chỉ thị

| Chỉ thị | Chức năng |
|---|---|
| `FROM` | Chọn image Node.js 18 Alpine |
| `WORKDIR` | Thiết lập thư mục làm việc `/app` |
| `COPY` | Sao chép file từ máy host vào image |
| `RUN` | Cài đặt thư viện Express |
| `ENV` | Thiết lập biến môi trường `PORT=3000` |
| `EXPOSE` | Khai báo cổng ứng dụng sử dụng |
| `CMD` | Khởi chạy ứng dụng bằng exec form |

Đây là Single-stage Dockerfile vì các bước cài đặt và chạy ứng dụng sử dụng cùng một stage.

## 6. Build Docker Image

Mở terminal trong VS Code tại thư mục chứa Dockerfile và chạy:

```powershell
docker build -t hw-backend:v1 .
```

Kiểm tra image:

```powershell
docker images
```

Image được tạo có tên `hw-backend` và tag `v1`.

## 7. Khởi chạy container

Chạy container với tên `app-single-stage`:

```powershell
docker run -d --name app-single-stage -p 8080:3000 hw-backend:v1
```

Trong đó:

- `-d`: chạy container ở chế độ nền.
- `--name app-single-stage`: đặt tên container.
- `-p 8080:3000`: ánh xạ cổng host `8080` vào cổng container `3000`.
- `hw-backend:v1`: image sử dụng để tạo container.

## 8. Kiểm tra container

### Kiểm tra trạng thái

```powershell
docker ps
```

Container `app-single-stage` cần có trạng thái `Up`.

### Kiểm tra log

```powershell
docker logs app-single-stage
```

Kết quả mong đợi:

```text
Server listening on port 3000
```

### Kiểm tra REST API

```powershell
curl.exe -i http://localhost:8080/
```

Kết quả mong đợi:

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Nội dung phản hồi:

```json
{
  "status": "success",
  "message": "Hello from Docker Single-stage!"
}
```

Có thể mở trình duyệt và truy cập:

http://localhost:8080/

## 9. Xử lý lỗi thường gặp

### Lỗi trùng tên container

Nếu Docker báo container `app-single-stage` đã tồn tại, xóa container cũ:

```powershell
docker rm -f app-single-stage
```

Sau đó chạy lại:

```powershell
docker run -d --name app-single-stage -p 8080:3000 hw-backend:v1
```

### Container không chạy

Kiểm tra log:

```powershell
docker logs app-single-stage
```

### Không truy cập được API

Kiểm tra trạng thái container và cổng:

```powershell
docker ps
docker port app-single-stage
```

## 10. Kết luận

Đã thực hành đóng gói ứng dụng REST API Node.js/Express bằng Single-stage Dockerfile. Docker Image `hw-backend:v1` được sử dụng để tạo container `app-single-stage`, cung cấp API qua cổng `8080` của máy host.

Kết quả được xác nhận bằng trạng thái container, log ứng dụng và phản hồi HTTP `200 OK`.

## 11. Minh chứng thực hành

Bổ sung ảnh chụp màn hình các kết quả sau:

1. Dockerfile trong VS Code.
2. Kết quả build image `hw-backend:v1`.
3. Kết quả `docker ps` cho thấy container `app-single-stage` đang chạy.
4. Kết quả `docker logs app-single-stage`.
5. Kết quả `curl.exe -i http://localhost:8080/` trả về HTTP `200 OK`.
