# Challenge: SQL injection - Time based

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
1 AND (SELECT CASE WHEN length((SELECT table_name from information_schema.tables))=1 THEN pg_sleep(10) ELSE NULL END) IS NULL---
```
add to intrusder rồi setting như hình dưới

<img width="1920" height="573" alt="image" src="https://github.com/user-attachments/assets/e6006d9b-fb50-4131-90a7-91e77b394168" />
