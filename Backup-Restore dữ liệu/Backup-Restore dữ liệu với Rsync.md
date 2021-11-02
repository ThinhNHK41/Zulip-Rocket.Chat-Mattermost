## **Backup và Restore dữ liệu của Server Chat với Rsync**

- Rsync (Remote Sync) là một công cụ hữu hiệu để sao lưu và đồng bộ dữ liệu trên Linux. Với câu lệnh rsync ta có thể sao lưu và đồng bộ dữ liệu remote từ các máy sử dụng hệ điều hành Linux một cách dễ dàng và thuận tiện.
- Các đặc điểm nổi bật khi dùng Rsync:
  - Hiệu quả trong việc sao lưu và đồng bộ file từ 1 hệ thống khác
  - Hỗ trợ sao chép links, devices, owners, groups và permissions.
  - Nhanh hơn sử dụng SCP (secure copy)
  - Rsync tốn ít bandwidth vì nó có sử dụng cơ chế nén khi truyền tải và nhận dữ liệu
Các thông số cơ bản:
rsync options source destination
- Các tùy chọn trong rsync:
  - -v : verbose
  - -r : sao chép dữ liệu theo cách đệ quy (không bảo tồn mốc thời gian và permission trong quá trình truyền dữ liệu)
  - -a : chế độ lưu trữ cho phép sao chép các tệp đệ quy và giữ các liên kết, quyền sở hữu, nhóm và mốc thời gian
  - -z : nén dữ liệu
  - -h : định dạng số
Cài đặt Rsync
- Có thể cài đặt rsync theo câu lệnh:
> apt-get install rsync
 
![image](https://user-images.githubusercontent.com/92511177/139781543-ab7fd962-1450-4147-b87e-92ac8e61e31c.png)

### **1. Sao lưu, đồng bộ file trên local**
- Để copy file backup.tar sang thư mục /tmp/backups/ ta làm như sau:

> rsync -zvh backup.tar /tmp/backups/
- Khi thư mục đích chưa tồn tại thì rsync sẽ tự động tạo thư mục đích cho bạn. Sao lưu đồng bộ thư mục trên local.
- Bạn có thể đồng bộ toàn bộ file trong thư mục tới 1 thư mục khác trên local, ví dụ muốn đồng bộ thư mục /folder1 tới /folder2/

> rsync -avzh /folder1 /folder2/

Ta sẽ backup folder /home/thinhnh/Pictures đến thư mục /Pictures/
 
![image](https://user-images.githubusercontent.com/92511177/139781571-714216c1-d866-4adf-8dbe-9c86e1b4977e.png)

![image](https://user-images.githubusercontent.com/92511177/139781581-1eb9449c-47d8-40c6-9eb4-a916384d2b64.png)

### **2. Sao lưu, đồng bộ dữ liệu từ Server về local và từ local lên Server**
- Ở đây mình sẽ copy dữ liệu của Server Chat sang một Server khác và sau đó lấy dữ liệu từ Server đó về.
- Lưu ý: 
  - Ta cần active firewall trên Server mà mình muốn backup dữ liệu từ mình lên.
> sudo ufw enable
> 
> sudo ufw allow 22/tcp
> 
> sudo ufw allow 2244/tcp
  - Ta cần cài đặt openssh-server trên Server mà mình muốn backup dữ liệu
sudo apt install openssh-server
sudo service ssh start
  - Trên Server Backup ta cần vào file /etc/ssh/sshd_config và thay đổi như sau:
 
 ![image](https://user-images.githubusercontent.com/92511177/139781602-7e7b6393-a872-45cc-9c2b-33d165b238d5.png)

 ![image](https://user-images.githubusercontent.com/92511177/139781615-a86bd050-32a8-4cf6-a36b-6da688a14b81.png)

  - Đồng thời ta cũng cần thiết lập lại password cho user root (Tại lúc đầu nó chưa nhận mật khẩu cho ssh)
 
 ![image](https://user-images.githubusercontent.com/92511177/139781637-a7f47bf6-58d2-4c14-95b8-ac91a1321d99.png)

- Copy dữ liệu từ local lên server: Ta sẽ copy thư mục /home/thinhnh/ và động bộ lên server có IP 192.168.17.141:
> rsync -avz /home/thinhnh/ root@192.168.17.141:/home/backup/
 
 ![image](https://user-images.githubusercontent.com/92511177/139781645-69f0f1de-2da5-47c5-a400-89cac257b336.png)

 ![image](https://user-images.githubusercontent.com/92511177/139781654-9897ffc5-6b8f-4235-b3b4-177459207ce4.png)

![image](https://user-images.githubusercontent.com/92511177/139781663-3e37f460-dc67-4d8b-883e-a0f27b3a8396.png)

- Copy dữ liệu từ server về local: Ta có dữ liệu của Server Chat đã backup lên Server khác và ta muốn đồng bộ về máy local của mình:
> rsync -avzh root@192.168.17.141:/home/backup/ /home/thinhnh/
 
![image](https://user-images.githubusercontent.com/92511177/139781672-91c82b87-7128-4f30-a623-f742885b2f31.png)

**Tài liệu tham khảo** <br>
https://vietcalls.com/backup-du-lieu-tren-he-thong-linux-su-dung-rsync/
https://www.ibm.com/docs/en/db2/11.1?topic=installation-enable-disable-remote-root-login

