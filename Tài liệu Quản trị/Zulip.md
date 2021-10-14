# Cài đặt Zulip
- Ta cần cài đặt trên hệ điều hành Ubuntu Server 20.04
- Ta cần cấu hình file hosts theo các lệnh sau:
  > sudo nano /etc/hosts

![image](https://user-images.githubusercontent.com/92511177/137304148-7559ba14-a515-46e0-8253-e411014b7218.png)

- Thêm dòng sau vào file: 
  192.168.17.155 zulip.lan 

![image](https://user-images.githubusercontent.com/92511177/137304327-0bbcee97-4cf7-49b8-8b97-a894d660c360.png)
<br> Lưu và thoát

- Tải và cài đặt cái gói cần thiết
	> cd /tmp
	> wget https://www.zulip.org/dist/releases/zulip-server-latest.tar.gz
	> tar -xvf zulip-server-latest.tar.gz
 
 - Cài đặt Zulip với chế độ self-signed-cert
  > sudo -s ./zulip-server-*/scripts/setup/install --self-signed-cert --email=1714168@dlu.edu.vn --hostname=zulip.lan

- Quá trình cài đặt mất khoảng 5-15 phút. Sau khi cài đặt hoàn tất, ta truy cập vào đường link mà Zulip cung cấp. Ở màn hình trình duyệt, ta nhập địa chỉ email và chọn Create Organization.

![image](https://user-images.githubusercontent.com/92511177/137304635-94dea3f4-c6f9-41fa-ad30-b3d00eefc306.png)

![image](https://user-images.githubusercontent.com/92511177/137304669-2b1477a8-89a8-4de3-8866-4e3788f832dd.png)

- Tiếp theo, ta sẽ tạo tài khoản admin để quản lý Zulip

![image](https://user-images.githubusercontent.com/92511177/137304717-de696c50-e887-42b5-94ef-26819e4ee9b9.png)

![image](https://user-images.githubusercontent.com/92511177/137304727-57dca142-1cf1-4e36-9841-0aaaa0e795c8.png)

- Cấu hình email cho Zulip: Ta cấu hình SMTP Server, để Zulip có thể gửi các thông báo. Ta tiến hành các bước sau:
	 > sudo nano /etc/zulip/settings.py
<br>
Chỉnh sửa các thông số theo sau
  > EMAIL_HOST = 'smtp.gmail.com'
  > EMAIL_HOST_USER = 'GMAILADDRESS'
  > EMAIL_USE_TLS = True
  > EMAIL_PORT = 587

![image](https://user-images.githubusercontent.com/92511177/137304884-ec8a16e0-18e3-4ce6-a268-765b180dd977.png)

![image](https://user-images.githubusercontent.com/92511177/137304898-3d3972ac-76fd-4a09-952c-22f3d3282a24.png)
<br>
  Lưu và thoát

- Tiếp theo ta cấu hình mật khẩu cho SMTP server. Ta mở file sau để chỉnh sửa:
  > sudo nano /etc/zulip/zulip-secrets.conf
	  
![image](https://user-images.githubusercontent.com/92511177/137305038-151f3297-bf80-4a43-b9bc-601f91beb526.png)
<br>
Trong file ta thêm dòng sau (PASSWORD là mật khẩu email của bạn):
  > email_password = PASSWORD
- Cuối cùng, ta restart lại dịch vụ Zulip
  > su zulip -c '/home/zulip/deployments/current/scripts/restart-server'

![image](https://user-images.githubusercontent.com/92511177/137305451-b4b6eab1-ac3b-47f3-90ba-548998c9838c.png)

![image](https://user-images.githubusercontent.com/92511177/137305477-94690ff2-9f22-4a5c-afce-c7315bb33047.png)
<br>
Và đó là các bước cài đặt Zulip chat server. Zulip là nền tảng tin tưởng, hiệu năng tốt để thay thế Slack hoặc Microsoft Teams.

## Tài liệu tham khảo
https://computingforgeeks.com/install-zulip-chat-server-on-ubuntu-debian/
<br>
https://www.techrepublic.com/article/how-to-install-the-zulip-chat-server-on-ubuntu-18-04/
