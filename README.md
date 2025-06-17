 <h2 align="center">
    <a>
    Chữ ký số
    </a>
</h2>
<p align="center">
    <img src="112.png" alt="DaiNam University Logo" width="200"/>
    <img src="113.png" alt="DaiNam University Logo" width="200"/>
    <img src="114.png" alt="DaiNam University Logo" width="200"/>
</p>
Giới thiệu về Chữ Ký Số (Digital Signature)
✅ 1. Chữ ký số là gì?
Chữ ký số là một dạng mã hóa dùng để xác nhận:

Tính xác thực của người gửi (người ký).

Toàn vẹn dữ liệu: đảm bảo rằng dữ liệu (ví dụ: file) không bị thay đổi trong quá trình truyền.

Không thể chối bỏ: người ký không thể phủ nhận rằng họ đã ký tài liệu.

Chữ ký số thường sử dụng cặp khóa công khai – khóa riêng (public/private key):

Khóa riêng dùng để ký.

Khóa công khai dùng để xác thực chữ ký.

🧠 Cơ chế hoạt động cơ bản:
Người gửi tạo hash của file (dữ liệu).

Dùng khóa riêng để mã hóa hash đó → tạo thành chữ ký số.

Người nhận dùng khóa công khai của người gửi để kiểm tra chữ ký này có khớp với file gốc hay không.

🌐 Cách sử dụng website này
Website được thiết kế đơn giản để mô phỏng một hệ thống quản lý file có hỗ trợ xác thực và upload file:

✍️ Các bước sử dụng:
B1. Đăng ký tài khoản
Truy cập trang web.

Nhấn "Đăng ký".

Nhập tên đăng nhập, mật khẩu và xác nhận.

Nhấn nút "Đăng ký".

🔒 Hiện tại bản web lưu tài khoản trong localStorage nên dữ liệu mất khi đổi trình duyệt/máy.

B2. Đăng nhập
Vào form đăng nhập.

Nhập tài khoản đã đăng ký.

Nhấn "Đăng nhập".

Sau khi đăng nhập, bạn sẽ được chuyển sang giao diện gửi file.

B3. Gửi file
Chọn file muốn gửi bằng nút "Chọn file".

Nhấn nút "Gửi".

File sẽ được hiển thị trong danh sách lịch sử file (được lưu tạm thời theo user trong localStorage).

⚠️ Giao diện hiện tại không có trường "chữ ký số" và "người nhận" nhưng backend (app.py) có hỗ trợ. Để kích hoạt đầy đủ chức năng chữ ký số, cần sửa phần JavaScript để:

Hash file trước khi upload.

Gửi chữ ký lên backend.

Backend kiểm tra chữ ký khi download.

B4. Tải file
Trong danh sách file, nhấn "Tải xuống" để lưu file về máy.
