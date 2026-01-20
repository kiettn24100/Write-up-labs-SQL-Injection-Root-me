# Challenge: SQL injection - Authentification - GBK

<img width="1507" height="510" alt="image" src="https://github.com/user-attachments/assets/beb5f9f4-ed31-4b8b-894e-38f8f63495c0" />

Vào Challenge thì cho một giao diện như vậy 

<img width="502" height="184" alt="image" src="https://github.com/user-attachments/assets/ac95c982-86fc-4fc1-a597-fd331d19ea59" />

Mình thử fuzz ở phần login với một vài payload quen thuộc thì đều không thể khai thác được gì 

Không tìm được thêm manh mối gì nên mình thử tìm cách gây lỗi ở backend xem nó có in ra lỗi không . Lưu ý phải phân biệt lỗi ở back end nó khác với lỗi ở DB , lỗi ở DB nãy giờ mình đã thử thêm dấu nháy đơn thử phá cấu trúc rồi nhưng khả năng nó đã bị chặn kiểu gì trước khi đi vào DB rồi . Nên không thể gây lỗi ở DB được

Gây lỗi ở backend bằng cách thử thêm kí hiệu mảng sau login và password . Bởi vì khả năng code backend nó chỉ xử lý chuỗi mà truyền mảng vào thì có thể báo lỗi ra màn hình 

Chúng ta được 

<img width="1493" height="494" alt="image" src="https://github.com/user-attachments/assets/b4bd36ce-04f7-4717-8bfc-8a68d0eb333d" />

```
Hàm md5() yêu cầu tham số đầu tiên đưa vào phải là một chuỗi ký tự 
Hàm ddslashes() yêu cầu tham số đầu tiên đưa vào phải là một chuỗi ký tự
```
Sau khi gây lỗi từng phần lại thì mình xác định được cái tham số `login` kia sẽ đi vào hàm `ddslashes()` còn password sẽ đi vào `md5()`

Hàm `ddslashes()` được hiểu như là , mỗi khi gặp các ký tự dấu nháy , ở dạng thường hay là dạng hex thì nó sẽ luôn luôn thêm dấu escape hoặc dạng hex của escape nếu đó là dạng hex của dấu nháy 

Với gợi í từ đề bài là GBK - Đây là một bảng mã mã hóa ký tự tiếng trung giống như ASCII , dạng dạng vậy 

 - Các ký tự ASCII như a ,b,c,1,2 , ... thì thường thường chỉ chiếm 1 byte
 - Các ký tự tiếng trung sẽ thường chiếm 2 byte
 - Nếu hệ thống gặp một byte cao nằm trong khoảng 0x81 đến 0xFE , nó sẽ tự động lấy thêm 1 byte tiếp theo , đứng sau nó để gộp lại thành 1 ký tự tiếng trung. Nhưng cái byte sau thì cũng phải nằm trong khoảng hợp lệ ( từ 0x40 - 0xFE ) 

Các bạn hiểu thế này cho dễ , giả sử byte cao nằm trong khoảng 0x81 đến 0xFE . Nếu mình truyền vào 0x88 0x5c 0x31 , dạng dạng thế . Thì nếu Database nó áp dụng chuẩn GBK thì khi nó thấy 0x88 thì đây là byte cao nó sẽ áp dụng quy tắc lấy byte sau đó là 0x5c , và cái 0x5c này lại nằm trong khoảng hợp lệ dành cho byte sau . 2 byte đó hợp lại thành 1 kí tự tung của

Bảo sao nãy giờ không thể gây ra lỗi gì , thì ra đã bị thêm escape hết .

Nhưng nó vẫn có lỗ hổng của nó , bởi vì cái tự động thêm đó nên nếu chúng ta truyền vào là `%88'` thì khi đi qua hàm `ddslashes()` nó sẽ tự động thêm dấu `\` , nên khi đó vào database sẽ là `0x88 0x5c 0x27` , rồi database khi gặp `0x88` nó biết byte cao nên sẽ lấy luôn `0x5c` đó nằm trong khoảng hợp lệ rồi tạo thành 1 kí tự trung quốc

`0x88 0x5c` - > `袈`

Kết quả lúc đầu truyền vào `%88'` Bây giờ chúng ta đã có `袈'` 

Áp dụng , nếu truyền vào ô login là : `%88' or 1=1 -- -` thì chúng ta có :

<img width="1492" height="821" alt="image" src="https://github.com/user-attachments/assets/570eca17-99cc-45b7-8a29-84fa658daf4c" />

Bypass thành công do đã đóng được dấu nháy trước đó và or 1=1 được kích hoạt 

Cuối cùng đi đến `logged.php` và nhận flag thôi 

<img width="1491" height="446" alt="image" src="https://github.com/user-attachments/assets/80b2b70b-4c08-44bf-83f8-4b2d4d14b069" />

`iMDaFlag1337!`

