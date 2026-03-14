Q1: What is the FTP password? 

FTB là giao thức sẽ truyền tải mọi thứ dưới dạng Cleartext (văn bản thuần, không mã hóa).
nhập câu lệnh ftp.request.command == "PASS" để tìm mật khẩu nhanh chóng 

$dhs khong import anh duoc$
![image alt](https://github.com/huanvche190175-spec/img/blob/4fccd334f514f16651c6736b008e01bf41a2dc73/imgreal.jpg)

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
