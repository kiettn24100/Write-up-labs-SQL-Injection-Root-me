# Challenge 1: SQL injection - Authentification

<img width="1451" height="525" alt="image" src="https://github.com/user-attachments/assets/c05075e1-f254-45a8-81a5-ae703253d4ed" />

Vào bài thì cho một giao diện web với chức năng đăng nhập , yêu cầu chúng ta phải lấy được mật khẩu của admin

<img width="402" height="370" alt="image" src="https://github.com/user-attachments/assets/50c58e2a-4d55-44a8-bb60-619b76bb3d14" />

Trước tiên mình thử nhập vào ô username các dấu nháy để test xem có lỗ hổng SQLi ở đây hay không

<img width="1098" height="183" alt="image" src="https://github.com/user-attachments/assets/e7fd663b-c85f-4a91-a18c-82c82f197d9d" />

Và kết quả là có tồn tại lỗ hổng SQLi ở đây thật , câu truy vấn đang sử dụng nháy đơn để đóng cái giá trị truyền vào tham số lại 

Tiếp tục mình thử
```
' or 1=1 --
```
Để xem coi nó có chặn gì ở đây hay không 

Kết quả là nó có trả về thông tin của user1 thật ạ , bao gồm cả username và password luôn , nhưng chắc là vì nó chỉ hiển thị được 1 dòng đầu tiên của dữ liệu trả về nên ta chưa thấy được hết thông tin admin , thông chắc là đang ở dưới của user1

<img width="422" height="443" alt="image" src="https://github.com/user-attachments/assets/04495105-a8d8-47c7-b340-543e5a6ad428" />

Đến đây thì vậy là xong bài rồi , muốn biết những thông tin dòng dưới nữa thì dùng thêm limit

```
' or 1=1 limit 1,1--
```
Và chúng ta có tài khoản admin

<img width="494" height="443" alt="image" src="https://github.com/user-attachments/assets/3cca983b-ba03-45da-907d-2cccc5eff415" />

F12 lên , và thay như thế này để đọc password

<img width="1920" height="530" alt="image" src="https://github.com/user-attachments/assets/d839f679-2b6b-4782-b7fe-a0a9aabde812" />

```
password: t0_W34k!$
```
CHúng ta solved được challenge này!!!

# Challenge 2:
