# Khóa học AI Testing Manual – Tài liệu kiểm thử

Repo lưu tài liệu thực hành cho khóa học **kiểm thử thủ công có hỗ trợ AI**. Hệ thống được kiểm thử là **Perfex CRM – Anh Tester Demo**, một website demo dùng để luyện tập: <https://crm.anhtester.com>.

Hiện tại repo tập trung vào module **Login** (`/authentication/login`) và bắt đầu có phần **Dashboard**.

## Cấu trúc thư mục

```
.
├── docs/
│   ├── SRS_Login_Module.md        # Đặc tả yêu cầu phần mềm (SRS) module Login
│   └── testcases/
│       ├── TC_Login.txt           # Test case Login (bản nháp, các bước ngắn gọn)
│       └── TC_Dashboard.txt       # Test case Dashboard (chưa có nội dung)
├── reqs/
│   └── requirements_login.md      # Yêu cầu module Login (chưa có nội dung)
├── .gitignore
├── LICENSE
└── README.md
```

## Nội dung chính

| Tài liệu | Mô tả | Trạng thái |
|---|---|---|
| [docs/SRS_Login_Module.md](docs/SRS_Login_Module.md) | SRS module Login: giao diện, 8 yêu cầu chức năng, quy tắc nghiệp vụ, yêu cầu phi chức năng, dữ liệu test đề xuất, ma trận truy vết | Bản nháp v1.0 |
| [docs/testcases/TC_Login.txt](docs/testcases/TC_Login.txt) | Test case Login | Bản nháp, mới có các bước chính |
| [docs/testcases/TC_Dashboard.txt](docs/testcases/TC_Dashboard.txt) | Test case Dashboard | Chưa viết |
| [reqs/requirements_login.md](reqs/requirements_login.md) | Yêu cầu Login | Chưa viết |

> **Lưu ý về SRS:** SRS được lập từ khảo sát giao diện trang Login và Forgot Password. Những hành vi sau khi submit form (thông báo lỗi, trang sau đăng nhập, Remember me, chống brute-force) được đánh dấu ⚠️ *Cần xác minh*, vì chưa được kiểm chứng trực tiếp.

## Cách sử dụng

1. Đọc [SRS Login](docs/SRS_Login_Module.md) để nắm phạm vi và yêu cầu của module.
2. Dựa trên SRS và ma trận truy vết (mục 8) để viết hoặc mở rộng test case trong `docs/testcases/`.
3. Thực thi test trên <https://crm.anhtester.com/authentication/login>, sau đó cập nhật các mục ⚠️ trong SRS bằng kết quả thực tế.

## Bảo mật

- File `.env` được liệt kê trong `.gitignore` và **không** được đưa lên git. Không commit thông tin đăng nhập, token hay khóa API vào repo.
- Đây là site demo công khai dành cho luyện tập; chỉ kiểm thử trên môi trường này.

## Giấy phép

Phát hành theo [MIT License](LICENSE).
