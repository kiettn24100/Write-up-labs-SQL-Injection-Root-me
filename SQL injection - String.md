# Challenge: SQL Injection - String


Vào Challenge thì cho chúng ta một giao diện như thế này



Đầu tiên mình bấm thử vào mấy cái đường link xanh xanh kia , ở trên thanh URL có tham số id nhận giá trị nhập vào thực hiện truy vấn và in ra màn hình kết quả trả về tư truy vấn 

Mình thử fuzz với một vài payload quen thuộc nhưng không nhận lại được gì cả

Tiếp theo ở mấy Tab nhỏ cạnh bên phải , mình thử vào đại tab search , và thử nhập vào `'or 1=1--` thì trang web có trả về thông tin trên màn hình , vậy tức là SQL Injection có hoạt động

Đầu tiên xác định số cột 

Truyền vào
```sql
' order by 2--
```
Chúng ta xác định được số cột bằng 2 

Xác định DB , thử fuzz bằng một vài lệnh 
