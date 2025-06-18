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

# III. Cài đặt và active các Plugin trong Wordpress

## Cài đặt các plugin: Rank Math SEO, Mytheme Shop, Elementor, Divi Theme

* Sau khi cài đặt wordpress, vào trang quản trị và tiến hành thêm plugin
  Plugin > Thêm Plugin > Tải plugin lên > Tải plugin lên > Cài đặt > Kích hoạt plugin



# IV. Cách sử dụng Plugin **All-in-One WP Migration**
## Cài Đặt Plugin

* Đăng nhập vào trang quản trị WordPress:  
   `http://vdien.wp.vietnix.tech/wp-admin`

* Vào menu: **Plugin > Cài mới**

* Tìm với từ khóa: `All-in-One WP Migration`

* Nhấn **"Cài đặt"** → **"Kích hoạt"**


## Sao Lưu Website (Backup)

* Vào menu: **All-in-One WP Migration > Backup**

* Nhấn nút **Create Backup**

* Sau khi hoàn tất:
   - Nhấn biểu tượng **ba chấm** để **tải file backup (.wpress) về máy**

## Khôi Phục Website (Restore)

### Trường hợp 1: File backup vẫn còn trên VPS
* Vào: **All-in-One WP Migration > Backup**
* Nhấn **Restore** ở bản backup mong muốn
* Xác nhận và chờ hoàn tất

### Trường hợp 2: Có file `.wpress` trên máy
* Vào: **All-in-One WP Migration > Import**
* Kéo file `.wpress` vào trình duyệt hoặc nhấn **Import from File**
* Xác nhận và đợi quá trình phục hồi hoàn tất

## Di Chuyển Website (Migrate)

* **Trên website cũ:**
   - Vào: **All-in-One WP Migration > Export**
   - Chọn: **Export to > File**
   - Tải file `.wpress` về máy

* **Trên website mới (đã cài WP + plugin):**
   - Vào: **All-in-One WP Migration > Import**
   - Tải lên file `.wpress` từ máy
   - Nhấn **Proceed** để xác nhận

---

# V. Mục đích sử dụng WP-Optimize – Cache và Litespeed Cache

## 1. WP-Optimize – Cache

### Mục Đích Sử Dụng
- Tối ưu cơ sở dữ liệu WordPress
- Tạo bộ nhớ đệm (cache) để tăng tốc website
- Nén ảnh
- Gộp và nén HTML/CSS/JS

### Khi Nào Nên Cài
- Website sử dụng **shared hosting** hoặc **VPS thường**
- Không dùng Web Server LiteSpeed
- Cần tối ưu **database + cache + ảnh** trong một plugin duy nhất
- Không cần cấu hình phức tạp

### Cách Cài Đặt & Sử Dụng
1. Vào WordPress admin → **Plugin > Cài mới**
2. Tìm: `WP-Optimize`
3. Cài đặt và kích hoạt
4. Vào menu: **WP-Optimize**
   - Chọn: **Database** → dọn dẹp bản nháp, revision
   - Chọn: **Images** → bật nén ảnh
   - Chọn: **Cache** → bật bộ nhớ đệm

## 2. LiteSpeed Cache

### Mục Đích Sử Dụng
- Tăng tốc website bằng cache cấp server (server-level cache)
- Tối ưu ảnh (WebP), nén dữ liệu, lazy load, minify,...
- Hỗ trợ CDN, Object Cache, HTTP/3
- Tối ưu tốc độ toàn diện và chuyên sâu

### Khi Nào Nên Cài
- Website chạy trên **Web Server LiteSpeed** (OpenLiteSpeed hoặc LiteSpeed Enterprise)
- Dùng **Hosting/VPS hỗ trợ LiteSpeed** như Vietnix, Azdigi,...
- Cần tối ưu tốc độ chuyên sâu, vượt trội so với plugin thông thường

> Nếu **không chạy LiteSpeed web server**, plugin này **sẽ không hiệu quả**

### Cách Cài Đặt & Sử Dụng
1. Vào WordPress admin → **Plugin > Cài mới**
2. Tìm: `LiteSpeed Cache`
3. Cài đặt và kích hoạt
4. Vào menu: **LiteSpeed Cache**
   - Bật cache
   - Cấu hình các mục như:
     - **Page Optimization** (minify, lazy load, defer JS,…)
     - **Image Optimization**
     - **CDN, Browser Cache, Object Cache,...**

## 3. Nên Dùng Plugin Nào?

| Trường hợp | Khuyên dùng Plugin |
|------------|---------------------|
| Dùng hosting bình thường | WP-Optimize |
| Dùng VPS + Webserver Apache/Nginx | WP-Optimize |
| Dùng Web Server LiteSpeed | **LiteSpeed Cache** (ưu tiên) |
| Muốn cấu hình đơn giản | WP-Optimize |
| Muốn tối ưu tốc độ tối đa, chuyên sâu | LiteSpeed Cache |

- **Không nên cài 2 plugin cache cùng lúc**, tránh xung đột.
- Nếu đã dùng **LiteSpeed Cache**, thì **không cần WP-Optimize (cache)** – chỉ dùng phần tối ưu database nếu muốn.

---

# VI. Tạo SSL với ZEROSSL
# Cài Đặt SSL Miễn Phí Từ ZeroSSL Cho WordPress Và Laravel Trên aaPanel

## Bước 1: Tạo Chứng Chỉ SSL Từ ZeroSSL

- Truy cập trang: https://zerossl.com  
- Nhấn **“New Certificate”**  
- Nhập domain vdien.wp.vietnix.tech 

- Chọn loại chứng chỉ: **90-Day Certificate (Free)**  
- Chọn phương thức xác minh: **HTTP File Upload**  
- Tải file `.txt` chứa chuỗi xác minh do ZeroSSL cung cấp


## Bước 2: Upload File Xác Minh Lên VPS Qua aaPanel

### Tạo thư mục xác minh

- Vào menu **Files**
- Vào thư mục: www/wwwroot/vdien.wp.vietnix.tech
- Tạo thư mục `.well-known`
- Trong `.well-known`, tạo tiếp thư mục `pki-validation`
- Upload file xác minh vừa tải về lên thư mục mói tạo

## Bước 3: Hoàn Tất Cấp SSL

- Quay lại ZeroSSL → nhấn **Next Step**
- Sau khi xác minh thành công, tải về file zip chứa 3 file:
   - `certificate.crt`
   - `ca_bundle.crt`
   - `private.key`

## Bước 4: Cài SSL Trong aaPanel

- Vào: **Website > domain cần cài SSL 
- Chọn tab **SSL**
- Nhập các nội dung từ các file
  - **Private Key**: dán nội dung từ file `private.key`
  - **Certificate**: dán nội dung kết hợp: `certificate.crt`, `ca_bundle.crt`

- Nhấn **Save**


## Bước 5: Kiểm Tra Hoạt Động Của SSL

- Truy cập website bằng HTTPS: https://vdien.wp.vietnix.tech
- Làm tương tự với web lavarel
