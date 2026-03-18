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

 Ta biết attacker đã khai thác lỗ hổng CVE-2003-0533 từ đó ta biết được Hacker muốn chiếm quyền điều khiển máy Windows thông qua dịch vụ SMB và biến nó thành một máy Proxy (Socks5).

Q8:  Which protocol did the attacker use to download additional malicious files to the target system?

Ta sẽ dựa vào bảng thống kê Endpoints TCP để kiểm tra 

<img width="698" height="205" alt="image" src="https://github.com/user-attachments/assets/d83332ed-1aa6-48b3-a816-dba1e6f1da6a" />

Phát hiện máy nạn nhân (192.150.11.111) có một kết nối bất thường tới IP của kẻ tấn công (98.114.205.102) qua cổng 8884 - Đây là một cổng không tiêu chuẩn, 1 cổng không thuộc dịch vụ nào của windows

Lọc riêng các gói tin đi qua cổng lạ đó (dùng lệnh tcp.port == 8884), sau đó Follow -> TCP Stream.

<img width="611" height="722" alt="image" src="https://github.com/user-attachments/assets/6c441633-1253-4614-84c9-42394526a68a" />

Kết quả trả về là một đoạn hội thoại với lời chào 220 NzmxFtpd 0wns j0, kèm theo các lệnh đăng nhập USER 1 và PASS 1 -> Protocol là TCP.

Q9: What is the name of the downloaded malware?

<img width="135" height="31" alt="image" src="https://github.com/user-attachments/assets/5b11cec3-2cc2-448b-9922-5cadcfc01103" />

Trong giao thức FTP, lệnh RETR (Retrieve) được sử dụng để máy khách (Client - lúc này là máy nạn nhân đã bị điều khiển) yêu cầu tải một tệp tin từ máy chủ (Server - máy của kẻ tấn công). Việc xuất hiện dòng lệnh RETR ssms.exe là bằng chứng trực tiếp cho thấy tệp thực thi có tên ssms.exe chính là Payload (mã độc) đã được đẩy thành công vào hệ thống mục tiêu.

Q10: The attacker's server was listening on a specific port. Provide the port number.

Máy nạn nhân (192.150.11.111) chủ động tạo một kết nối TCP tới IP của kẻ tấn công (98.114.205.102) tại đích đến là cổng 8884 như ở trên đã nói -> 8884

Q11: When was the involved malware first submitted to VirusTotal for analysis? Format: YYYY-MM-DD

Ta sẽ đi tìm Data channel - Khi một bên ra lệnh "Hãy gửi file cho tôi", một kênh hoàn toàn mới sẽ được mở ra CHỈ để truyền cái file đó. Truyền xong là đóng lại ngay.

Khi nạn nhân (bị hacker điều khiển) muốn tải file ssms.exe về, nó phải báo cho máy chủ FTP (của hacker) biết thông qua lệnh PORT trong chế độ Active Mode.

Ta đã biết dòng lệnh trong luồng TCP Stream ở cổng 8884: PORT 192,150,11,111,4,56

<img width="232" height="27" alt="image" src="https://github.com/user-attachments/assets/e92cbfec-5421-425e-9f29-34becb58c7b4" />

Cấu trúc chuẩn của lệnh này là: PORT IP1,IP2,IP3,IP4,P1,P2 

Để tính ra số cổng thực tế, ta áp dụng công thức: Port = P1 * 256 + P2

Port = 4 * 256 + 56 = 1024 + 56 = 1080

Chuyển hướng điều tra gõ lệnh lọc: tcp.port == 1080 do file ssms.exe chảy qua cổng 1080, nên ta tìm ở cổng 8884 sẽ không bao giờ thấy nội dung file, phải tìm file ở đúng cổng 1080

Chọn 1 gói bất kỳ rồi follow TCP stream rồi chuyển định dạng về raw: 

<img width="1263" height="950" alt="image" src="https://github.com/user-attachments/assets/f830c777-7432-4906-a611-d90c8b34a2d7" />

Lưu về máy ( malware.exe ) rồi dùng md5sum để lấy mã hash, sau đó lên Virustotal để kiểm tra

<img width="943" height="47" alt="image" src="https://github.com/user-attachments/assets/4b6d76a2-6b67-4b86-9da5-770e22cee7c0" />

<img width="451" height="79" alt="image" src="https://github.com/user-attachments/assets/ad15a9e4-4943-4144-aaf1-1205e0162476" />

First submission: 2007-06-27

Q12: What is the key used to encode the shellcode?
