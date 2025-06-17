# Triển Khai Website với AAPanel trên VPS

## I. Cài Đặt AAPanel trên VPS

### 1. SSH vào VPS
```
ssh root@14.225.207.58
```
Sau đó nhập password để đăng nhập 

### 2. Chạy lệnh cài đặt aaPanel
```
URL=https://www.aapanel.com/script/install_7.0_en.sh && if [ -f /usr/bin/curl ];then curl -ksSO "$URL" ;else wget --no-check-certificate -O install_7.0_en.sh "$URL";fi;bash install_7.0_en.sh aapanel
```
### 3. Đăng nhập vào giao diện aaPanel
* Sau khi cài đặt thành công aaPanel sẽ cung cấp thông tin đăng nhập dạng
```
aaPanel Internet Address: https://14.225.207.58:16050/39cf5d1c
aaPanel Internal Address: https://14.225.207.58:16050/39cf5d1c
username: username
password: password
```
Sử dụng thông tin đăng nhập được cung cấp để vào aaPanel. Sau khi đăng nhập sẽ vào được giao diện aaPanel

[![image.png](https://i.postimg.cc/FKqp85R2/image.png)](https://postimg.cc/p9fK5SKZ)

# II. Tạo Website WordPress và Laravel
## 1. Tạo Website WordPress

**Tên miền cần tạo:**
```
  vdien.wp.vietnix.tech 
```

**Vào aaPanel > Website > Add Site**
Sau đó nhập các thông tin tương ứng và nhấn `Submit`
[![image.png](https://i.postimg.cc/151K61fz/image.png)](https://postimg.cc/Yj8gKVNc)
![image](https://github.com/user-attachments/assets/95e2716d-685a-4fc1-ad96-45a51fcb8d44)

## 2. Tạo Website Lavarel


**Tên miền cần tạo:**
```
vdien.laravel.vietnix.tech
```
**Vào aaPanel > Website > Add Site**
Sau đó nhập các thông tin tương ứng và nhấn `Submit`

![image](https://github.com/user-attachments/assets/5a8fa69a-e3a5-43b7-91bd-f8612b387982)
![image](https://github.com/user-attachments/assets/ff3571b7-389e-45dd-9459-412f6d6989ac)

## 3. Cài đặt Wordpress
```
cd /www/wwwroot/vdien.wp.vietnix.tech
wget https://wordpress.org/latest.zip
unzip latest.zip
mv wordpress/* .
rm -rf wordpress latest.zip
chown -R www:www .
```
Khi thực hiện thay đổi quyền sở hữu cho thư mục WordPress với lệnh 
```
chown -R www:www .
```
**Có thể gặp lỗi:**
```
chown: changing ownership of './.user.ini': Operation not permitted
```
### Nguyên nhân:

* aaPanel tạo ra file .user.ini với thuộc tính immutable để bảo vệ file khỏi bị chỉnh sửa hoặc thay đổi quyền.

### Để sửa lỗi này thì ta tiến hành:
* Kiểm tra thuộc tính với:
```
lsattr .user.ini
```
* Kết quả:
```
----i---------e------- .user.ini
```
Chữ i nghĩa là file đang bị khóa chỉnh sửa.
* Gỡ bỏ thuộc tính immutable tạm thời bằng lệnh:
``
chattr -i .user.ini
``
* Sau đó thực hiện lệnh chown:
```
chown -R www:www .
```
* Cuối cùng là khôi phục lại trạng thái immutable:
```
chattr +i .user.ini
```

## 4. Cài đặt Lavarel 
* **Bước 1: Bảo đảm đã cài đặt LNMP Stack (Linux + Nginx + MySQL + PHP)**


## 4. Cài đặt Laravel

* **Bước 1: Bảo đảm đã cài đặt LNMP Stack (Linux + Nginx + MySQL + PHP)**

* **Bước 2: Cài Laravel**
  * **Chuyển vào thư mục của trang web:**
    ```bash
    cd /www/wwwroot/vdien.laravel.vietnix.tech
    ```
  * **Cài Laravel bằng Composer:**
    ```bash
    composer create-project laravel/laravel .
    ```
  * **Phân quyền để web server có quyền truy cập:**
    ```bash
    chown -R www:www .
    chmod -R 755 .
    ```

* **Bước 3: Cấu hình Laravel**
  * **Copy file .env từ file mẫu:**
    ```bash
    cp .env.example .env
    ```
  * **Tạo APP_KEY cho ứng dụng Laravel:**
    ```bash
    php artisan key:generate
    ```
    `APP_KEY` giúp Laravel mã hóa và giải mã dữ liệu nhạy cảm như session, reset token, cookie...

  * **Phân quyền thư mục lưu trữ tạm và cache:**
    ```bash
    chmod -R 775 storage
    chmod -R 775 bootstrap/cache
    ```

* **Bước 4: Cấu hình Nginx trong aaPanel:**
  - Vào **aaPanel > Website**  
  - Chọn **Conf** bên cạnh website  
  - Trong phần **Site Directory**, chọn **Running Directory** là `public`  
  - ![image](https://github.com/user-attachments/assets/99beea02-b3ae-4ea8-8cad-179d627b232e)

* **Bước 5: Kiểm tra**
  - Truy cập: http://vdien.laravel.vietnix.tech  
  - Nếu thấy trang mặc định Laravel hiển thị là đã cài thành công.
  
### Trong quá trình cài đặt có thể gặp một số lỗi sau:
* Putenv bị vô hiệu hóa
  
    **Nguyên nhân:** PHP bị tắt proc_open trong cấu hình disable_functions.

    **Cách xử lý:** Truy cập aaPanel > App Store > PHP 8.3 > Settings > Tab Disable Functions, Xóa Putenv > Lưu lại 
  
* The Process class relies on proc_open

    **Nguyên nhân:** PHP bị tắt proc_open trong cấu hình disable_functions.

    **Cách xử lý:** Truy cập aaPanel > App Store > PHP 8.3 > Settings > Tab Disable Functions, Xóa proc_open > Lưu lại 
  
![image](https://github.com/user-attachments/assets/e8a77501-7ac0-40df-8837-7949d4c4ed45)

* composer-runtime-api ^2.2 not matched

    **Nguyên nhân:** Phiên bản Composer quá cũ (Composer v1).

    **Cách xử lý:** Cập nhật Composer lên phiên bản mới nhất (Composer 2.x+)

* league/flysystem-local requires ext-fileinfo

    **Nguyên nhân:** PHP thiếu extension fileinfo.

    **Cách xử lý:** Vào aaPanel > App Store > PHP 8.3 > Settings > Install Extensions > Tìm và cài fileinfo

![image](https://github.com/user-attachments/assets/2bdb8bbe-024a-4557-94f5-af669c81fb37)

* Project directory "/www/wwwroot/vdien.laravel.vietnix.tech/." is not empty.
    **Nguyên nhân:** Thư mục không rỗng.

    **Cách xử lý:** Vào aaPanel > Website > Chọn Conf kế bên website > Site directory > Tắt Anti-XSS attack

