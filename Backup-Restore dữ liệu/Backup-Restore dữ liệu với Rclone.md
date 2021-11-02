**Backup dữ liệu của các Server (Zulip, Rocket.Chat, Mattermost)**

\- Ta sẽ sử dụng công cụ Rclone để back up dữ liệu của các server lên
Google Drive.

\- Rclone là một công cụ đồng bộ hóa dữ liệu tương tự Rsync nhưng lại
được tập trung phát triển chức năng kết nối với các dịch vụ lưu trữ đám
mây.

\- Ưu điểm của việc sử dụng dịch vụ lưu trữ đám mây đó là tốc độ cao (do
có server được đặt trên khắp thế giới), an toàn dữ liệu (không lo ngại
các vấn đề phần cứng, network) và nhất là hầu hết đều miễn phí.

\- Rclone hỗ trợ rất nhiều dịch vụ Cloud thông dụng như:

-   Google Drive

-   Amazon S3

-   Dropbox

-   Google Cloud Storage

-   Amazon Drive

-   The local filesystem

-   \...

\- Thay vì backup đưa lên VPS khác lưu trữ, ta chuyển sang sử dụng
Google Drive.

\- Ta sẽ tiến hành một script backup nhỏ sau để lưu trữ các server chat:

-   Sao lưu toàn bộ database MySQL, mỗi database một file .gz

-   Sao lưu toàn bộ code trong thư mục /home/domain.com/public_html/

-   Tổng hợp tất cả dữ liệu vào 1 folder

-   Upload file backup lên Google Drive vào lúc 2h sáng

\- Tự động xóa file backup trên VPS sau khi upload xong, xóa file backup
trên Cloud nếu quá 2 tuần.

\- Lưu ý: Server cần cài đặt MySQL.

**1. Zulip**

**a. Cài đặt Rclone**

\- Cài đặt bản mới nhất với hệ điều hành Linux 64bit

cd /root/

wget https://downloads.rclone.org/rclone-current-linux-amd64.zip

unzip rclone-current-linux-amd64.zip

\\cp rclone-v\*-linux-amd64/rclone /usr/sbin/

rm -rf rclone-\*

![](.//media/image1.png)


![](.//media/image2.png)


![](.//media/image3.png)


**b. Back up dữ liệu lên Cloud với Rclone**

\- Tạo kết nối với Google Drive

-   Đầu tiên ta sẽ cấu hình kết nối Rclone với Google Drive, việc này
    chỉ phải làm 1 lần duy nhất. Kết nối được tạo tên zulip

-   Chạy lệnh sau: rclone config

> ![](.//media/image4.png)
> 

-   Ta sẽ nhận được thông báo: No remotes found - make a new one, nhập n
    rồi nhấn Enter để tạo kết nối.

-   Ở dòng name bạn nhập **zulip** để đặt tên cho kết nối, bạn có thể
    chọn tên nào cũng được

> ![](.//media/image5.png)
> 

-   Một danh sách các dịch vụ cloud xuất hiện, hãy chọn số 15, Google
    Drive rồi nhấn Enter.

> ![](.//media/image6.png)
> 
>
> ![](.//media/image7.png)
> 
>
> ![](.//media/image8.png)
> 

-   Ở 2 dòng tiếp theo Client ID và Client Secret bạn hãy để trống nhấn
    Enter

> ![](.//media/image9.png)
> 

-   Mục Scope that rclone should use when requesting access from drive
    chọn 1 - drive

> ![](.//media/image10.png)
> 

-   Tiếp theo, để trống với ID of the root folder và Service Account
    Credentials JSON file path

> ![](.//media/image11.png)
> 

-   Khi được hỏi Use auto config? hãy nhập n rồi nhấn Enter. Ngay lập
    tức, Rclone sẽ đưa ra 1 đường link, ta có thể click thẳng vào đó
    hoặc copy rồi paste vào trình duyệt

> ![](.//media/image12.png)
> 
>
> ![](.//media/image13.png)
> 
>
> ![](.//media/image14.png)
> 
>
> ![](.//media/image15.png)
> 

-   Chọn n tức no đối với Configure this as a team drive

> ![](.//media/image16.png)
> 

-   Rclone xác nhận thông tin một lần nữa, bạn nhấn y để đồng ý rồi nhấn
    q để thoát khỏi giao diện cấu hình kết nối

> ![](.//media/image17.png)
> 
>
> ![](.//media/image18.png)
> 

-   Vậy là xong, giờ bạn có thể test với lệnh liệt kê thư mục trong kết
    nối remote

> rclone lsd zulip:
>
> ![](.//media/image19.png)
> 

**c. Script backup toàn bộ Zulip và upload lên Cloud**

\- Ta tạo file backup.sh và nhấp nối dung sau: (Lưu ý: ở phần
SERVER_NAME là tên thư mục bạn tạo trên Drive, REMOTE_NAME là zulip như
mình tạo ở trên, MYSQL_PASSWORD là mật khẩu mysql của bạn).

vi /root/backup.sh

#!/bin/bash

cwd=\$(pwd)

SERVER_NAME=Zulip

REMOTE_NAME=zulip

DATE=\`date +%Y-%m-%d\`

TIMESTAMP=\$(date +%F)

BAK_DIR=/data-backup/

BACKUP_DIR=\$BAK_DIR/\$TIMESTAMP

MYSQL_USER=\"root\"

MYSQL=/usr/bin/mysql

MYSQL_PASSWORD=1714168

Mysqldump=/usr/bin/mysqldump

rclone=/usr/sbin/rclone

SECONDS=0

exec \>\$BAK_DIR/logs/\$DATE.log

exec 2>&1

mkdir -p \"\$BACKUP_DIR/mysql\"

echo \"Starting Backup Database\";

databases=\`\$MYSQL -u \$MYSQL_USER -p\$MYSQL_PASSWORD -e \"SHOW
databases;\" \| grep -Ev \"(Database \|information_schema \|
performance_schema \| mysql \| sys)\" \`

for db in \$databases; do

echo \$db

\$Mysqldump -u \$MYSQL_USER -p\$MYSQL_PASSWORD \--databases \$db
\--quick \--lock-tables=false \| gzip> \"\$BACKUP_DIR/mysql/\$db.gz\"

done

echo \"Finished\";

echo \'\';

echo \"Starting Backup Website\";

mkdir -p \$BACKUP_DIR/data

domain=\$D##\*/ \# Domain name

echo \"-\" \$domain;

zip -r -y -q \$BACKUP_DIR/data/\$TIMESTAMP.zip /var/www/html/ #Exclude
cache

#fi

#done

echo \"Finished\";

echo \'\';

echo \"Starting compress file\";

size1=\$(du -sh \$BACKUP_DIR \| awk \'print \$1\')

cd \$BAK_DIR

tar -czf \$TIMESTAMP\".tgz\" \$TIMESTAMP

cd \$cwd

size2=\$(du -sh \$BACKUP_DIR\".tgz\"\| awk \'print \$1\')

rm -rf \$BACKUP_DIR

echo \"File compress from \"\$size1\" to \"\$size2

echo \"Finished\";

echo \'\';

echo \"Starting Backup Uploading\";

\$rclone copy \$BACKUP_DIR.tgz \"\$REMOTE_NAME:/\$SERVER_NAME/\"

#\$rclone -q delete \--min-age 1m \"\$REMOTE_NAME:/\$SERVER_NAME\"
#remove all backups older than 1 week

find \$BAK_DIR -mindepth 1 -mtime +6 -delete

echo \"Finished\";

echo \'\';

duration=\$SECONDS

echo \"Total \$size2, \$((\$duration/60)) minutes and
\$((\$duration%60)) seconds elapsed.\"

![](.//media/image20.png)


![](.//media/image21.png)


![](.//media/image22.png)


\- Sau khi tạo xong file ta chạy lệnh sau:

chmod +x /root/backup.sh

![](.//media/image23.png)

\- Ta chạy lệnh sau để tiến hành backup:

/root/backup.sh

![](.//media/image24.png)


![](.//media/image25.png)


\- Thử kiểm tra trên Cloud xem có thư mục mới với dữ liệu backup:

rclone lsl zulip:Zulip

![](.//media/image26.png)

![](.//media/image27.png)


**d. Tạo cronjob tự động backup hàng ngày**

crontab -e

0 2 \* \* \* /root/backup.sh \> /dev/null 2>&1

![](.//media/image28.png)


![](.//media/image29.png)

**e. Tải file backup từ Cloud xuống VPS**

\- Cách đơn giản nhất để bạn khôi phục lại dữ liệu đó là tỉa file backup
từ Cloud xuống máy tính, rồi tùy chọn theo như cầu mà up trở lại lên
VPS. Tuy nhiên, nếu muốn tải trực tiếp file backup về VPS, bạn có thể sử
dụng luôn Rclone với câu lệnh **copy**

VD: rclone copy \"zulip:/Zulip/2021-10-20.tgz\" /root/

![](.//media/image30.png)


Lệnh trên sẽ copy folder 2021-10-20 trong thư mục Zulip trên Cloud về
thư mục /root/ của VPS. Tốc độ upload và download từ Cloud đều rất
nhanh. Sau khi copy dữ liệu backup về VPS, ta tiến hành giải nén file
zip, copy thư mục web và nginx về đúng vị trí đồng thời tiến hành import
database.

**2. Rocket.Chat**

**a. Cài đặt Rclone**

\- Cài đặt bản mới nhất với hệ điều hành Linux 64bit

cd /root/

wget https://downloads.rclone.org/rclone-current-linux-amd64.zip

unzip rclone-current-linux-amd64.zip

\\cp rclone-v\*-linux-amd64/rclone /usr/sbin/

rm -rf rclone-\*

![](.//media/image1.png)


![](.//media/image2.png)


![](.//media/image3.png)


**b. Back up dữ liệu lên Cloud với Rclone**

\- Tạo kết nối với Google Drive

-   Đầu tiên ta sẽ cấu hình kết nối Rclone với Google Drive, việc này
    chỉ phải làm 1 lần duy nhất. Kết nối được tạo tên zulip

-   Chạy lệnh sau: rclone config

> ![](.//media/image4.png)
> 

-   Ta sẽ nhận được thông báo: No remotes found - make a new one, nhập n
    rồi nhấn Enter để tạo kết nối.

-   Ở dòng name bạn nhập **rocketchat** để đặt tên cho kết nối, bạn có
    thể chọn tên nào cũng được

> ![](.//media/image31.png)
> 

-   Một danh sách các dịch vụ cloud xuất hiện, hãy chọn số 15, Google
    Drive rồi nhấn Enter.

> ![](.//media/image6.png)
> 
>
> ![](.//media/image7.png)
> 
>
> ![](.//media/image8.png)
> 

-   Ở 2 dòng tiếp theo Client ID và Client Secret bạn hãy để trống nhấn
    Enter

> ![](.//media/image9.png)
> 

-   Mục Scope that rclone should use when requesting access from drive
    chọn 1 - drive

> ![](.//media/image10.png)
> 

-   Tiếp theo, để trống với ID of the root folder và Service Account
    Credentials JSON file path

> ![](.//media/image11.png)
> 

-   Khi được hỏi Use auto config? hãy nhập n rồi nhấn Enter. Ngay lập
    tức, Rclone sẽ đưa ra 1 đường link, ta có thể click thẳng vào đó
    hoặc copy rồi paste vào trình duyệt

> ![](.//media/image32.png)
> 
>
> ![](.//media/image13.png)
> 
>
> ![](.//media/image33.png)
> 
>
> ![](.//media/image34.png)
> 

-   Chọn n tức no đối với Configure this as a team drive

> ![](.//media/image16.png)
> 

-   Rclone xác nhận thông tin một lần nữa, bạn nhấn y để đồng ý rồi nhấn
    q để thoát khỏi giao diện cấu hình kết nối

> ![](.//media/image35.png)
> 
>
> ![](.//media/image18.png)
> 

-   Vậy là xong, giờ bạn có thể test với lệnh liệt kê thư mục trong kết
    nối remote

> rclone lsd rocketchat:
>
> ![](.//media/image36.png)
> 

**c. Script backup toàn bộ Rocket.Chat và upload lên Cloud**

\- Ta tạo file backup.sh và nhấp nối dung sau: (Lưu ý: ở phần
SERVER_NAME là tên thư mục bạn tạo trên Drive, REMOTE_NAME là rocketchat
như mình tạo ở trên, MYSQL_PASSWORD là mật khẩu mysql của bạn).

vi /root/backup.sh

#!/bin/bash

cwd=\$(pwd)

SERVER_NAME=Rocketchat

REMOTE_NAME=rocketchat

DATE=\`date +%Y-%m-%d\`

TIMESTAMP=\$(date +%F)

BAK_DIR=/data-backup/

BACKUP_DIR=\$BAK_DIR/\$TIMESTAMP

MYSQL_USER=\"root\"

MYSQL=/usr/bin/mysql

MYSQL_PASSWORD=1714168

Mysqldump=/usr/bin/mysqldump

rclone=/usr/sbin/rclone

SECONDS=0

exec \>\$BAK_DIR/logs/\$DATE.log

exec 2>&1

mkdir -p \"\$BACKUP_DIR/mysql\"

echo \"Starting Backup Database\";

databases=\`\$MYSQL -u \$MYSQL_USER -p\$MYSQL_PASSWORD -e \"SHOW
databases;\" \| grep -Ev \"(Database \|information_schema \|
performance_schema \| mysql \| sys)\" \`

for db in \$databases; do

echo \$db

\$Mysqldump -u \$MYSQL_USER -p\$MYSQL_PASSWORD \--databases \$db
\--quick \--lock-tables=false \| gzip> \"\$BACKUP_DIR/mysql/\$db.gz\"

done

echo \"Finished\";

echo \'\';

echo \"Starting Backup Website\";

mkdir -p \$BACKUP_DIR/data

domain=\$D##\*/ \# Domain name

echo \"-\" \$domain;

zip -r -y -q \$BACKUP_DIR/data/\$TIMESTAMP.zip /var/www/html/ #Exclude
cache

#fi

#done

echo \"Finished\";

echo \'\';

echo \"Starting compress file\";

size1=\$(du -sh \$BACKUP_DIR \| awk \'print \$1\')

cd \$BAK_DIR

tar -czf \$TIMESTAMP\".tgz\" \$TIMESTAMP

cd \$cwd

size2=\$(du -sh \$BACKUP_DIR\".tgz\"\| awk \'print \$1\')

rm -rf \$BACKUP_DIR

echo \"File compress from \"\$size1\" to \"\$size2

echo \"Finished\";

echo \'\';

echo \"Starting Backup Uploading\";

\$rclone copy \$BACKUP_DIR.tgz \"\$REMOTE_NAME:/\$SERVER_NAME/\"

#\$rclone -q delete \--min-age 1m \"\$REMOTE_NAME:/\$SERVER_NAME\"
#remove all backups older than 1 week

find \$BAK_DIR -mindepth 1 -mtime +6 -delete

echo \"Finished\";

echo \'\';

duration=\$SECONDS

echo \"Total \$size2, \$((\$duration/60)) minutes and
\$((\$duration%60)) seconds elapsed.\"

![](.//media/image37.png)


![](.//media/image21.png)


![](.//media/image22.png)


\- Sau khi tạo xong file ta chạy lệnh sau:

chmod +x /root/backup.sh

![](.//media/image23.png)

\- Ta chạy lệnh sau để tiến hành backup:

/root/backup.sh

![](.//media/image24.png)


![](.//media/image25.png)


\- Thử kiểm tra trên Cloud xem có thư mục mới với dữ liệu backup:

rclone lsl rocketchat:Rocketchat

![](.//media/image38.png)


![](.//media/image39.png)


**d. Tạo cronjob tự động backup hàng ngày**

crontab -e

0 2 \* \* \* /root/backup.sh \> /dev/null 2>&1

![](.//media/image28.png)


![](.//media/image29.png)

**e. Tải file backup từ Cloud xuống VPS**

\- Cách đơn giản nhất để bạn khôi phục lại dữ liệu đó là tỉa file backup
từ Cloud xuống máy tính, rồi tùy chọn theo như cầu mà up trở lại lên
VPS. Tuy nhiên, nếu muốn tải trực tiếp file backup về VPS, bạn có thể sử
dụng luôn Rclone với câu lệnh **copy**

VD: rclone copy \"rocketchat:/Rocketchat/2021-10-20.tgz\" /root/

![](.//media/image40.png)


Lệnh trên sẽ copy folder 2021-10-20 trong thư mục Rocket.Chat trên Cloud
về thư mục /root/ của VPS. Tốc độ upload và download từ Cloud đều rất
nhanh. Sau khi copy dữ liệu backup về VPS, ta tiến hành giải nén file
zip, copy thư mục web và nginx về đúng vị trí đồng thời tiến hành import
database.

**3. Mattermost**

**a. Cài đặt Rclone**

\- Cài đặt bản mới nhất với hệ điều hành Linux 64bit

cd /root/

wget https://downloads.rclone.org/rclone-current-linux-amd64.zip

unzip rclone-current-linux-amd64.zip

\\cp rclone-v\*-linux-amd64/rclone /usr/sbin/

rm -rf rclone-\*

![](.//media/image1.png)


![](.//media/image2.png)


![](.//media/image3.png)


**b. Back up dữ liệu lên Cloud với Rclone**

\- Tạo kết nối với Google Drive

-   Đầu tiên ta sẽ cấu hình kết nối Rclone với Google Drive, việc này
    chỉ phải làm 1 lần duy nhất. Kết nối được tạo tên zulip

-   Chạy lệnh sau: rclone config

> ![](.//media/image4.png)
> 

-   Ta sẽ nhận được thông báo: No remotes found - make a new one, nhập n
    rồi nhấn Enter để tạo kết nối.

-   Ở dòng name bạn nhập **mattermost** để đặt tên cho kết nối, bạn có
    thể chọn tên nào cũng được

> ![](.//media/image41.png)
> 

-   Một danh sách các dịch vụ cloud xuất hiện, hãy chọn số 15, Google
    Drive rồi nhấn Enter.

> ![](.//media/image6.png)
> 
>
> ![](.//media/image7.png)
> 
>
> ![](.//media/image8.png)
> 

-   Ở 2 dòng tiếp theo Client ID và Client Secret bạn hãy để trống nhấn
    Enter

> ![](.//media/image9.png)
> 

-   Mục Scope that rclone should use when requesting access from drive
    chọn 1 - drive

> ![](.//media/image10.png)
> 

-   Tiếp theo, để trống với ID of the root folder và Service Account
    Credentials JSON file path

> ![](.//media/image11.png)
> 

-   Khi được hỏi Use auto config? hãy nhập n rồi nhấn Enter. Ngay lập
    tức, Rclone sẽ đưa ra 1 đường link, ta có thể click thẳng vào đó
    hoặc copy rồi paste vào trình duyệt

> ![](.//media/image42.png)
> 
>
> ![](.//media/image13.png)
> 
>
> ![](.//media/image43.png)
> 
>
> ![](.//media/image44.png)
> 

-   Chọn n tức no đối với Configure this as a team drive

> ![](.//media/image16.png)
> 

-   Rclone xác nhận thông tin một lần nữa, bạn nhấn y để đồng ý rồi nhấn
    q để thoát khỏi giao diện cấu hình kết nối

> ![](.//media/image45.png)
> 
>
> ![](.//media/image18.png)
> 

-   Vậy là xong, giờ bạn có thể test với lệnh liệt kê thư mục trong kết
    nối remote

> rclone lsd mattermost:
>
> ![](.//media/image46.png)
> 

**c. Script backup toàn bộ Mattermost và upload lên Cloud**

\- Ta tạo file backup.sh và nhấp nối dung sau: (Lưu ý: ở phần
SERVER_NAME là tên thư mục bạn tạo trên Drive, REMOTE_NAME là mattermost
như mình tạo ở trên, MYSQL_PASSWORD là mật khẩu mysql của bạn).

vi /root/backup.sh

#!/bin/bash

cwd=\$(pwd)

SERVER_NAME=Mattermost

REMOTE_NAME=mattermost

DATE=\`date +%Y-%m-%d\`

TIMESTAMP=\$(date +%F)

BAK_DIR=/data-backup/

BACKUP_DIR=\$BAK_DIR/\$TIMESTAMP

MYSQL_USER=\"root\"

MYSQL=/usr/bin/mysql

MYSQL_PASSWORD=1714168

Mysqldump=/usr/bin/mysqldump

rclone=/usr/sbin/rclone

SECONDS=0

exec \>\$BAK_DIR/logs/\$DATE.log

exec 2>&1

mkdir -p \"\$BACKUP_DIR/mysql\"

echo \"Starting Backup Database\";

databases=\`\$MYSQL -u \$MYSQL_USER -p\$MYSQL_PASSWORD -e \"SHOW
databases;\" \| grep -Ev \"(Database \|information_schema \|
performance_schema \| mysql \| sys)\" \`

for db in \$databases; do

echo \$db

\$Mysqldump -u \$MYSQL_USER -p\$MYSQL_PASSWORD \--databases \$db
\--quick \--lock-tables=false \| gzip> \"\$BACKUP_DIR/mysql/\$db.gz\"

done

echo \"Finished\";

echo \'\';

echo \"Starting Backup Website\";

mkdir -p \$BACKUP_DIR/data

domain=\$D##\*/ \# Domain name

echo \"-\" \$domain;

zip -r -y -q \$BACKUP_DIR/data/\$TIMESTAMP.zip /var/www/html/ #Exclude
cache

#fi

#done

echo \"Finished\";

echo \'\';

echo \"Starting compress file\";

size1=\$(du -sh \$BACKUP_DIR \| awk \'print \$1\')

cd \$BAK_DIR

tar -czf \$TIMESTAMP\".tgz\" \$TIMESTAMP

cd \$cwd

size2=\$(du -sh \$BACKUP_DIR\".tgz\"\| awk \'print \$1\')

rm -rf \$BACKUP_DIR

echo \"File compress from \"\$size1\" to \"\$size2

echo \"Finished\";

echo \'\';

echo \"Starting Backup Uploading\";

\$rclone copy \$BACKUP_DIR.tgz \"\$REMOTE_NAME:/\$SERVER_NAME/\"

#\$rclone -q delete \--min-age 1m \"\$REMOTE_NAME:/\$SERVER_NAME\"
#remove all backups older than 1 week

find \$BAK_DIR -mindepth 1 -mtime +6 -delete

echo \"Finished\";

echo \'\';

duration=\$SECONDS

echo \"Total \$size2, \$((\$duration/60)) minutes and
\$((\$duration%60)) seconds elapsed.\"

![](.//media/image47.png)


![](.//media/image21.png)


![](.//media/image22.png)


\- Sau khi tạo xong file ta chạy lệnh sau:

chmod +x /root/backup.sh

![](.//media/image23.png)

\- Ta chạy lệnh sau để tiến hành backup:

/root/backup.sh

![](.//media/image24.png)


![](.//media/image25.png)


\- Thử kiểm tra trên Cloud xem có thư mục mới với dữ liệu backup:

rclone lsl mattermost:Mattermost

![](.//media/image48.png)


![](.//media/image49.png)


**d. Tạo cronjob tự động backup hàng ngày**

crontab -e

0 2 \* \* \* /root/backup.sh \> /dev/null 2>&1

![](.//media/image28.png)


![](.//media/image29.png)

**e. Tải file backup từ Cloud xuống VPS**

\- Cách đơn giản nhất để bạn khôi phục lại dữ liệu đó là tỉa file backup
từ Cloud xuống máy tính, rồi tùy chọn theo như cầu mà up trở lại lên
VPS. Tuy nhiên, nếu muốn tải trực tiếp file backup về VPS, bạn có thể sử
dụng luôn Rclone với câu lệnh **copy**

VD: rclone copy \"mattermost:/Mattermost/2021-10-20.tgz\" /root/

![](.//media/image50.png)


Lệnh trên sẽ copy folder 2021-10-20 trong thư mục Mattermost trên Cloud
về thư mục /root/ của VPS. Tốc độ upload và download từ Cloud đều rất
nhanh. Sau khi copy dữ liệu backup về VPS, ta tiến hành giải nén file
zip, copy thư mục web và nginx về đúng vị trí đồng thời tiến hành import
database.

**Tài liệu tham khảo**

https://vietcalls.com/huong-dan-dung-rclone-backup-du-lieu-len-google-drive/

https://huongdan.azdigi.com/how-to-install-cai-dat-mysql-tren-ubuntu-2004/

https://hocvps.com/rclone/#script-2-voi-hocvps-script-phien-ban-1-8-tro-xuong-hoac-he-quan-tri-khac
