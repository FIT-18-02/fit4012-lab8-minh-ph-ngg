# Lab 8 – Báo cáo 1 trang

## 1. Mục tiêu

Xây dựng chương trình truyền dữ liệu an toàn qua socket bằng cách kết hợp DES-CBC, SHA-256 và RSA-OAEP.

## 2. Luồng xử lý Sender

1. Đọc plaintext từ biến môi trường `MESSAGE`, file `INPUT_FILE` hoặc bàn phím.
2. Tính SHA-256 của plaintext gốc.
3. Sinh ngẫu nhiên DES key 8 byte và IV 8 byte.
4. Mã hóa plaintext bằng DES-CBC, đặt IV ở đầu ciphertext.
5. Mã hóa DES key bằng RSA public key của Receiver.
6. Gửi packet qua socket theo định dạng Lab 8.

## 3. Luồng xử lý Receiver

1. Nhận packet qua socket.
2. Tách encrypted DES key, ciphertext và SHA-256 hash.
3. Dùng RSA private key để giải mã DES key.
4. Dùng DES key để giải mã ciphertext.
5. Tính lại SHA-256 của plaintext nhận được.
6. So sánh hash để kết luận dữ liệu nguyên vẹn hay đã bị thay đổi.

## 4. Kết quả minh chứng

- Chương trình chạy thành công, Sender mã hóa gửi đi và Receiver nhận dữ liệu, giải mã chính xác.
- Hệ thống đã kiểm tra tính toàn vẹn bằng SHA-256: **Khớp mã Hash (INTEGRITY PASSED)**.
- Khóa DES đã được bảo vệ thành công qua cơ chế mã hóa bất đối xứng RSA-OAEP.
- Hệ thống đã bắt được các trường hợp lỗi hoặc dữ liệu bị thay đổi (`tamper`) trên đường truyền socket thành công.

*Chi tiết các file nhật ký hoạt động:*
- File log Sender: `logs/sender_success.log` đã ghi nhận toàn bộ quá trình sinh key, mã hóa và gửi gói tin thành công.
- File log Receiver: `logs/receiver_success.log` đã ghi nhận quá trình nhận gói tin, giải mã RSA lấy DES key, giải mã dữ liệu thành công và kiểm tra hash trùng khớp.

## 5. Nhận xét

Cơ chế RSA-OAEP giúp bảo vệ khóa DES khi truyền qua mạng. SHA-256 giúp Receiver phát hiện thay đổi trên dữ liệu sau khi giải mã. Tuy nhiên, DES không còn phù hợp cho hệ thống thật; hướng nâng cấp nên là AES-GCM hoặc AES-CBC kết hợp cơ chế xác thực mạnh hơn.
