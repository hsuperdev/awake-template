---
title: Thao tác với cơ sở dữ liệu sử dụng PDO
subtitle: PHP
category:
  - About Awake
author: Hoàn Nguyễn
date: 2019-12-23T22:02:38.711Z
featureImage: /uploads/about-hero.jpg
---


In[Uncategorized](https://blog.codegym.vn/category/uncategorized/)December 25, 2019[![](https://secure.gravatar.com/avatar/49292377aba973fe8d41563ad26a15a6?s=25&d=mm&r=g)](https://blog.codegym.vn/author/vu-the-cong/)[Vu The Cong](https://blog.codegym.vn/author/vu-the-cong/)

Để có thể thao tác với CSDL trong [lập trình PHP](https://hoclaptrinhweb.org/hoc-thiet-ke-web/lap-trinh-web.html) chúng ta có 3 cách:

* Sử dụng mysql extension (đã ngừng phát triển và không được khuyên dùng)
* Sử dụng mysqli extension (hỗ trợ từ PHP 5 trở lên, là phiên bản cải tiến của mysql extension)
* Sử dụng PDO (PHP Data Object)

**PDO là gì?**

![cach-su-dung-pdo-trong-php](https://hoclaptrinhweb.org/images/ecode/hoc-lap-trinh-web/pdo-architecture.png)

PDO (PHP Data Object) là một database abstraction layer, có thể sử dụng các extension khác nhau để giao tiếp với các CSDL khác nhau (Oracle, DB2, Microsoft SQL server, Postgress SQL..) .PDO có thể được sử dụng để lập trình theo cả hướng thủ tục và theo hướng đối tượng.

Ứng với mỗi CSDL khác nhau PDO sẽ sử dụng các loại driver khác nhau để thao tác với CSDL. Các lệnh SQL được truyền vào PDO sẽ được các driver này chuyển sang câu lệnh SQL tương ứng với CSDL đang được sử dụng. Chính vì vậy khi sử dụng PDO chúng ta có thể dễ dàng chuyển sang một hệ quản trị CSDL khác mà không cần phải viết lại mã.

**Kết nối đến CSDL trong PDO**[?](https://hoclaptrinhweb.org/lap-trinh/hoc-php/255-bai-11-thao-tac-voi-csdl-trong-php-thong-qua-pdo.html#)

| 123456789101112131415 | `$dsn='mysql:host=localhost;dbname=ten_csdl';// Set options$options=array(PDO::MYSQL_ATTR_INIT_COMMAND =>"SET NAMES utf8",PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION);// Create a new PDO instanacetry{$db=newPDO($dsn,'user_dang_nhap_csdl','password_csdl',$options);}// Catch any errorscatch(PDOException$e) {echo$e->getMessage();exit();}` |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

Để có thể hiển thị tiếng việt khi thao tác với CSDL MySQL chúng ta cần chạy lệnh **“SET NAMES utf8”** khi kết nối đến CSDL MySQL. Giá trị PDO::ERRMODE_EXCEPTION trong thuộc tính PDO::ATTR_ERRMODE sẽ giúp PDO ném ra ngoại lệ khi gặp lỗi đồng thời tạo ra PHP warning. Tuy nhiên bạn có thể sử dụng try catch để bắt lỗi nhằm che giấu các thông báo lỗi.

Khi thao tác với CSDL trong PHP, đối với các lệnh SQL có tham số động chúng ta không nên truyền câu lệnh SQL trực tiếp thông qua chuỗi thay vì vậy chúng ta nên sử dụng Prepared Statement để tránh bị tấn công SQL Injection.

**Chèn dữ liệu vào CSDL thông qua PDO**[?](https://hoclaptrinhweb.org/lap-trinh/hoc-php/255-bai-11-thao-tac-voi-csdl-trong-php-thong-qua-pdo.html#)

| 1234567891011 | `//Khởi tạo Prepared Statement$stmt=$db->prepare('INSERT INTO users (name, email, age) values (:name, :mail, :age)');$stmt->bindParam(':name','Nguyen Van A');$stmt->bindParam(':mail','email_address');$stmt->bindParam('age', 22);$stmt->execute();/*****************Cách viết khác *********************/$stmt=$db->prepare('INSERT INTO users (name, email, age)values (:name, :mail, :age)');$data=array('name'=>'Nguyen Van A','mail'=>'email_address','age'=> 22);$stmt->execute($data);` |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |

Đầu tiên chúng ta sẽ tạo một Prepared Statement thông qua hàm **prepare()**. Ở đây chúng ta không truyền giá trị trực tiếp cho name, mail và age. Thay vào đó chúng ta sẽ sử dụng các place holder để giữ chỗ cho giá trị của các biến trên. Tiếp theo chúng ta tiến hành gắn giá trị cho các place holder vào câu lệnh Prepared Statement thông qua hàm**\
bindParam($tên_place_holder, $giá_trị_của_place_holder)**. Cuối cùng chúng ta thực thi prepared statement thông qua lệnh **execute()**.

**Truy vấn CSDL với PDO:**

Khi đọc dữ liệu từ database, PDO sẽ trả về dữ liệu theo cấu trúc mảng (array) hoặc đối tượng (object) thông qua các phương thức sau:

* fetchAll(\[int $fetch_mode, fetch_argument]): trả về một mảng chứa tất cả các hàng trong tập kết quả trả về (result set). Một số giá trị phổ biến của fetch_mode:

  * PDO::FETCH_BOTH (default): trả về dữ liệu dạng mảng với key là tên của cột và cả số thứ tự của cột.
  * PDO::FETCH_ASSOC: trả về dữ liệu dạng mảng với key là tên cột của bảng trong CSDL.
  * PDO::FETCH_NUM: trả về dữ liệu dạng mảng với key là số thứ tự của cột.
  * PDO::FETCH_OBJ: trả về một đối tượng của stdClass với tên thuộc tính trong đối tượng là tên của cột.
  * PDO::FETCH_CLASS: Gán giá trị của từng cột cho từng thuộc tính (property/attribute) của một lớp được chỉ định trước.
* fetch(\[int $fetch_mode]): trả về hàng kế tiếp từ result set.
* fetchColumn(\[int $column_number = 0] ): trả về 1 giá trị của cột được chỉ định trong hàng kế tiếp của result set.

Ví dụ truy vấn dữ liệu với PDO[?](https://hoclaptrinhweb.org/lap-trinh/hoc-php/255-bai-11-thao-tac-voi-csdl-trong-php-thong-qua-pdo.html#)

| 123456789101112 | `$stmt=$db->prepare('SELECT name, email, age from users');//Thiết lập kiểu dữ liệu trả về$stmt->setFetchMode(PDO::FETCH_ASSOC);$stmt->execute();$resultSet=$stmt->fetchAll();/*Trong trường hợp chưa setFetchMode() bạn có thể sử dụng$resultSet = $stmt->fetchAll(PDO::FETCH_ASSOC);*/foreach($resultSetas$row) {echo$row['name'] .'\n';echo$row['email'] .'\n';echo$row['age'] .'\n';}` |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

Do dữ liệu lấy về thông qua FETCH_ASSOC là dữ liệu dưới dạng mảng 2 chiều nên chúng ta sẽ sử dụng vòng lặp for để duyệt qua từng hàng và lấy ra giá trị của từng phần tử thông qua tên của cột bên trong CSDL.
