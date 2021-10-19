# **Mattermost**

 **a. Chế độ Admin**
>
> \- Sau khi triển khai, cài đặt và cấu hình xong Mattermost thì bạn
> truy cập vào đường dẫn: IP_Address:8065 thì bạn sẽ có thể đăng nhập
> hoặc tạo một tài khoản mới (Đây là tài khoản Admin của Server
> Mattermost mình đã tạo).

![](.//media/image1.png)

\- Sau khi đăng nhập, ta click **Go to System Console** để vào giao diện
của Admin

> ![](.//media/image2.png)
>
> \- Ở giao diện của Admin có một số tính năng ta có thể thấy như sau:

-   Edition and License (Phiên bản và Chính sách):

> ![](.//media/image3.png)
>
> Ở đây khi mới bắt đầu bạn có 2 lựa chọn là dùng thử phiên bản Trial
> (miễn phí trong 30 ngày). Hoặc có thể mua license với một số loại giá
> như sau:
>
> ![](.//media/image4.png)
> 
>
> Phiên bản doanh nghiệp: \$30 / user / tháng (Dành cho các tập đoàn,
> công ty lớn)
>
> Phiên bản professional: \$10 / user / tháng (Dành cho các công ty,
> doanh nghiệp nhỏ)
>
> Ngoài ra ta có thể đăng ký dựa trên số lượng thành viên mong muốn

-   Reporting (Báo cáo)

> \- Ở mục báo cáo này ta có thể xem được các số liệu thống kê về Server
> Mattermost mình đang triển khai
>
> ![](.//media/image5.png)
>
> Các loại số liệu thống kê rất đa dạng như: Số người đã truy cập, Số
> lượng (  Team, Channel, Session, Command, Tài liệu tải lên - tải
> xuống, User truy cập hằng ngày/tháng, ...), ....
>
> \- Các biểu đồ thống kê về Loại Channel, Loại File/Hastag/Text-Only và
> nhiều biểu đồ khác
>
> ![](.//media/image6.png)
> 
>
> \- Trạng thái của các Team trên Mattermost
>
> ![](.//media/image7.png)
> 
>
> ![](.//media/image8.png)
> 
>
> Ta có thể thấy được số lượng Channel Public/Private trong team, số
> lượng Post, người đăng các bài Post, thông tin người truy cập gần đây,
> người mới vào Team.
>
> \- Ngoài ra, nó còn hiển thị file ghi log của server giúp ta có thể
> biết thông tin hoạt động của từng thành viên trong Mattermost dựa trên
> Id của người đó.
>
> ![](.//media/image9.png)
> 

-   User Management (Quản lý người dùng)

> \- User (Người dùng): Ta có thể xem được thông tin các user dựa trên
> Role (Admin, Member, Guest, Inactive), theo nhóm. Chi tiết user
> (Email, phương pháp đăng nhập vào Mattermost, User ID)
>
> ![](.//media/image10.png)
> 
>
> Đối với người dùng Admin ta có thể điều chỉnh một số tùy chọn như:
> Quản lý Role, Quản lý Team, Đổi mật khẩu,\...
>
> ![](.//media/image11.png)
> 
>
> Đổi với người dùng Member ta có thể điều chỉnh các thông tin như: Ngắt
> hoạt động, Quản lý Role, Quản lý Team, Đổi mật khẩu, Cập nhật
> Email,\...
>
> ![](.//media/image12.png)
>
> \- Group (Nhóm): Ta có thể quản lý được các nhóm trong Domain trên
> Mattermost
>
> ![](.//media/image13.png)
> 
>
> \- Team (Đội): Ta có thể quản lý được các Team trên Mattermost. Điều
> chỉnh các tùy chọn cho Team như: Sao lưu người dùng từ Group đến Team,
> Bất kì ai cũng có thể tham gia vào Team, Chỉ cho phép những user có
> email theo đuôi nhất định vào Team.
>
> ![](.//media/image14.png)
>
> ![](.//media/image15.png)
> 
>
> Thêm thủ công User của Group hoặc User vào Team
>
> ![](.//media/image16.png)
> 
>
> ![](.//media/image17.png)
> 
>
> \- Channel (Kênh): Tương tự như Group hay Team thì Channel cũng có thể
> điều chỉnh các tùy chọn cho từng Channel như: Quản lý người dùng có
> thể truy cập Channel, Public/Private Channel, Thêm người dùng từ Group
> hay user thông thường, Cho phép Tạo/Quản lý bài Post,\...
>
> ![](.//media/image18.png)
> 
>
> \- Permissions (Quyền): Ở mục này Admin có thể điều chỉnh các tùy chọn
> Quyền của Tất cả Thành viên, Admin Channel/Team/System được phép quản
> lý, Tạo hay xóa các thông tin liên quan đến từng nhóm riêng trên.
>
> ![](.//media/image19.png)
> 
>
> ![](.//media/image20.png)
> 
>
> \- System Roles (Quyền hệ thống): Ở đây ta sẽ điều chỉnh được quyền
> của từng Role trong hệ thống (Admin/Member/User Manager/Viewer)
>
> ![](.//media/image21.png)
> 
>
> ![](.//media/image22.png)
> 
>
> Đồng thời có thể thêm thủ công user khi vào từng Role
>
> ![](.//media/image23.png)
> 

-   Environment (Môi trường làm việc)

> \- Web Server: Xem / Thay đổi thông tin URL của Server Mattermost
>
> ![](.//media/image24.png)
>
> \- Database (Cơ sở dữ liệu): Xem được thông tin của cơ sở dữ liệu lưu
> trữ Mattermost, số lượng kết nối cho phép, thời gian Timeout, Tìm kiếm
> cơ sở dữ liệu.
>
> ![](.//media/image25.png)
> 
>
> \- Các mục khác liên quan đến môi trường làm việc như: File Storage,
> Image Proxy, Elasticsearch, SMTP, Push Notification Server, High
> Availability, Rate Limiting, Logging, Session Lengths, Performance
> Monitoring, Developer.

-   Site Configuration (Cấu hình trang web)

> \- Ta có thể tùy chọn cấu hình liên quan đến trang Mattermost sử dụng.
>
> \- Customization: Tùy chỉnh các thông tin mô tả của Trang Web (Site
> name, Description, Brand Image, Link hỗ trợ, Chính sách, link download
> phiên bản Mobile)
>
> ![](.//media/image26.png)
> 
>
> ![](.//media/image27.png)
> 
>
> ![](.//media/image28.png)
> 
>
> \- Localization: Điều chỉnh ngôn ngữ cho giao diện Admin/Client
>
> ![](.//media/image29.png)
>
> \- Users and Teams: Điều chỉnh số lượng user của mỗi Team/Channel, tùy
> chọn hiển thị các thông tin của các user trong Team/Channel,\...
>
> ![](.//media/image30.png)
> 
>
> ![](.//media/image31.png)
> 
>
> \- Notifications (Thông báo): Ta có thể điều chỉnh các tùy chọn liên
> quan đến việc gửi thông báo cho các User
>
> ![](.//media/image32.png)
> 
>
> ![](.//media/image33.png)
> 
>
> \- Announcement Banner: Tùy chỉnh tên màu của Banner
>
> ![](.//media/image34.png)
> 
>
> \- Emoji: Kích hoạt biểu tượng Emoji 
>
> ![](.//media/image35.png)
> 
>
> \- Posts: Cho phép xem trước đường Link, cấm xem trước Link từ một vài
> tên miền,\...
>
> ![](.//media/image36.png)
> 
>
> \- File Sharing and Downloads: Cho phép chia sẻ File, Tải xuống/lên
> File trên Mobile
>
> ![](.//media/image37.png)
> 
>
> \- Public Links
>
> ![](.//media/image38.png)
> 
>
> \- Notices: Cho phép cảnh báo trên Admin/User
>
> ![](.//media/image39.png)
> 

-   Authentication (Xác thực)

> \- Signup: Quản lý các vấn đề liên quan đến đăng nhập như cho phép tạo
> tài khoản, điều hướng đối với một vài user, Cho phép mở server, Cho
> phép lời mời qua email,\...
>
> ![](.//media/image40.png)
> 
>
> \- Email: Cho phép tạo tài khoản với email, yêu cầu xác nhận qua
> email, cho phép đăng nhập với email, cho phép đăng nhập với username.
>
> ![](.//media/image41.png)
> 
>
> \- Password: Tùy chỉnh độ dài tối thiểu của mật khẩu, các yêu cầu liên
> quan đến mật khẩu, số lần đăng nhập tối đa nếu nhập sai mật khẩu
>
> ![](.//media/image42.png)
> 
>
> -  Ngoài ra còn có các mục xác thực khác như: MFA, AD/LDAP, SAML 2.0,
> OpenID Connect, Guest Access.

-   Plugins

> \- Plugin Management
>
> ![](.//media/image43.png)
> 
>
> \- Channel Export
>
> ![](.//media/image44.png)
> 
>
> \- Incident Collaboration
>
> ![](.//media/image45.png)
> 
>
> \- User Satisfaction Surveys
>
> ![](.//media/image46.png)

-   Integration (Tích hợp)

> \- Integration management: Quản lý tích hợp
>
> ![](.//media/image47.png)
> 
>
> \- Bot Accounts: Cho phép tạo tài khoản Bot, Tắt tài khoản Bot khi
> người sở hữu không hoạt động
>
> ![](.//media/image48.png)
> 
>
> \- GIF: Cho phép gửi GIF
>
> ![](.//media/image49.png)
> 
>
> \- CORS
>
> ![](.//media/image50.png)

-   Ngoài ra còn các mục khác có thể tìm hiểu thêm như: Compliance, Experimental.

**b. Chế độ User (Admin)**

\- Như đã tìm hiểu ở trên về các thiết lập Admin có thể điều chỉnh cho
hệ thống Mattermost. Nếu như từ tài khoản Admin ta muốn chuyển qua chế
độ người dùng để sử dụng hệ thống chat Mattermost. Ta hãy chọn Team
Selection như sau:

![](.//media/image51.png)


\- Sau đó chọn tạo một team mới hoặc có thể truy cập vào Team đã tạo

> ![](.//media/image52.png)
>
> \- Ở đây ta có giao diện sau khi truy cập Team bao gồm các Channel,
> Direct message (Tin nhắn riêng),\...
>
> ![](.//media/image53.png)
> 

\- Nếu muốn mời thêm người dùng khác vào Team thì ta hãy click vào
**Invite others to the workspace.** Ta có thể copy đường link và gửi cho
người khác để truy cập Team hoặc có thể thêm người dùng vào Team (đối
với người dũng đã có sẵn).

> ![](.//media/image54.png)
> 
>
> ![](.//media/image55.png)
> 
>
> \- Ở mục Channel, ta có thể tạo các Channel mới cũng như tùy chỉnh
> trên các Channel (Đánh dấu, Mute Channel, Di chuyển channel, thêm
> thành viên, Copy link hay rời khỏi channel).
>
> ![](.//media/image56.png)
> 
>
> ![](.//media/image57.png)
> 
>
> ![](.//media/image58.png)
> 
>
> \- Ở mục Direct Messages, ta có thể thêm 1 hoặc nhiều người dùng để có
> thể tạo ra các cuộc hội thoại riêng.
>
> ![](.//media/image59.png)
> 
>
> ![](.//media/image60.png)
> 
>
> \- Ở phần tin nhắn ta có rất nhiều tính năng (thiết lập tiêu đề cuộc
> trò chuyện, Emoji, Lưu post, ...) giúp trải nghiệm Chat với Mattermost
> thuận tiện hơn.
>
> ![](.//media/image61.png)
> 
>
> \- Ngoài ra ta còn rất nhiều các tùy chọn cho tài khoản của mình khi
> sử dụng Mattermost
>
> ![](.//media/image62.png)
> 
>
> Ta có thể tự tạo riêng cho mình một Emoji, xem các phím tắt giúp cho
> việc trao đổi nhanh và thuận tiện hơn. Hoặc đối với Admin thì có thể
> chọn **System Console** để tiến đến giao diện Admin như phần trước.
>
> ![](.//media/image63.png)
> 
>
> ![](.//media/image64.png)
> 
>
> Ta có thể thêm các gói thư viện ngoài phù hợp thông qua Marketplace để
> giúp cho trải nghiệm Mattermost của bạn tốt hơn.
>
> ![](.//media/image65.png)
>
> ![](.//media/image66.png)
> 
>
> \- Ta cũng có thể điều chỉnh trạng thái của mình giúp cho việc tương
> tác giữa mọi người phù hợp hơn.
>
> ![](.//media/image67.png)

**c. Chế độ User (Member)**

\- Cũng giống như chế độ User (Admin) bạn, có thể tạo các
Team/Channel/Direct Message riêng. Tương tác thiết lập với
Team/Channel/Direct Message dựa trên quyền mà Admin cung cấp cho user
đó. Đồng thời user cũng không thể truy cập vào System Console cũng như
các mục tác động tới cài đặt hệ thống (System Console, Manage Member,
Integration, Marketplace,\...)

![](.//media/image68.png)
![](.//media/image69.png)


Vậy ta đã tìm hiểu qua một về cách sử dụng Mattermost, các tính năng
cũng như giao diện sử dụng cho Admin và người dùng. Nhưng để có cái nhìn
chi tiết hơn thì ta nên trực tiếp sử dụng phần mềm.
