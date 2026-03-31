Link Lab: https://portswigger.net/web-security/sql-injection/blind/lab-sql-injection-visible-error-based

Yêu cầu: Login với tài khoản administrator
Công cụ: Burp suite
Như ta đã biết, Visible error-based SQL injection là 1 lỗ hổng lợi dụng hệ thống cấu hình sai, hiển thị các thông báo lỗi quá chi tiết, từ đó ta sẽ biết được cấu trúc CSDL và đánh cắp các thông tin nhạy cảm
Ở Lab này ta sẽ tập trung khai thác vào cookie Tracking id.
Đầu tiền, ta sẽ thử thêm dấu ' vào Tracking Id 
<img width="1472" height="667" alt="{02A78D3F-1845-4811-991A-E4AFA472A63D}" src="https://github.com/user-attachments/assets/91e88594-0a0f-49e8-84a0-0eef99186867" />

ở đây, lỗi thông báo làm lộ toàn bộ câu truy vấn. Nó cũng giải thích chuỗi kí tự chưa được đóng.
Tiếp theo, hãy thêm kí tự chú thích -- để vô hiệu hoá toàn bộ câu truy vấn còn lại 
<img width="1512" height="615" alt="{91ADCC1F-AEF1-4C84-917F-EDB7E6165DDC}" src="https://github.com/user-attachments/assets/893d2c5b-5403-4537-8979-1fb3f8bad128" />

Lúc này trang web đã hoạt động bình thường chứng tỏ câu truy vấn của ta đã có tác dụng.
Sau đó, thêm vào câu truy vấn sau 'and cast(select 1) as int)-- . "Cast" là cú pháp ép kiểu, giúp trả sang kiểu int

<img width="1488" height="697" alt="{567CB5DA-F7FA-4530-8644-4A5C510712B7}" src="https://github.com/user-attachments/assets/5a4750b0-1c14-4a5d-8b38-ef5068f9977f" />

Quan sát lỗi lúc này báo rằng, điều kiện AND phải là 1 biểu thức boolean, ta chỉ cần sửa như sau:

<img width="1494" height="711" alt="{59884921-023D-434E-AE59-6458FF4AF41C}" src="https://github.com/user-attachments/assets/8230eebf-aa41-470b-8c22-8bf2268ef397" />

Giải thích: thêm 1= tạo ra 1 biểu thức so sánh 1=1 luôn trả về giá trị đúng(true), giúp câu lệnh SQL phù hợp với mọi hệ quản trị CSDL.
Tiếp theo, ta cần truy xuất dữ liệu người dùng từ bảng users 

<img width="1486" height="697" alt="{C2811522-9412-40C7-9654-1752C3575CB9}" src="https://github.com/user-attachments/assets/cb064292-fba9-4ca8-a346-7e56ea4436c1" />

Thông báo lỗi ở đây báo rằng có vẻ câu truy vấn hiện tại đã bị cắt bớt do quá giới hạn kí tự. Ta thử xoá giá trị tracking id ban đầu để tạo khoảng trống.

<img width="1486" height="686" alt="{0DFF774D-851C-40C2-89F4-BF57193ADF93}" src="https://github.com/user-attachments/assets/52d34d87-2e45-42a1-86fe-9439b9749e96" />

Lúc này, server lại báo rằng trả về quá nhiều dòng cùng 1 lúc gây ra lỗi, ta cần giới hạn số dòng trả về

<img width="1479" height="706" alt="{3EFD6CC3-694B-4B1A-B64A-39A05348B994}" src="https://github.com/user-attachments/assets/9f0f1430-8c79-40c1-a677-cb3858ab30be" />

Ta đã biết được tên administrator, giờ chỉ cần truy xuất mật khẩu từ bảng users là thành công 

<img width="1487" height="765" alt="{7D8F022A-F38E-4E24-8953-5CEE6CE8D280}" src="https://github.com/user-attachments/assets/05416f87-2c04-464f-8b85-4eb9a49cded0" />

Đăng nhập với tên tài khoản là administrator và mật khẩu vừa truy xuất được sẽ giải quyết được lab này
