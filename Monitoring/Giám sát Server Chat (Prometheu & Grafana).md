## 1.  **Mô hình mạng**

> ![](.//media/image1.png)
> 
>
> \- Ta sẽ sử dụng Prometheus và Grafana để giám sát các server chat
> (Zulip, Rocket.Chat, Mattermost).

## 2.  **Cài đặt Prometheus và Grafana trên CentOS 7**

### **a. Cấu hình chung cho hệ thống trước khi cài đặt Prometheus**
>
> \- Update Linux, sync NTP Linux, disable selinux:
>
> Chạy lệnh:
>
> yum update -y
>
> ntpdate 1.ro.pool.ntp.org
>
> ![](.//media/image2.png)
> 
>
> ![](.//media/image3.png)
> 
>
> vim /etc/sysconfig/selinux
>
> Thay đổi \"SELINUX=enforng\" thành \"SELINUX=disabled\"
>
> ![](.//media/image4.png)
>
> Lưu và thoát. Sau đó reboot lại server
>
> \- Cài đặt Iptables thay thế cho firewalld trên CentOS 7

-   Bước 1: remove firewalld

> systemctl stop firewalld
>
> systemctl disable firewalld
>
> systemctl mask \--now firewalld
>
> ![](.//media/image5.png)
> 

-   Bước 2: Cài đặt iptables

> yum install iptables-services -y
>
> systemctl start iptables
>
> systemctl enable iptables
>
> ![](.//media/image6.png)
>
> ![](.//media/image7.png)
> 

Mở các port sau:

> -A INPUT -p tcp -m state \--state NEW -m tcp \--dport 22 -j ACCEPT
>
> -A INPUT -p tcp -m state \--state NEW -m tcp \--dport 3000 -j ACCEPT
>
> -A INPUT -p tcp -m state \--state NEW -m tcp \--dport 9090 -j ACCEPT
>
> -A INPUT -p tcp -m state \--state NEW -m tcp \--dport 9100 -j ACCEPT

![](.//media/image8.png)


-   Bước 3: Lưu và khởi động lại dịch vụ:

> ![](.//media/image9.png)
> 
>
> Kiểm tra lại port đang mở:
>
> ![](.//media/image10.png)
>
> Kiểm tra lại port đang được dùng bởi service nào
>
> ![](.//media/image11.png)
> 
>
### **b. Cài đặt Prometheus bằng Package**
>
> \- Cài đặt Prometheus

-   Chạy các lệnh sau

> wget
> https://github.com/prometheus/prometheus/releases/download/v2.10.0/prometheus-2.10.0.linux-amd64.tar.gz
>
> tar -xvzf prometheus-2.10.0.linux-amd64.tar.gz
>
> mv prometheus-2.10.0.linux-amd64 /usr/local/prometheus/
>
> ![](.//media/image12.png)
> 
>
> ![](.//media/image13.png)
> 
>
> ![](.//media/image14.png)
> 

-   Tạo service prometheus trong systemd

> gedit /etc/systemd/system/prometheus.service
>
> ![](.//media/image15.png)
> 
>
> Nội dung file là:
>
> \[Unit\]
>
> Description=Prometheus
>
> Wants=network-online.target
>
> After=network-online.target
>
> \[Service\]
>
> User=root
>
> Group=root
>
> Type=simple
>
> ExecStart=/usr/local/prometheus/prometheus \\
>
> \--config.file /usr/local/prometheus/prometheus.yml \\
>
> \--storage.tsdb.path /usr/local/prometheus/ \\
>
> \--web.console.templates=/usr/local/prometheus/consoles \\
>
> \--web.console.libraries=/usr/local/prometheus/console_libraries \\
>
> \--web.listen-address=0.0.0.0:9090 \\
>
> \--web.enable-admin-api
>
> Restart=always
>
> \[Install\]
>
> WantedBy=multi-user.target
>
> ![](.//media/image16.png)
> 
>
> Restart và enable services
>
> systemctl daemon-reload
>
> systemctl start prometheus
>
> systemctl enable prometheus
>
> systemctl status prometheus
>
> ![](.//media/image17.png)
> 
>
> \- Prometheus Web UI: Prometheus đã cung cấp sẵn một web UI để hiển
> thị dữ liệu trực quan. Truy cập Prometheus web UI tại địa chỉ
> http://IP:9090/graph
>
> ![](.//media/image18.png)
> 

### **c. Cài đặt Grafana**

**- Bước 1:** Tải Grafana

> wget https://dl.grafana.com/oss/release/grafana-7.4.3-1.x86_64.rpm

![](.//media/image19.png)

**- Bước 2:** Cài đặt Grafana
>
> sudo yum localinstall grafana-7.4.3-1.x86_64.rpm
>
> ![](.//media/image20.png)
> 
>
**- Bước 3:** Khởi động dịch vụ
>
> sudo service grafana-server start
>
> sudo /sbin/chkconfig \--add grafana-server
>
> systemctl daemon-reload
>
> systemctl start grafana-server
>
> systemctl status grafana-server
>
> sudo systemctl enable grafana-server.service
>
> ![](.//media/image21.png)
> 
>
> ![](.//media/image22.png)
>
- **Bước 4:** Kết nối Prometheus với Grafana

-   Truy cập vào Grafana: http://IP:3000

> ![](.//media/image23.png)
> 

-   Password mặc định là admin/admin và sau đó sẽ đổi sau lần đầu tiên
    đăng nhập

-   Thêm datasource: Cliick button Add datasource. Sau đó chọn biểu
    tượng của Prometheus

> ![](.//media/image24.png)

-   Ở mục HTTP, nhập URL của Prometheus web UI và chọn Access là Browser

> ![](.//media/image25.png)
> 

-   Như vậy ta đã hoàn thành việc kết nối giữa Grafana và Prometheus

> ![](.//media/image26.png)
> 

## 3.  **Giám sát các Server Ubuntu (Zulip/Rocket.Chat/Mattermost)**

**- Bước 1:** Download node_exporter trên các Server cần giám sát
>
> wget
> https://github.com/prometheus/node_exporter/releases/download/v1.2.2/node_exporter-1.2.2.linux-amd64.tar.gz
>
> ![](.//media/image27.png)
> 
>
**- Bước 2:** Giải nén source code và copy đến đường dẫn sau
> /usr/local/node_exporter
>
> tar -xvzf node_exporter-1.2.2.linux-amd64.tar.gz
>
> mkdir /usr/local/node_exporter
>
> mv node_exporter-1.2.2.linux-amd64/node_exporter
> /usr/local/node_exporter
>
> ![](.//media/image28.png)
> 
>
**- Bước 3:** Tạo service trong systemd cho node_exporter
>
> gedit /etc/systemd/system/node_exporter.service
>
> Nội dung sau:
>
> \[Unit\]
>
> Description=Node Exporter
>
> Wants=network-online.target
>
> After=network-online.target
>
> \[Service\]
>
> User=root
>
> ExecStart=/usr/local/node_exporter/node_exporter
>
> \[Install\]
>
> WantedBy=default.target
>
> ![](.//media/image29.png)
> 
>
**- Bước 4:** Restart và enable service
>
> systemctl daemon-reload
>
> systemctl start node_exporter
>
> systemctl enable node_exporter
>
> ![](.//media/image30.png)
> 
>
**- Bước 5:** Truy cập vào node_exporter để kiểm tra (Trên Prometheus
> Server)
>
> ![](.//media/image31.png)
>
> ![](.//media/image32.png)
> 
>
> ![](.//media/image33.png)
> 
>
**- Bước 6:** Tạo job trong prometheus để giám sát server với nội dung
> sau
>
> gedit /usr/local/prometheus/prometheus.yml
>
> Nội dung:
>
> \- job_name: \'node_exporter\'
>
> scrape_interval: 5s
>
> static_configs:
>
> \- targets: \[\'192.168.17.155:9100\'\]
>
> labels:
>
> hostname: Zulip
>
> \- job_name: \'node_exporter\'
>
> scrape_interval: 5s
>
> static_configs:
>
> \- targets: \[\'192.168.17.136:9100\'\]
>
> labels:
>
> hostname: Rocketchat
>
> \- job_name: \'node_exporter\'
>
> scrape_interval: 5s
>
> static_configs:
>
> \- targets: \[\'192.168.17.142:9100\'\]
>
> labels:
>
> hostname: Mattermost
>
> ![](.//media/image34.png)
> 
>
**- Bước 7:** Restart lại prometheus service và kiểm tra kết quả
>
> systemctl restart prometheus
>
> ![](.//media/image35.png)
> 
>
**- Bước 8:** Đăng nhập vào Grafana để tạo Dashboard giám sát
>
> Zulip
>
> ![](.//media/image36.png)
> 
>
> ![](.//media/image37.png)
>
> Rocket.Chat
>
> ![](.//media/image38.png)
> 
>
> ![](.//media/image39.png)
>
> Mattermost
>
> ![](.//media/image40.png)
> 
>
> ![](.//media/image41.png)
> 

## **4. Cấu hình Alert trong Prometheus gửi tin nhắn qua Gmail**

**- Bước 1:** Đầu tiên ta sẽ download source cài đặt vào trong server

![](.//media/image42.png)

**- Bước 2:** Giải nén và copy vào thư mục /usr/local để quản lý

![](.//media/image43.png)


**- Bước 3:** Tạo service trong systemd cho alertmanager

![](.//media/image44.png)


Nội dung file như sau:

![](.//media/image45.png)


Enable và start service

![](.//media/image46.png)


**- Bước 4:** Cấu hình alert trong prometheus

Mở cổng 9093

-A INPUT -p tcp -m state \--state NEW -m tcp \--dport 9093 -j ACCEPT

![](.//media/image47.png)


![](.//media/image48.png)

Tại mục target: nhập thông tin ip, port, tại đây chính là ip của
prometheus server và port 9093 (port của alertmanager)

![](.//media/image49.png)


**- Bước 5:** Tạo các rule alert trong thư mục prometheus
(linux-rules.yml cho Zulip, linux-rules1.yml cho Rocket.Chat,
linux-rules2.yml cho Mattermost)

touch linux-rules.yml

![](.//media/image50.png)


![](.//media/image51.png)


![](.//media/image52.png)


Tại đây mình thêm nội dung sau vào linux-rules.yml

Zulip

![](.//media/image53.png)
![](.//media/image54.png)


![](.//media/image55.png)


![](.//media/image56.png)


Rocket.Chat

![](.//media/image57.png)


Mattermost

![](.//media/image58.png)


**- Bước 6:** Tiếp tục khai báo tên của rule đó vào cấu hình của
prometheus như sau

![](.//media/image59.png)


![](.//media/image60.png)


**- Bước 7:** Kiểm tra lại rule đã tạo bằng lệnh sau

![](.//media/image61.png)

**- Bước 8:** Restart prometheus service và kiểm tra kết quả. Ở đây ta
đang xem alert của tất cả các job trong file cấu hình prometheus.yml

![](.//media/image62.png)


![](.//media/image63.png)

**- Bước 9:** Cấu hình file alertmanager.yml để có thể alert về email

![](.//media/image64.png)


![](.//media/image65.png)


![](.//media/image66.png)

Sau đó kiểm tra file cấu hình đã được cập nhật thành công hay chưa với
lệnh amtool

![](.//media/image67.png)


Sau đó ta restart lại dịch vụ prometheus

![](.//media/image68.png)


**- Bước 10:** Ta sẽ kiểm tra email xem kết quả alert từ Server
Prometheus

![](.//media/image69.png)


![](.//media/image70.png)


**Tài liệu tham khảo**

https://itforvn.com/prometheus-tu-a-den-z-phan-01-cai-dat-prometheus-va-grafana-tren-centos-7/

https://toivietblog.com/monitor-linux-server-voi-prometheus-node-exporter/
