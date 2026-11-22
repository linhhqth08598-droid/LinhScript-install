## 1. Cài đặt & chạy chương trình

Bộ cài đặt chính thức đã đóng gói sẵn toàn bộ môi trường chạy (không cần cài thêm JDK hay cấu hình thủ công).

### Bước 1: Tải bộ cài đặt
👉 **[Tải về LinhScript-Setup-1.0.0.exe (Windows 64-bit)](https://github.com)**

### Bước 2: Cài đặt vào máy
1. Mở file **`LinhScript-Setup-1.0.0.exe`** vừa tải về.
2. Tích chọn **"Add LinhScript to PATH (Khuyên dùng)"** rồi bấm **Next → Install**.
3. Chương trình sẽ tự động cài đặt gọn gàng vào thư mục `C:\Users\<Tên_Bạn>\AppData\Local\Programs\LinhScript` và cấu hình sẵn lệnh hệ thống.

### Bước 3: Chạy chương trình đầu tiên
1. Tạo một file mới tên là **`hello.hql`** với nội dung:
   ```java
   print("Xin chào LinhScript!");
   var ten = "Linh";
   print("Tôi là " + ten);
   ```
2. Mở **Command Prompt (CMD)** hoặc **PowerShell** tại thư mục chứa file và gõ lệnh:
   ```bash
   LinhScript hello.hql
   ```
