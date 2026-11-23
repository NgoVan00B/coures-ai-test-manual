# SRS – Module Login (Perfex CRM | Anh Tester Demo)

| Thông tin | Giá trị |
|---|---|
| Hệ thống | Perfex CRM – Anh Tester Demo |
| URL | https://crm.anhtester.com/authentication/login |
| Module | Authentication – Login |
| Phiên bản tài liệu | 1.0 (Draft) |
| Ngày lập | 2026-11-23 |
| Nguồn thông tin | Khảo sát giao diện/DOM thực tế của trang Login và Forgot Password (không submit form) |

> **Quy ước đánh dấu**
> - ✅ **Đã xác nhận**: quan sát trực tiếp trên giao diện/DOM.
> - ⚠️ **Cần xác minh**: suy luận theo hành vi mặc định của Perfex CRM (CodeIgniter), chưa test thực tế vì chưa submit form.

---

## 1. Giới thiệu

### 1.1 Mục đích
Tài liệu mô tả yêu cầu chức năng và phi chức năng của module **Login**, làm cơ sở cho việc phân tích, thiết kế test case và kiểm thử thủ công.

### 1.2 Phạm vi
**Trong phạm vi:**
- Màn hình Login (`/authentication/login`)
- Chọn ngôn ngữ ngay trên màn hình Login
- Tùy chọn "Remember me"
- Liên kết "Forgot Password?" và màn hình Forgot Password (`/authentication/forgot_password`)
- Điều hướng ở header/footer của trang Login

**Ngoài phạm vi:**
- Quản lý người dùng/phân quyền, Dashboard sau đăng nhập
- Đăng nhập Customer Portal và đăng ký khách hàng
- Màn hình Reset Password (qua link email)
- Xác thực hai lớp (2FA) – chưa quan sát thấy trên giao diện

### 1.3 Thuật ngữ

| Thuật ngữ | Giải thích |
|---|---|
| SRS | Software Requirements Specification |
| CSRF | Cross-Site Request Forgery |
| Remember me | Duy trì phiên đăng nhập sau khi đóng trình duyệt |
| FR / NFR | Functional / Non-functional Requirement |

### 1.4 Đối tượng sử dụng

| Vai trò | Mô tả |
|---|---|
| Admin / Staff | Nhân viên đăng nhập vào trang quản trị CRM |
| Khách (chưa đăng nhập) | Truy cập trang Login, Knowledge Base, Forgot Password |

---

## 2. Mô tả tổng quan

### 2.1 Bối cảnh sản phẩm
Login là cổng xác thực đầu tiên của Perfex CRM. Người dùng nhập Email và Password để truy cập vào hệ thống quản trị.

### 2.2 Giả định và ràng buộc
- Tài khoản do hệ thống/Admin tạo sẵn; **không có chức năng tự đăng ký** trên trang Login ✅.
- Form gửi bằng phương thức `POST` tới chính URL Login ✅.
- Form có CSRF token ẩn (`csrf_token_name`) ✅.
- Trang chạy trên HTTPS ✅.

### 2.3 Sơ đồ luồng chính

```
[Truy cập /authentication/login]
        │
        ▼
[Nhập Email + Password (+ chọn Language, Remember me)]
        │ Click "Login"
        ▼
   ┌─ Hợp lệ? ──No──► [Hiển thị thông báo lỗi, ở lại trang Login] ⚠️
   │
  Yes
   ▼
[Tạo phiên đăng nhập → chuyển tới trang quản trị/Dashboard] ⚠️
```

---

## 3. Giao diện màn hình Login (✅ Đã xác nhận)

### 3.1 Bố cục
- **Header**: logo "Perfex CRM | Anh Tester Demo" (link về `/`), menu "Knowledge Base" (`/knowledge-base`), nút "Login" (`/authentication/login`), nút toggle menu trên mobile.
- **Nội dung**: tiêu đề **"Please login"** và form đăng nhập.
- **Footer**: "2026 Copyright Perfex CRM | Anh Tester Demo".

### 3.2 Danh sách field

| # | Thành phần | Label | Loại | name / id | Bắt buộc (HTML) | Ghi chú |
|---|---|---|---|---|---|---|
| 1 | Language | Language | Select (dropdown tìm kiếm) | `language` | Không | Mặc định **English**; 26 ngôn ngữ (xem 3.3) |
| 2 | Email | Email Address | Input `type=text` | `email` | Không (không có `required`) | Không placeholder; không dùng `type=email` |
| 3 | Password | Password | Input `type=password` | `password` | Không (không có `required`) | Che ký tự |
| 4 | Remember me | Remember me | Checkbox | `remember` | Không | Mặc định **không chọn** |
| 5 | Login | Login | Button `submit` | – | – | Gửi form |
| 6 | Forgot Password? | Forgot Password? | Link | – | – | → `/authentication/forgot_password` |
| 7 | CSRF token | – | Hidden | `csrf_token_name` | – | Tự sinh mỗi lần tải trang |

### 3.3 Danh sách ngôn ngữ (✅)
English (mặc định), Chinese, Vietnamese, Indonesia, Bulgarian, Portuguese_br, Swedish, Dutch, Persian, Turkish, Catalan, Spanish, Portuguese, French, Czech, Japanese, Italian, Slovak, Russian, Polish, Greek, Finnish, Norwegian, German, Ukrainian, Romanian.

---

## 4. Yêu cầu chức năng

### FR-LOGIN-01 – Hiển thị trang Login
- **Mô tả**: Khi truy cập `/authentication/login`, hệ thống hiển thị form đăng nhập đầy đủ các thành phần ở mục 3.2.
- **Tiêu chí chấp nhận**:
  - Tiêu đề trình duyệt và heading là "Please login" ✅
  - Ngôn ngữ mặc định là English, Remember me không được chọn ✅
  - Trường Email và Password trống ✅

### FR-LOGIN-02 – Đăng nhập thành công
- **Tiền điều kiện**: Tài khoản tồn tại và đang hoạt động (active).
- **Luồng chính**:
  1. Người dùng nhập Email hợp lệ và Password đúng.
  2. Nhấn **Login**.
  3. Hệ thống xác thực, tạo phiên và chuyển tới trang quản trị (Dashboard) ⚠️.
- **Hậu điều kiện**: Người dùng ở trạng thái đã đăng nhập; truy cập lại trang Login khi đã đăng nhập sẽ được chuyển hướng ⚠️.

### FR-LOGIN-03 – Đăng nhập thất bại
Hệ thống **phải** giữ người dùng ở trang Login và hiển thị thông báo lỗi trong các trường hợp ⚠️:

| Tình huống | Kết quả mong đợi |
|---|---|
| Email sai, Password đúng | Thông báo sai thông tin đăng nhập |
| Email đúng, Password sai | Thông báo sai thông tin đăng nhập |
| Cả hai đều sai / email không tồn tại | Thông báo sai thông tin đăng nhập |
| Để trống Email và/hoặc Password | Thông báo lỗi bắt buộc nhập / sai thông tin |
| Email sai định dạng | Thông báo lỗi |
| Tài khoản bị vô hiệu hóa (inactive) | Thông báo tài khoản không hoạt động |

> Lưu ý: các field không có thuộc tính `required` ở HTML ✅ → việc kiểm tra bắt buộc nhập được thực hiện phía server, không phải trình duyệt.

### FR-LOGIN-04 – Remember me
- Khi tick **Remember me** và đăng nhập thành công, phiên được duy trì sau khi đóng và mở lại trình duyệt ⚠️.
- Khi không tick, phiên kết thúc khi đóng trình duyệt hoặc hết hạn session ⚠️.

### FR-LOGIN-05 – Chọn ngôn ngữ
- Người dùng chọn một ngôn ngữ trong dropdown **Language** (có ô tìm kiếm ✅).
- Ngôn ngữ đã chọn được gửi kèm form (`language`) và dùng làm ngôn ngữ giao diện sau đăng nhập ⚠️.
- Có thể gõ từ khóa để lọc danh sách ngôn ngữ ✅.

### FR-LOGIN-06 – Quên mật khẩu
- Click **Forgot Password?** → chuyển tới `/authentication/forgot_password` ✅.
- Màn hình Forgot Password gồm: heading **"Forgot Password"**, field **Email Address** (`type=email`, name `email`), nút **Submit** ✅.
- Gửi email hợp lệ đã đăng ký: hệ thống gửi hướng dẫn đặt lại mật khẩu qua email ⚠️.
- Gửi email không tồn tại/sai định dạng: hiển thị thông báo lỗi ⚠️.

### FR-LOGIN-07 – Điều hướng
- Logo → trang chủ `/` ✅.
- "Knowledge Base" → `/knowledge-base` ✅ (truy cập không cần đăng nhập).
- Nút "Login" trên header → `/authentication/login` ✅.

### FR-LOGIN-08 – Phím tắt / thao tác bàn phím
- Nhấn **Enter** khi đang ở field Email/Password sẽ submit form ⚠️.
- Thứ tự Tab hợp lý: Language → Email → Password → Remember me → Login → Forgot Password ⚠️.

---

## 5. Quy tắc nghiệp vụ

| Mã | Quy tắc |
|---|---|
| BR-01 | Định danh đăng nhập là **Email Address** (không dùng username). |
| BR-02 | Thông báo lỗi không nên tiết lộ email có tồn tại hay không (chống dò tài khoản) ⚠️. |
| BR-03 | Mật khẩu hiển thị dạng che ký tự, không lưu plain-text. |
| BR-04 | Mỗi lần tải form phải có CSRF token hợp lệ; request thiếu/sai token bị từ chối ⚠️. |
| BR-05 | Hệ thống có thể giới hạn số lần đăng nhập sai liên tiếp (Perfex hỗ trợ cơ chế chặn theo IP) ⚠️. |

---

## 6. Yêu cầu phi chức năng

| Mã | Loại | Yêu cầu |
|---|---|---|
| NFR-01 | Bảo mật | Truyền dữ liệu qua HTTPS ✅; password không xuất hiện trong URL; có CSRF token ✅ |
| NFR-02 | Bảo mật | Chống brute-force/khóa tạm sau nhiều lần sai ⚠️; chống SQL Injection/XSS ở field Email, Password |
| NFR-03 | Bảo mật | Cookie phiên có thuộc tính `HttpOnly`, `Secure` ⚠️ |
| NFR-04 | Hiệu năng | Trang Login tải ≤ 3 giây; đăng nhập phản hồi ≤ 3 giây (mạng thường) |
| NFR-05 | Tương thích | Hoạt động trên Chrome, Firefox, Edge, Safari bản mới |
| NFR-06 | Responsive | Hiển thị tốt trên desktop, tablet, mobile (header có nút toggle navigation ✅) |
| NFR-07 | Khả dụng | Label gắn với input; điều hướng được bằng bàn phím |
| NFR-08 | Đa ngôn ngữ | Hỗ trợ 26 ngôn ngữ ✅ |

---

## 7. Dữ liệu kiểm thử đề xuất

| Loại | Email | Password | Kết quả mong đợi |
|---|---|---|---|
| Hợp lệ | `admin@example.com` | `123456` | Đăng nhập thành công (theo thông tin được cung cấp) |
| Sai password | `admin@example.com` | `wrong123` | Báo lỗi |
| Email không tồn tại | `notexist@example.com` | `123456` | Báo lỗi |
| Email trống | *(trống)* | `123456` | Báo lỗi |
| Password trống | `admin@example.com` | *(trống)* | Báo lỗi |
| Cả hai trống | *(trống)* | *(trống)* | Báo lỗi |
| Email sai định dạng | `admin@` / `admin.com` | `123456` | Báo lỗi |
| Khoảng trắng đầu/cuối | ` admin@example.com ` | `123456` | Ghi nhận hành vi thực tế (trim hay không) |
| Email viết hoa | `ADMIN@EXAMPLE.COM` | `123456` | Ghi nhận hành vi thực tế |
| SQL Injection | `' OR '1'='1` | `' OR '1'='1` | Báo lỗi, không đăng nhập được |
| XSS | `<script>alert(1)</script>` | `123456` | Không thực thi script |
| Chuỗi rất dài | 256+ ký tự | 256+ ký tự | Báo lỗi, không crash |

---

## 8. Ma trận truy vết (gợi ý nhóm Test Case)

| Yêu cầu | Nhóm test case |
|---|---|
| FR-LOGIN-01 | UI/hiển thị mặc định |
| FR-LOGIN-02 | Đăng nhập thành công |
| FR-LOGIN-03 | Validation, đăng nhập thất bại, tài khoản inactive |
| FR-LOGIN-04 | Remember me / session |
| FR-LOGIN-05 | Đa ngôn ngữ |
| FR-LOGIN-06 | Forgot Password |
| FR-LOGIN-07, 08 | Điều hướng, bàn phím, accessibility |
| NFR-01 → 03 | Security testing |
| NFR-04 → 06 | Performance, compatibility, responsive |

---

## 9. Điểm cần xác minh / rủi ro quan sát được

1. Field Email dùng `type=text`, không `required` → không có kiểm tra phía trình duyệt; cần xác nhận thông báo lỗi của server.
2. Chưa xác nhận nội dung chính xác các thông báo lỗi, URL sau đăng nhập, thời hạn "Remember me", và cơ chế chống brute-force (chưa submit form).
3. Chưa thấy cơ chế CAPTCHA hay 2FA trên giao diện.
4. Dropdown Language xuất hiện hai lần trong DOM (select gốc + widget tìm kiếm) – cần lưu ý khi viết automation test.

## 10. Lịch sử thay đổi

| Phiên bản | Ngày | Nội dung |
|---|---|---|
| 1.0 | 2026-11-23 | Khởi tạo bản nháp từ khảo sát giao diện |
