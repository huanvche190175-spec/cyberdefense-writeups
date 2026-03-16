Q1: What is the FTP password? 

FTB là giao thức sẽ truyền tải mọi thứ dưới dạng Cleartext (văn bản thuần, không mã hóa).
nhập câu lệnh ftp.request.command == "PASS" để tìm mật khẩu nhanh chóng 

$dhs khong import anh duoc$

-> PASS= AfricaCTF2021

Q2: What is the IPv6 address of the DNS server used by 192.168.1.26?

dùng lệnh ip.addr== 192... and ipv6 and dns để tìm địa chỉ ip

tuy nhiên 192.168.1.26 là ipv4 nên sẽ không cho ra kết quả nào

$insert ảnh vô$

Ta cần phải tìm địa chỉ MAC

Dùng lệnh ip.addr== 192...

Vào 1 gói tin bất kỳ, nhìn phần Ethernet II ra ta có thể lấy được địa chỉ MAC của máy tại : Ethernet II, Dst: c8:09:a8:57:47:93

Có địa chỉ MAC có thể tìm kiếm được địa chi ipv6 bằng lệnh: eth.addr==ca:0b:ad:ad:20:ba and ipv6 and dns

Lấy được ipv6 ở phần Internet Protocol: Internet Protocol Version 6, Src: fe80::c80b:adff:feaa:1db7

Q3: What domain is the user looking up in packet 15174?

Dùng Ctrl + G và nhập 15174 để đi nhanh đến gói tin

Ta lấy được tên miển ( Domain ) ở cột info: www.7-zip.org

Q4: How many UDP packets were sent from 192.168.1.26 to 24.39.217.246?

Dùng lệnh "ip.src == 192.168.1.26 and ip.dst == 24.39.217.246 and udp" để lấy các gói tin có nguồn và đích đến như kia dùng giao thức UDP

Thanh bar dưới cùng bên phải sẽ cho ta biết là có bao nhiêu gói tin: 10.

Q5: What is the MAC address of the system under investigation in the PCAP file?

Cần phải biết địa chỉ ip của nạn nhân là gì.

Trên thanh công cụ dùng Statistics vào lần lượt phần ipv4 và ipv6 để kiểm tra phát hiện ip 192.168.1.26 nhiều bất thường -> ip nạn nhân

Dùng lệnh ip.addr=192... rồi vào 1 gói tin bất kỳ, nhìn phần Ethernet II ra ta có thể lấy được địa chỉ MAC của máy tại : Ethernet II, Src: c8:09:a8:57:47:93

Q6: What was the camera model name used to take picture 20210429_152157.jpg?

Dùng lệnh frame contains "20210429_152157.jpg" để tìm xem gí tin nào chứa chuỗi ký tự này

Tiếp ta cần lưu bức ảnh về máy. Chuột phải vào gói tin rồi nhấn Follow -> TCPstream

Ta thấy có lệnh STOR 20210429_152157.jpg tức có người đã tấn công và tải ảnh lên FTP

Dùng lệnh ftp-data để kiểm tra các gói tin mang theo file

Thấy được nhiều gói tin gửi cùng 1 bức ảnh. Tiến hành Follow TCP stream 1 gói tin bất kỳ Wireshark sẽ gộp tất cả lại

Chỉnh show as thành Raw và save as với duôi .jpg

Ta được hình ảnh: $insert ảnh vô 

Để kiểm tra camera model click phải vào ảnh, vào properties sang phần details và kéo xuống sẽ thấy model camera: LM-Q725K

Q7: What is the ephemeral public key provided by the server during the TLS handshake in the session with the session ID: da4a0000342e4b73459d7360b4bea971cc303ac18d29b99067e46d16cc07f4ff?

Dùng lệnh tls.handshake.session_id == da4a0000342e4b73459d7360b4bea971cc303ac18d29b99067e46d16cc07f4ff để tìm gói tin có dòng chữ: Server Key Exchange

Nhìn xuống packet details mở mục Transport Layer Security

Mở mục TLSv1.2 Record Layer: Handshake Protocol: Server Key Exchange.

Mở tiếp Handshake Protocol: Server Key Exchange.

Mở mục EC Diffie-Hellman Server Params ta lấy được Pubkey: 04edcc123af7b13e90ce101a31c2f996f471a7c8f48a1b81d765085f548059a550f3f4f62ca1f0e8f74d727053074a37bceb2cbdc7ce2a8994dcd76dd6834eefc5438c3b6da929321f3a1366bd14c877cc83e5d0731b7f80a6b80916efd4a23a4d

Q8: What is the first TLS 1.3 client random that was used to establish a connection with protonmail.com?

Dùng lệnh tls.handshake.extensions_server_name == "protonmail.com" để tìm kiếm các gói tin Client Hello 

Dưới phần Packet Details ta lấy được Client random là: 24e92513b97a0348f733d16996929a79be21b0b1400cd7e2862a732ce7775b70

Q9: Which country is the manufacturer of the FTP server’s MAC address registered in?

Từ câu trước ta biết rằng hacker đã lập ra 1 máy chủ ftp để gửi ảnh lên với ip 192.1.168.20

Dùng lệnh ip.src == 192.168.1.20 để tìm các gói tin có source từ ip này

Chọn bất kỳ gói tin nào, xuống phần packet detaisl để lấy địa chỉ MAC

Lên hwaddress.com để tra địa chỉ MAC biết được quốc gia là: United States

Q10: What time was a non-standard folder created on the FTP server on the 20th of April?

Để tìm thư mục được tạo trên máy chủ ftp ta dùng lệnh ftp.request.command == "MKD", do muốn tạo thư mục trên ftp sever thì cần gõ một lệnh có tên là MKD

Không kiếm được gói tin nào -> Wireshark bắt đầu ghi lại luồng mạng sau khi cái thư mục đó đã được tạo ra

Dùng lệnh ftp-data, thấy có gói tin với info LIST -? follow tcp stream ta thấy có folder tạo vào ngày 20 tháng 4 và bên cạnh là giờ tạo: 17:53

Q11: What URL was visited by the user and connected to the IP address 104.21.89.171?

Dùng lệnh ip.addr == 104.21.89.171 && http.request để lọc ra các gói tin mà địa chỉ ip kia đang giao tiếp kèm theo http.request để lọc việc truy cập URL

Thấy có 1 gói tin, chọn gói tin và xuống packet details, mở rộng Hypertext Transfer Protocol ta thấy dòng full request sẽ hiện ra URL: [Full request URI: http://dfir.science/]
