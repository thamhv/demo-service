# Tài liệu tích hợp Wallet Refund API

Tài liệu này cung cấp các hướng dẫn xử lý lỗi nghiệp vụ hoàn tiền nâng cao cho các đối tác ví điện tử.

## 1. Endpoint Hoàn Tiền Thử Lại (Retry Dispute)

Mã nguồn xử lý cho phép các đối tác yêu cầu hoàn tiền lại khi một giao dịch bị tranh chấp (Dispute).

* **Đường dẫn (Path):** `/v1/refund/retry-dispute`
* **Phương thức:** `POST`

### Tham số yêu cầu (Request Body)

| Tên trường | Kiểu dữ liệu | Bắt buộc | Mô tả |
| :--- | :--- | :--- | :--- |
| `refund_id` | String | Có | Định danh của lệnh hoàn tiền ban đầu |
| `partner_error_code` | String | Có | Mã lỗi nhận về từ phía hệ thống ngân hàng đối tác |

> **Lưu ý nghiệp vụ:** Khi nhận được trường `partner_error_code`, hệ thống sẽ tự động routing sang gateway dự phòng để thực hiện lệnh hoàn trả tiền ngay lập tức mà không cần tạo ticket đối soát thủ công.