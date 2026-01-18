# Challenge: SQL injection - Lecture de fichiers

<img width="1501" height="509" alt="image" src="https://github.com/user-attachments/assets/370bdf6e-0bc3-49dd-ba5a-0388a7057a17" />

Vào Challenge thì cho một giao diện như vậy

<img width="477" height="156" alt="image" src="https://github.com/user-attachments/assets/329402aa-9c8c-4451-8537-4c2ae761a7bc" />

Tương tự như những bài khác thì Login chả có gì cả 

Vào tab  Members thì có một link ở đó cho xem profile admin bằng cách tham số id nhận giá trị truyền vào từ bên ngoài rồi thực hiện truy vấn SQL và kết quả trả về từ truy vấn nó sẽ hiển thị ra màn hình

Mình thử truyền vào `10000 or 1=1 -- -` 

<img width="1164" height="324" alt="image" src="https://github.com/user-attachments/assets/03e209cd-f090-4dde-b7e9-f6f65b264115" />

Nó vẫn hiển thị thông tin admin
