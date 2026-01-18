# Challenge: SQL Truncation

<img width="1541" height="515" alt="image" src="https://github.com/user-attachments/assets/5c4df3a5-e40c-423a-bafa-aec1794607fb" />

Vào Challenge thì cho một giao diện như vầy 

<img width="437" height="173" alt="image" src="https://github.com/user-attachments/assets/e7b928b3-aff0-465f-b21b-501aa68de06f" />

Với gợi í từ ban đầu bài là `SQL Truncation`, Sau khi tìm hiểu thì mình nói qua cho các bạn dễ hiểu thế này 

Giả sử , cơ sở dữ liệu nó giới hạn username chỉ được nhập 6 kí tự , nhưng lúc này ở nơi bạn nhập , phía Back end nó lại không quy định username được nhập 6 kí tự 

Kết quả là , lúc đầu khi username bạn nhập: `admin        x` nó đi qua backend , phía backend sẽ gửi lên database nhờ database check xem có username nào là `admin        x` hay không?

Và database nó chỉ chứa username `admin` chứ không có `admin        x` , Vậy là nó đi qua được backend 

Khi nó vào đến Database thì engine của Database thực hiện Insert Into thêm tài `admin        x` vào trong Database , nhưng nó lại chỉ cho phép 6 kí tự , thế là chỉ còn lại `admin` , mà hơn nữa nó lại không quy định username chỉ được duy nhất ko được trùng lặp , và thế là 2 tài khoản admin được cất trong kho database với 2 mật khẩu khác nhau , và có 1 cái là do mình tự đặt

Áp dụng vào bài này , khi bạn bật Dev tool lên thì thấy đoạn này nó giới hạn username là 12 kí tự

<img width="1919" height="497" alt="image" src="https://github.com/user-attachments/assets/12890d56-0b71-4238-8d2e-705dad1c517e" />

Vậy thì chúng ta chỉ cần nhập `admin` rồi thêm 12 khoảng trắng ở sau mà thêm bất cứ kí tự gì vào sau 12 khoảng trắng đấy, để khi vào database nó xóa đi tất cả những kí tự thừa thì chúng ta sẽ có `admin`

password thì cứ nhập bất kì đi , nhớ để tí login vào acc admin là được

<img width="650" height="240" alt="image" src="https://github.com/user-attachments/assets/f83470c4-d951-451b-a92a-0ef18bdc6bfd" />

Và chúng ta có User Saved

<img width="582" height="256" alt="image" src="https://github.com/user-attachments/assets/e069e1a2-0cf2-44b4-84f8-94af2072807b" />

Rồi vào lại tab Administration kia:

Nhập lại mật khẩu đã đăng kí lúc nãy 

<img width="897" height="160" alt="image" src="https://github.com/user-attachments/assets/3e95500a-8493-4cde-b77e-68d80cbae366" />

Done bài này !!
