Q1: What is the attacker's IP address?

98.114.205.102	192.150.11.111	TCP	62	1821 → 445 [SYN] -> 98.114.205.102 là tp của attacker

Q2: What is the target's IP address?

192.150.11.111

Q3: Provide the country code for the attacker's IP address (a.k.a geo-location).

Lên ipinfo.io và paste ip của attacker (98.114.205.102) vào để tìm kiếm quốc gia: -> Philadelphia, Pennsylvania, US

Q4: How many TCP sessions are present in the captured traffic?

Trên thanh công cụ trên cùng, chọn Statistics

Chọn tiếp Conversations

Trong cửa sổ mới hiện ra, chuyển sang tab TCP ta thấy có 5 sessions.

Q5: How long did it take to perform the attack (in seconds)?

chọn Statistics -> Capture File Properties.

 Dòng Elapsed ta thấy hệ thống tự động tính toán tổng thời gian ghi lại gói tin là 00:00:16

 Hoặc ta có thể xem từ gói tin đầu đến gói cuối ở cột Time từ gói 1 là 0.000 gói cuối là 16.219 -> 16s

 Q6: Provide the CVE number of the exploited vulnerability.

 CVE là viết tắt của Common Vulnerabilities and Exposures (Danh sách các lỗ hổng và phơi nhiễm phổ biến). Có thể hiểu đơn giản đây là một mã định danh duy nhất dành cho một lỗi bảo mật phần mềm hoặc phần cứng cụ thể.

 Dùng lệnh smb để lọc ra các gói tin liên quan đến chia sẻ file và gọi hàm

 Thấy được ở packet 33 và 38 có 1 hàm rất đáng nghi DsRoleUpgradeDownlevelServer: Đây là một hàm nội bộ của Windows dùng để nâng cấp các máy chủ đóng vai trò Domain Controller cũ. Người dùng hoặc quản trị viên mạng bình thường gần như không bao giờ gọi hàm này qua môi trường chia sẻ mạng ẩn danh.

 Lên mạng search về DsRoleUpgradeDownlevelServer biết được mã CVE là CVE-2003-0533.

 Q7: Which protocol was used to carry over the exploit?

 smb do dữ liệu exploit thực chất được gói trong DSSETUP, DSSETUP lại được gói trong DCERPC, và DCERPC lại được truyền đi trên đường truyền bằng SMB.

 Q8: Which protocol did the attacker use to download additional malicious files to the target system?

 Ta biết attacker đã khai thác lỗ hổng CVE-2003-0533 từ đó ta biết được Hacker muốn chiếm quyền điều khiển máy Windows thông qua dịch vụ SMB và biến nó thành một máy Proxy (Socks5).

 
