# Demo Service

## Overview

Demo Service cung cấp các API cho phép hệ thống khác gửi và gửi lại tin nhắn SMS thông qua một giao diện REST thống nhất.

Service được thiết kế để:

* Gửi SMS đến một số điện thoại.
* Gửi lại (resend) một SMS đã được gửi trước đó.
* Tích hợp dễ dàng với các hệ thống nội bộ và đối tác.
* Hỗ trợ xác thực bằng JWT Bearer Token.

---

# Architecture

```
+-------------+
| Client App  |
+------+------+
       |
       | HTTPS + JWT
       |
+------v------+
| SMS Service |
+------+------+
       |
       |
+------v----------------+
| SMS Gateway Provider  |
+-----------------------+
```

---

# Base URL

```
https://<host>/public/all
```

Ví dụ:

```
https://api.example.com/public/all
```

---

# Authentication

Tất cả API yêu cầu Bearer Token.

Header:

```http
Authorization: Bearer <JWT_TOKEN>
```

---

# Content Type

Request

```http
Content-Type: application/json
```

Response

```http
Content-Type: application/json
```

---

# APIs

## 1. Send SMS

Gửi một tin nhắn SMS mới.

### Endpoint

```
POST /public/all/send-sms
```

### Request Body

| Field        | Type     | Required | Description            |
| ------------ | -------- | -------- | ---------------------- |
| phoneNumber  | string   | Yes      | Số điện thoại nhận SMS |
| message      | string   | Yes      | Nội dung tin nhắn      |
| senderId     | string   | No       | Tên hiển thị người gửi |
| scheduleTime | datetime | No       | Thời gian gửi hẹn giờ  |

### Example Request

```json
{
  "phoneNumber": "0912345678",
  "message": "Your OTP is 123456",
  "senderId": "MYAPP"
}
```

### Example Response

```json
{
  "success": true,
  "messageId": "SMS-202607010001",
  "status": "SENT",
  "message": "SMS sent successfully"
}
```

---

## 2. Resend SMS

Gửi lại một tin nhắn đã tồn tại.

### Endpoint

```
POST /public/all/resend-sms
```

### Request Body

| Field     | Type   | Required | Description                      |
| --------- | ------ | -------- | -------------------------------- |
| messageId | string | Yes      | Mã giao dịch của SMS cần gửi lại |

### Example Request

```json
{
  "messageId": "SMS-202607010001"
}
```

### Example Response

```json
{
  "success": true,
  "messageId": "SMS-202607010002",
  "status": "SENT",
  "message": "SMS resent successfully"
}
```

---

# Response Codes

| HTTP Code | Description                    |
| --------- | ------------------------------ |
| 200       | Request được xử lý thành công  |
| 400       | Dữ liệu đầu vào không hợp lệ   |
| 401       | Authentication thất bại        |
| 403       | Không có quyền truy cập        |
| 404       | Không tìm thấy SMS cần gửi lại |
| 500       | Lỗi hệ thống                   |

---

# Response Model

```json
{
  "success": true,
  "messageId": "SMS-202607010001",
  "status": "SENT",
  "message": "SMS sent successfully"
}
```

| Field     | Description      |
| --------- | ---------------- |
| success   | Kết quả xử lý    |
| messageId | Mã giao dịch SMS |
| status    | Trạng thái xử lý |
| message   | Thông báo trả về |

---

# Error Response

```json
{
  "timestamp": "2026-07-01T10:15:30Z",
  "status": 400,
  "error": "Bad Request",
  "code": "SMS-001",
  "message": "Invalid phone number",
  "path": "/public/all/send-sms"
}
```

---

# Operational Notes

* Chỉ chấp nhận các số điện thoại hợp lệ theo định dạng của hệ thống.
* Nội dung SMS có thể bị giới hạn độ dài theo cấu hình của nhà cung cấp SMS Gateway.
* `messageId` là duy nhất cho mỗi lần gửi và có thể dùng để tra cứu hoặc gửi lại.
* API `resend-sms` chỉ hoạt động với các `messageId` còn tồn tại trong hệ thống.
* Mọi request và response đều được ghi log để phục vụ việc theo dõi và xử lý sự cố.

---

# OpenAPI Specification

OpenAPI specification được công bố cùng service và có thể sử dụng để:

* Sinh client SDK.
* Import vào Postman hoặc Insomnia.
* Hiển thị trên Swagger UI.
* Tích hợp với Backstage API Catalog.

---

# Version History

| Version | Date       | Description     |
| ------- | ---------- | --------------- |
| 1.0.0   | 2026-07-01 | Initial release |
