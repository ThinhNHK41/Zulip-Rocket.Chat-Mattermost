# **Rocket.Chat**

**- Một số yêu cầu:**

-   ServerOS: Ubuntu 20.04 64-bit

-   RAM: 2Gb

-   Disk: 50Gb

-   vCPU: 2

**- Bước 1: Update hệ thống Ubuntu**

> sudo apt-get -y update

> sudo apt-get -y upgrade

![](.//media/image1.png)


**- Bước 2: Cài đặt các gói cần thiết**

Thêm MongoDB GPG signing key

> wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc \| sudo apt-key add -

Thêm MongoDB repository

> echo \"deb \[ arch=amd64,arm64 \] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse\" \| sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list

Cấu hình Node.js để được cài đặt thông qua quản lý gói Ubuntu

> sudo apt-get -y update && sudo apt-get install -y curl && curl -sL https://deb.nodesource.com/setup_12.x \| sudo bash -

Cài đặt Node.js, MongoDB, build tools và graphicsmagick

> sudo apt-get install -y build-essential mongodb-org nodejs graphicsmagick

![](.//media/image2.png)


Sau đó ta enable và start dịch vụ Mongod trước để tránh bị lỗi về sau

> **sudo systemctl enable mongod**

> **sudo systemctl start mongod**

> **sudo systemctl status mongod**

![](.//media/image3.png)


Kiểm tra phiên bản của node

> **node \--version**

![](.//media/image4.png)


Cài đặt inherits và n

> **sudo npm install -g inherits n**

![](.//media/image5.png)


Tạo một symbolic link cho thư mục node binary đến

> sudo ln -s /usr/bin/node /usr/local/bin/node

![](.//media/image6.png)

**- Bước 3: Cài đặt Rocket.Chat trên Ubuntu 20.04**

Tải xuống phiên bản mới nhất của Rocket.Chat với lệnh sau

> curl -L https://releases.rocket.chat/latest/download -o /tmp/rocket.chat.tgz

![](.//media/image7.png)


Giải nén file tải xuống đến thư mục /tmp

> tar -xzf /tmp/rocket.chat.tgz -C /tmp

![](.//media/image8.png)

Cài đặt Rocket.Chat đến thư mục bạn muốn. Ở đây, ta cài đặt vào thư mục
/opt

> cd /tmp/bundle/programs/server && npm install

> cd \~/

> sudo mv /tmp/bundle /opt/Rocket.Chat

![](.//media/image9.png)


![](.//media/image10.png)


**- Bước 4: Tạo người dùng hệ thống Rocket.Chat**

Tạo một người dùng rocketchat và gán quyền cho thư mục Rocket.Chat

> sudo useradd -M rocketchat && sudo usermod -L rocketchat

> sudo chown -R rocketchat:rocketchat /opt/Rocket.Chat

![](.//media/image11.png)


Ta sẽ sử dụng user này để tiến hành tạo dịch vụ

**- Bước 5: Tạo dịch vụ Rocket.Chat**

Tạo file dịch vụ Rocket.Chat như sau:

> cat \<\< EOF \|sudo tee -a /etc/systemd/system/rocketchat.service
> 
> \[Unit\]
> 
> Description=The Rocket.Chat server
> 
> After=network.target remote-fs.target nss-lookup.target nginx.service mongod.service
> 
> \[Service\]
> 
> ExecStart=/usr/local/bin/node /opt/Rocket.Chat/main.js
> 
> StandardOutput=syslog
> 
> StandardError=syslog
> 
> SyslogIdentifier=rocketchat
> 
> User=rocketchat
> 
> Environment=MONGO_URL=mongodb://localhost:27017/rocketchat?replicaSet=rs01
> MONGO_OPLOG_URL=mongodb://localhost:27017/local?replicaSet=rs01
> ROOT_URL=http://localhost:3000/ PORT=3000
> 
> \[Install\]
> 
> WantedBy=multi-user.target
> 
> EOF

Cấu hình storage engine và replication cho MongoDB sau đó chạy dịch vụ
MongoDB

> sudo sed -i \"s/\^#replication:/replication:\\n replSetName: rs01/\" /etc/mongod.conf

![](.//media/image12.png)


Start và enable dịch vụ MongoDB

> sudo systemctl daemon-reload

> sudo systemctl enable mongod && sudo systemctl restart mongod

![](.//media/image13.png)


Xác nhận dịch vụ MongoDB

> **systemctl status mongod**

![](.//media/image14.png)


Kiểm tra

> **mongo \--eval \"printjson(rs.initiate())\"**

![](.//media/image15.png)


Chạy dịch vụ Rocket.Chat

> sudo systemctl enable rocketchat && sudo systemctl start rocketchat

![](.//media/image16.png)


Kiểm tra dịch vụ có đang chạy không

> **systemctl status rocketchat**

![](.//media/image17.png)


**- Bước 6: Cấu hình Nginx Reverse Proxy**

Cấu hình Nginx để reverse proxy. Ta cũng có thể thiết lập Let\'s Encrypt
trong cùng file cấu hình

Cài đặt Nginx web server

> sudo apt install nginx

![](.//media/image18.png)


Sử dụng file cấu hình sau để reverse proxy cài đặt trước khi thiết lập
SSL

> sudo gedit /etc/nginx/conf.d/rocketchat.conf

![](.//media/image19.png)

Nội dung file như sau:

> upstream rocket_backend 
> 
> server 127.0.0.1:3000;
> 
> 
> 
> server 
> 
> listen 80;
> 
> server_name rocketchat.lan;
> 
> access_log /var/log/nginx/rocketchat-access.log;
> 
> error_log /var/log/nginx/rocketchat-error.log;
> 
> location / 
> 
> proxy_pass http://rocket_backend/;
> 
> proxy_http_version 1.1;
> 
> proxy_set_header Upgrade \$http_upgrade;
> 
> proxy_set_header Connection \"upgrade\";
> 
> proxy_set_header Host \$http_host;
> 
> proxy_set_header X-Real-IP \$remote_addr;
> 
> proxy_set_header X-Forward-For \$proxy_add_x\_forwarded_for;
> 
> proxy_set_header X-Forward-Proto http;
> 
> proxy_set_header X-Nginx-Proxy true;
> 
> proxy_redirect off;

![](.//media/image20.png)

Trong đó, bạn có thể thay thế **rocketchat.lan** thành domain name của
mình

![](.//media/image21.png)


Kiểm tra cấu hình Nginx đã ok chưa

> **sudo nginx -t**

![](.//media/image22.png)

Restart và enable dịch vụ Nginx

> sudo systemctl restart nginx

> sudo systemctl enable nginx

![](.//media/image23.png)


**- Bước 7: Thiết lập Let\'s Encrypt SSL**

Tải và cài đặt Let\'s Encrypt cho xác thực SSL

> sudo apt install certbot python3-certbot-nginx

![](.//media/image24.png)

Sau đó chạy lệnh sau và nhập các thông tin để xác thực SSL

> **sudo certbot \--nginx**

> \$ **sudo certbot \--nginx**

> Saving debug log to /var/log/letsencrypt/letsencrypt.log
> 
> Plugins selected: Authenticator nginx, Installer nginx
> 
> Which names would you like to activate HTTPS for?
> 
> - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
> 
> 1: rocketchat.lan
> 
> - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
> 
> Select the appropriate numbers separated by commas and/or spaces, or leave input
> 
> blank to select all options shown (Enter \'c\' to cancel): 1
> 
> Obtaining a new certificate
> 
> Performing the following challenges:
> 
> http-01 challenge for chat.hirebestengineers.com
> 
> Waiting for verification\...
> 
> Cleaning up challenges
> 
> Deploying Certificate to VirtualHost /etc/nginx/conf.d/chat.conf
> 
> Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
> 
> - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
> 
> 1: No redirect - Make no further changes to the webserver configuration.
> 
> 2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
> new sites, or if you\'re confident your site works on HTTPS. You can undo this change by editing your web server\'s configuration.
> 
> - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
> 
> Select the appropriate number \[1-2\] then \[enter\] (press \'c\' to cancel): 2
> 
> Redirecting all traffic on port 80 to ssl in /etc/nginx/conf.d/chat.conf

Cuối cùng cấu hình Nginx sau SSL (thêm các thông tin vào file /etc/nginx/conf.d/rocketchat.conf như sau:

> upstream rocket_backend 
> 
> server 127.0.0.1:3000;
> 
> 
> 
> server 
> 
> server_name rocketchat.lan;
> 
> access_log /var/log/nginx/rocketchat-access.log;
> 
> error_log /var/log/nginx/rocketchat-error.log;
> 
> location / 
> 
> proxy_pass http://rocket_backend/;
> 
> proxy_http_version 1.1;
> 
> proxy_set_header Upgrade \$http_upgrade;
> 
> proxy_set_header Connection \"upgrade\";
> 
> proxy_set_header Host \$http_host;
> 
> proxy_set_header X-Real-IP \$remote_addr;
> 
> proxy_set_header X-Forward-For \$proxy_add_x\_forwarded_for;
> 
> proxy_set_header X-Forward-Proto http;
> 
> proxy_set_header X-Nginx-Proxy true;
> 
> proxy_redirect off;
> 
> listen 443 ssl; \# managed by Certbot
> 
> ssl_certificate /etc/letsencrypt/live/chat.hirebestengineers.com/fullchain.pem; \# managed by Certbot
> 
> ssl_certificate_key /etc/letsencrypt/live/chat.hirebestengineers.com/privkey.pem; \# managed by Certbot
> 
> include /etc/letsencrypt/options-ssl-nginx.conf; \# managed by Certbot
> 
> ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; \# managed by Certbot
> 
> server 
> 
> if (\$host = chat.hirebestengineers.com) 
> 
> return 301 https://\$host\$request_uri;
> 
>  \# managed by Certbot
> 
> listen 80;
> 
> server_name chat.hirebestengineers.com;
> 
> return 404; \# managed by Certbot

Kiểm tra cấu hình Nginx đã ok chưa

> **sudo nginx -t**

![](.//media/image25.png)

Restart và enable dịch vụ Nginx

> sudo systemctl restart nginx

![](.//media/image26.png)


**- Bước 8: Tạo tài khoản Admin**

Sau khi hoàn tất toàn bộ quá trình cài đặt ta truy cập
http://rocketchat.lan:3000 để tiến hành thiết lập Rocket.Chat. Tại bước
đầu tiên ta được yêu cầu tạo tài khoản Admin. ĐIền các thông tin cần
thiết và bấm Continue.

![](.//media/image27.png)


Bước tiếp theo tiến hành điền các thông tin về Công ty/Tổ chức của mình
và bấm Continue.

![](.//media/image28.png)


Tiếp theo ta đặt tên cho website, lựa chọn ngôn ngữ hiển thị, loại
server (Private, Public) và tùy chọn xác thực 2 lớp qua email.

Cuối cùng hãy lựa chọn loại phù hợp mà bạn muốn (Sử dụng các dịch vụ
Rocket.Chat cung cấp hoặc lựa chọn tự cài đặt cấu hình) và bấm Continue.

![](.//media/image29.png)


Như vậy ta đã hoàn tất quá trình thiết lập Rocket.Chat hãy bấm đến
Workspace của bạn để truy cập vào Rocket.Chat

![](.//media/image30.png)


![](.//media/image31.png)


**Tài liệu tham khảo**

<https://technologyrss.com/how-to-install-rocket-chat-server-on-ubuntu-19-04/>

<https://blog.hostvn.net/chia-se/huong-dan-cai-dat-rocket-chat-tren-ubuntu-18.html>

<https://computingforgeeks.com/install-rocket-chat-on-ubuntu-with-letsencrypt/#>
