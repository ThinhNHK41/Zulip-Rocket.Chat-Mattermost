# 1. Zulip
**a. Ưu điểm**
- Là phần mềm mã nguồn mở và miễn phí. Zulip sử dụng license Apache 2.0. Ta có thể tự xây dựng riêng server cho mình.
- Giúp theo dõi các cuộc trò chuyện khác nhau. Thay vì phòng hay kênh thì Zulip xây dựng dựa trên các chủ đề cho cuộc trò chuyện.
- Dễ dàng đồng bộ các cuộc hội thoại. Trao đổi thuận tiện khi mọi người ở các múi giờ khác nhau..
- Hỗ trợ tất cả các nền tảng.
- Được lưu trữ hoặc tự lưu trữ cục bộ. Với các công cụ xuất dữ liệu để có thể di chuyển.
- Giao diện trực quan, rõ ràng.
- Hỗ trợ Video conferencing (jitsi)
- Tích hợp với email
- Có một API mạnh mẽ cho phép tích hợp dịch vụ của bên thứ 3
- Có công cụ tìm kiếm nâng cao
- Có thông báo thời gian thực

**b. Nhược điểm**
- Không có mã hóa End-to-End.
- Giao diện khá chi tiết nên gây gối dẫn đến khó sử dụng
- Cần phải có email của tổ chức để đăng ký tài khoản.

# 2. Rocket.Chat
**a. Ưu điểm**
- Là phần mềm mã nguồn mở và miễn phí. Rocket.Chat sử dụng license MIT với source code có sẵn trên GitHub. Ta có thể tự xây dựng riêng server cho mình.
- Hỗ trợ tất cả các phiên bản Desktop và Mobile.
- Hỗ trợ một loạt các phương pháp xác thực
- Cộng đồng rất rộng và hỗ trợ lẫn nhau
- Hỗ trợ Video conferencing (jitsi)
	- Dễ dàng chia sẻ tệp bằng cách kéo thả
	- Có thể tùy chỉnh background
	- Hỗ trợ công cụ tìm kiếm đơn giản, linh hoạt trong tin nhắn riêng hoặc nhóm.
	- Có 2 phiên bản On-premises (Miễn phí) hoặc SaaS (Trả thêm phí) để có thể sử dụng được các tính năng mới nhất của hệ thống, cải thiện sự kết nối giữa khách hàng và công ty. 

**b. Nhược điểm**
- Nhà phát triển không hỗ trợ
- Web client bị mất hình ảnh.
- Giao diện mobile tẻ nhạt

# 3. Mattermost
**a. Ưu điểm**
	- Là phần mềm mã nguồn mở và miễn phí. Ta có thể tự xây dựng riêng server cho mình.
- Có các thuộc tính giống Slack
- Hỗ trợ tốt cho các doanh nghiệp	
- Giao diện dễ sử dụng
- Hỗ trợ nhiều tính năng dành cho người quản trị server Rocket.Chat
- Cung cấp cả phiên bản online và offline
- Có công cụ tìm kiếm nâng cao

**b. Nhược điểm**
	- Chỉ có thể tương tác với LDAP thông qua phiên bản enterprise (phải trả phí)
	- Các thuộc tính, tùy chỉnh nền, permission chỉ áp dụng với phiên enterprise.

## 4. Một vài thông số so sánh khác

![image](https://user-images.githubusercontent.com/92511177/138821092-43d0cc93-d006-48c3-a1bf-276ef7ba7b40.png)

## 5. Tổng kết
- Từ những so sánh ở trên cho thấy ta có thể tự xây dựng một nền tảng công cụ Chat từ 1 trong 3 công cụ (Zulip / Rocket.Chat / Mattermost). Nhưng sẽ tùy vào mục đích sử dụng và tính năng, đặc điểm của từng cái mà ta lựa chọn công cụ nào phù hợp nhất. 
- Đối với các mô hình doanh nghiệp vừa và nhỏ thì ta có thể sử dụng Rocket.Chat vì hỗ trợ miễn phí với nhiều tính năng, bảo mật cao, tích hợp meeting jitsi,... Hoặc có thể mua license để được hỗ trợ thêm rất nhiều công cụ xác thực/bảo mật mạnh giúp cho việc trao đổi, hội họp được an toàn hơn.
- Đối với các mô hình doanh nghiệp lớn ta có thể sử dụng Rocket.Chat nhưng cũng có thể sử dụng Zulip hoặc Mattermost. Zulip thì có giao diện hơi phức tạp nên dẫn đến việc sẽ khó sử dụng, mất thời gian để làm quen nhưng lại có các chính sách bảo mật nâng cao, hỗ trợ nhiều cho Admin kiểm soát server Chat. 
- Tương tự với Mattermost, Mattermost thì yêu cầu mua bản quyền sau 14 ngày dùng thử nhưng lại là một công cụ Chat rất đáng để đầu tư. Nó sở hữu một giao diện dễ sử dụng, bắt mắt với nhiều tính năng cho người dùng. Có rất nhiều các tùy chọn quản trị hệ thống, tính năng bảo mật nâng cao cho Admin để kiểm soát server Chat một cách hiệu quả và an toàn.
