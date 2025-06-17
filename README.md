<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Giải thích về "Chữ ký số" trong Dự án</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
            margin: 20px;
            background-color: #f4f4f4;
            color: #333;
        }
        .container {
            max-width: 800px;
            margin: auto;
            background: #fff;
            padding: 30px;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        h1, h2 {
            color: #333;
        }
        pre {
            background-color: #eee;
            padding: 15px;
            border-radius: 5px;
            overflow-x: auto;
        }
        .warning {
            background-color: #fff3cd;
            border-left: 5px solid #ffc107;
            padding: 15px;
            margin-top: 20px;
            border-radius: 5px;
        }
        .code-snippet {
            background-color: #e6e6e6;
            padding: 10px;
            border-radius: 4px;
            font-family: monospace;
            white-space: pre-wrap;
            word-break: break-all;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Giải thích về "Chữ ký số" trong Dự án Chia sẻ Tệp</h1>

        <p>Tài liệu này cung cấp cái nhìn tổng quan về cách khái niệm "chữ ký số" được triển khai và sử dụng trong ứng dụng quản lý và chia sẻ tệp này.</p>

        <h2>1. Chức năng "Chữ ký" hiện tại</h2>
        <p>Trong ứng dụng của chúng tôi, khi người dùng tải lên một tệp thông qua API, có một trường tùy chọn được gọi là <code>signature</code>. Trường này cho phép người gửi đính kèm một chuỗi văn bản tự do cùng với tệp của họ.</p>

        <h3>Cách thức hoạt động:</h3>
        <ul>
            <li>Người dùng tải lên một tệp thông qua biểu mẫu trong <code>index.html</code>.</li>
            <li>Cùng với tệp, người gửi có thể nhập một "chữ ký" (dưới dạng văn bản).</li>
            <li>Thông tin này (bao gồm tên tệp, người gửi, người nhận và "chữ ký") được gửi đến API <code>/api/upload</code> của máy chủ.</li>
            <li>Máy chủ lưu trữ chuỗi "chữ ký" này nguyên trạng cùng với các siêu dữ liệu khác của tệp trong một danh sách các tệp đã tải lên.</li>
        </ul>

        <h3>Đoạn mã liên quan trong <code>app.py</code>:</h3>
        <pre><code class="code-snippet">
@app.route('/api/upload', methods=['POST'])
def upload():
    me = session.get('username')
    file = request.files.get('file')
    recips = request.form.get('recipients', '').split(',')
    sig = request.form.get('signature') # &lt;-- Đây là trường "signature"
    if not file or not recips or not sig:
        return jsonify(success=False, message='Thiếu dữ liệu'), 400
    filename = secure_filename(file.filename or ' ')
    path = os.path.join(UPLOAD_FOLDER, f"{uuid.uuid4()}_{filename}")
    file.save(path)
    files.append({
        'id': str(uuid.uuid4()), 'filename': filename, 'filepath': path,
        'sender': me, 'recipients': recips, 'signature': sig, 'timestamp': time.time() # &lt;-- "signature" được lưu trữ
    })
    return jsonify(success=True, message='Gửi file thành công')
        </code></pre>

        <h3>Đoạn mã liên quan trong <code>index.html</code> (phần gửi file):</h3>
        <p>Hiện tại, <code>index.html</code> của bạn chưa có trường nhập liệu cho <code>signature</code>. Để người dùng có thể nhập chữ ký, bạn cần thêm một trường <code>input</code> vào form tải lên:</p>
        <pre><code class="code-snippet">
&lt;div class="upload-section"&gt;
    &lt;h3&gt;Gửi file&lt;/h3&gt;
    &lt;form id="uploadForm"&gt;
        &lt;input type="file" id="uploadFile" required /&gt;
        &lt;!-- Thêm trường nhập chữ ký --&gt;
        &lt;div class="form-group"&gt;
            &lt;label for="fileSignature"&gt;Chữ ký/Ghi chú:&lt;/label&gt;
            &lt;input type="text" id="fileSignature" placeholder="Nhập chữ ký hoặc ghi chú của bạn" /&gt;
        &lt;/div&gt;
        &lt;!-- Thêm trường chọn người nhận (nếu chưa có) --&gt;
        &lt;div class="form-group"&gt;
            &lt;label for="fileRecipients"&gt;Người nhận (cách nhau bởi dấu phẩy):&lt;/label&gt;
            &lt;input type="text" id="fileRecipients" placeholder="username1,username2" required /&gt;
        &lt;/div&gt;
        &lt;button type="submit" class="btn-submit"&gt;Gửi&lt;/button&gt;
    &lt;/form&gt;
&lt;/div&gt;
        </code></pre>
        <p>Và điều chỉnh JavaScript trong <code>index.html</code> để lấy giá trị của trường này khi gửi form:</p>
        <pre><code class="code-snippet">
// Gửi file
document.getElementById("uploadForm").onsubmit = function (e) {
    e.preventDefault();
    const fileInput = document.getElementById("uploadFile");
    const file = fileInput.files[0];
    const signature = document.getElementById("fileSignature").value; // Lấy giá trị chữ ký
    const recipients = document.getElementById("fileRecipients").value; // Lấy giá trị người nhận

    if (file && signature && recipients) {
        const formData = new FormData();
        formData.append('file', file);
        formData.append('signature', signature);
        formData.append('recipients', recipients);

        fetch('/api/upload', {
            method: 'POST',
            body: formData
        })
        .then(response => response.json())
        .then(data => {
            if (data.success) {
                alert(data.message);
                updateFileHistory(); // Cập nhật danh sách file sau khi gửi thành công
                fileInput.value = ''; // Xóa input file
                document.getElementById("fileSignature").value = ''; // Xóa input chữ ký
                document.getElementById("fileRecipients").value = ''; // Xóa input người nhận
            } else {
                alert('Lỗi: ' + data.message);
            }
        })
        .catch(error => {
            console.error('Error:', error);
            alert('Đã xảy ra lỗi khi gửi file.');
        });
    } else {
        alert("Vui lòng chọn tệp, nhập chữ ký và người nhận.");
    }
};
        </code></pre>

        <h2>2. Hạn chế và Khuyến nghị</h2>
        <div class="warning">
            <p><strong>QUAN TRỌNG:</strong> Cần lưu ý rằng "chữ ký" được triển khai trong phiên bản hiện tại của ứng dụng <strong>chỉ là một chuỗi văn bản thông thường</strong> được cung cấp bởi người dùng.</p>
            <p>Nó <strong>KHÔNG</strong> phải là một chữ ký số mật mã học theo đúng nghĩa. Điều này có nghĩa là:</p>
            <ul>
                <li>Nó không đảm bảo tính toàn vẹn của tệp (tệp có bị thay đổi sau khi được ký không?).</li>
                <li>Nó không xác minh được danh tính của người ký một cách an toàn.</li>
                <li>Nó không cung cấp khả năng chống chối bỏ (người ký có thể dễ dàng chối bỏ rằng họ đã gửi tệp với chữ ký đó).</li>
            </ul>
        </div>

        <h3>Để có một hệ thống chữ ký số an toàn và mạnh mẽ hơn, cần phải triển khai các kỹ thuật mật mã học như:</h3>
        <ul>
            <li>Sử dụng các thuật toán băm (ví dụ: SHA-256) để tạo hàm băm của tệp.</li>
            <li>Sử dụng các thuật toán chữ ký số (ví dụ: RSA, ECDSA) với cặp khóa công khai/riêng tư để ký hàm băm của tệp.</li>
            <li>Cung cấp cơ chế xác minh chữ ký bằng khóa công khai tương ứng.</li>
        </ul>

        <h2>3. Mục tiêu Phát triển trong Tương lai</h2>
        <p>Trong các phiên bản tương lai của dự án, chúng tôi có kế hoạch nghiên cứu và tích hợp các giải pháp chữ ký số mật mã học để nâng cao tính bảo mật, toàn vẹn dữ liệu và xác thực danh tính cho các tệp được chia sẻ.</p>

        <p>Mục tiêu là cung cấp một môi trường chia sẻ tệp an toàn hơn, nơi người dùng có thể tin tưởng vào tính xác thực và nguồn gốc của các tệp nhận được.</p>
    </div>
</body>
</html>
