# Mattermost

![](.//media/image1.png)


**\- Các yêu cầu hệ thống:**

-   Máy chủ Linux (Ubuntu / Debian / CentOS)

-   1x vCPU/Core

-   2 GB RAM

-   6.5 Storage

**a\. Các bước cài đặt**

**\- Bước 1: Cập nhật hệ thống**

Cập nhật hệ thống lên phiên bản mới nhất

> apt update

> apt -y upgrade

> reboot

![](.//media/image2.png)

![](.//media/image3.png)

![](.//media/image4.png)

Thiết lập Hostname

> hostnamectl set-hostname mattermost.lan

> exec bash

![](.//media/image5.png)


**\- Bước 2: Cài đặt PostgreSQL làm máy chủ cơ sở dữ liệu**

Mattermost hỗ trợ MySQL/MariaDB và PostgreSQL. Tuy nhiên mặc định file
cấu hình được định sẵn với PostgreSQL nên ta sẽ dùng PostgreSQL là máy
chủ cơ sở dữ liệu.

> apt -y install postgresql postgresql-contrib

![](.//media/image6.png)


Sau khi cài đặt xong PostgreSQL, ta hãy đăng nhập và tạo thông tin
Database và người dùng

> sudo \--login \--user postgres

![](.//media/image7.png)

Để tạo hãy sử dụng các lệnh sau. Và ở đây ta sẽ tạo như sau:

-   Database: Đặt là mattermost

-   Username: Đặt là mmuser

-   Password: Đặt là P\@ssw0rd

> psql
> 
> CREATE DATABASE mattermost;
> 
> CREATE USER mmuser WITH PASSWORD \'P\@ssw0rd\';
> 
> GRANT ALL PRIVILEGES ON DATABASE mattermost to mmuser;
> 
> \\q

![](.//media/image8.png)


**\- Bước 3: Tạo nhóm và người dùng hệ thống**

Ta cần tạo user và group hệ thống để chạy. Để tạo thực hiện như sau:

> useradd \--system \--user-group mattermost

![](.//media/image9.png)

Ta có thể xác nhận đó là người dùng hệ thống vì ID của nó nhỏ hơn 1000

> id mattermost

![](.//media/image10.png)

**\- Bước 4: Tải và cài đặt Mattermost**

Bây giờ ta sẽ tải và cài đặt Mattermost từ trang chủ. Ta hãy chạy các
lệnh sau để tải về và giải nén file sau khi tải

> cd /root
> 
> wget https://releases.mattermost.com/5.37.0/mattermost-5.37.0-linux-amd64.tar.gz
> 
> tar xvf mattermost-\*.tar.gz
> 
> mv mattermost /opt

![](.//media/image11.png)


![](.//media/image12.png)


![](.//media/image13.png)


Tiếp theo ta hãy tạo một thư mục mới để lưu trữ dữ liệu như (hình ảnh,
video, ...). Ta có thể tạo bằng lệnh sau và cấp quyền sử dụng

> mkdir /opt/mattermost/data
> 
> chown -R mattermost:mattermost /opt/mattermost
> 
> chmod -R g+w /opt/mattermost

![](.//media/image14.png)


**\- Bước 5: Cấu hình Mattermost Server**

Sửa file cấu hình:

\- Thiết lập trình điều khiển CSDL Database tại file **/opt/mattermost/config/config.json**

> vi /opt/mattermost/config/config.json

\- Thay các dòng sau bằng thông tin của mình. Và file cấu hình cài đặt cơ sở dữ liệu PostgreSQL trong phần \"SqlSettings\".

**\- Lưu ý ta cần thay các dòng như sau bằng thông tin của mình**

-   SiteURL: Nhập vào hostname của mình

-   mat-khau-db: Nhập vào mật khẩu đã tạo ở bước 2

> \"SiteURL\": \"https://mattermost.lan\",
> 
> \"DriverName\": \"postgres\",
> 
> \"DataSource\": \"postgres://mmuser:mat-khau-db\@localhost:5432/mattermost?sslmode=disable&connect_timeout=10\",

![](.//media/image15.png)

![](.//media/image16.png)

![](.//media/image17.png)

\- Sau khi đã thay đổi ta hãy save lại thông tin.

Tạo file dịch vụ hệ thống của Mattermost

\- Tiếp theo, ta sẽ cần tạo tệp dịch vụ systemd để quản lý dịch vụ
Mattermost. Ta có thể tạo nó bằng lệnh sau:

> vi /etc/systemd/system/mattermost.service

> \[Unit\]
> 
> Description=Mattermost
> 
> After=network.target
> 
> After=postgresql.service
> 
> Requires=postgresql.service
> 
> \[Service\]
> 
> Type=notify
> 
> ExecStart=/opt/mattermost/bin/mattermost
> 
> TimeoutStartSec=3600
> 
> Restart=always
> 
> RestartSec=10
> 
> WorkingDirectory=/opt/mattermost
> 
> User=mattermost
> 
> Group=mattermost
> 
> LimitNOFILE=49152
> 
> \[Install\]
> 
> WantedBy=multi-user.target

![](.//media/image18.png)

![](.//media/image19.png)


\- Lưu và đóng tệp sau đó khởi động lại daemon systemd bằng lệnh sau:

> systemctl daemon-reload

![](.//media/image20.png)


\- Tiếp theo, khởi động dịch vụ Mattermost và cho phép nó khởi động khi
khởi động lại hệ thống bằng lệnh sau:

> systemctl start mattermost

> systemctl enable mattermost

> systemctl status mattermost

![](.//media/image21.png)


\- Và ngay lúc này thì Mattermost đang chạy và lắng nghe trên cổng 8065.
Và ta hãy truy cập server-ip:8065 để đăng nhập.

![](.//media/image22.png)


**MK: Abc\@123456789** (trong trường hợp này)

\- Truy cập vào System Console

![](.//media/image23.png)


\- Bước 5: Cấu hình Nginx Reverse Proxy

Tiếp theo ta hãy cài đặt và cấu hình NGINX Reverse Proxy cho Mattermost.
Để cài đặt ta sử dụng lệnh sau:

> apt-get install nginx -y

![](.//media/image24.png)


Sau khi cài đặt xong ta hãy tạo mới file cấu hình Mattermost và thêm vào
nội dung sau:

> vi /etc/nginx/conf.d/mattermost.conf

![](.//media/image25.png)

Thêm các dòng sau vào file. Và thay đổi 2 dòng sau bằng thông tin của
mình

-   server 192.168.17.142: Thay bằng IP của mình

-   server_name mattermost.lan; : Thay bằng hostname của mình

> upstream backend 
> 
> server 192.168.17.142:8065;
> 
> keepalive 32;
> 
> 
> 
> proxy_cache_path /var/cache/nginx levels=1:2
> keys_zone=mattermost_cache:10m max_size=3g inactive=120m
> use_temp_path=off;
> 
> server 
> 
> listen 80;
> 
> server_name mattermost.lan;
> 
> location \~ /api/v\[0-9\]+/(users/)?websocket\$ 
> 
> proxy_set_header Upgrade \$http_upgrade;
> 
> proxy_set_header Connection \"upgrade\";
> 
> client_max_body_size 50M;
> 
> proxy_set_header Host \$http_host;
> 
> proxy_set_header X-Real-IP \$remote_addr;
> 
> proxy_set_header X-Forwarded-For \$proxy_add_x\_forwarded_for;
> 
> proxy_set_header X-Forwarded-Proto \$scheme;
> 
> proxy_set_header X-Frame-Options SAMEORIGIN;
> 
> proxy_buffers 256 16k;
> 
> proxy_buffer_size 16k;
> 
> client_body_timeout 60;
> 
> send_timeout 300;
> 
> lingering_timeout 5;
> 
> proxy_connect_timeout 90;
> 
> proxy_send_timeout 300;
> 
> proxy_read_timeout 90s;
> 
> proxy_pass http://backend;
> 
> 
> 
> location / 
> 
> client_max_body_size 50M;
> 
> proxy_set_header Connection \"\";
> 
> proxy_set_header Host \$http_host;
> 
> proxy_set_header X-Real-IP \$remote_addr;
> 
> proxy_set_header X-Forwarded-For \$proxy_add_x\_forwarded_for;
> 
> proxy_set_header X-Forwarded-Proto \$scheme;
> 
> proxy_set_header X-Frame-Options SAMEORIGIN;
> 
> proxy_buffers 256 16k;
> 
> proxy_buffer_size 16k;
> 
> proxy_read_timeout 600s;
> 
> proxy_cache mattermost_cache;
> 
> proxy_cache_revalidate on;
> 
> proxy_cache_min_uses 2;
> 
> proxy_cache_use_stale timeout;
> 
> proxy_cache_lock on;
> 
> proxy_http_version 1.1;
> 
> proxy_pass http://backend;





![](.//media/image26.png)


Khởi động lại cấu hình NGINX

> rm /etc/nginx/sites-enabled/default

> systemctl restart nginx

![](.//media/image27.png)


**\- Bước 6: Cài đặt SSL Let\'s Encrypt cho Mattermost**

Trước tiên, ta sẽ cần cài đặt ứng dụng khách Certbot trong hệ thống của
mình để quản lý Let\'s Encrypt SSL. Ta có thể cài đặt nó bằng lệnh sau:

> apt-get install python3-certbot-nginx -y

![](.//media/image28.png)


Sau khi cài đặt Certbot, hãy chạy lệnh sau để cài đặt Let\'s Encrypt SSL
cho trang web của mình.

> certbot \--nginx -d mattermost.lan

Sau đó lần lượt chọn A \> Y \> 2

Bây giờ ta hãy truy cập và kiểm tra SSL nhé

**Tài liệu tham khảo**

<https://dotrungquan.info/cai-dat-mattermost-tren-ubuntu-20/>
