# ARP-protocol
Như đã biết trong mô hình OSI, gói tin ở lớp 3 có địa chỉ IP nguồn, địa chỉ IP đích sau đó sẽ được chuyển xuống lớp 2 đóng thành các frame trong đó có MAC đích và MAC nguồn rồi sau đó mới có thể  truyền thông tin giữa hai máy tính trong cùng một mạng vật lý 

<img src="http://i.imgur.com/ZvtsjE6.jpg">

ARP là phương thức phân giải địa chỉ động giữa địa chỉ lớp network và địa chỉ lớp datalink. Quá trình thực hiện bằng cách: một thiết bị IP trong mạng gửi một gói tin broadcast đến toàn mạng yêu cầu thiết bị khác gửi trả lại địa chỉ phần cứng ( địa chỉ lớp datalink ) của mình.

ARP về cơ bản là một quá trình 2 chiều request/response giữa các thiết bị trong cùng mạng nội bộ. Thiết bị nguồn request bằng cách gửi một bản tin broadcast trên toàn mạng. Thiết bị đích response bằng một bản tin unicast đến thiết bị nguồn

Có hai dạng bản tin trong ARP : một được gửi từ nguồn đến đích, và một được gửi từ đích tới nguồn.
- Request : Khởi tạo quá trình, gói tin được gửi từ thiết bị nguồn tới thiết bị đích
- Reply : Là quá trình đáp trả gói tin ARP request, được gửi từ máy đích đến máy nguồn

Thông thường, máy gửi đã biết địa chỉ IP của máy nhận. Vì thế, sau khi xác định được địa chỉ IP của máy nhận thì lớp Network của máy gửi sẽ so sánh địa chỉ IP của máy nhận và địa chỉ IP của chính nó:
Nếu cùng địa chỉ mạng thì máy gửi sẽ tìm trong bảng MAC table của mình để có được địa chỉ MAC của máy nhận. Trong trường hợp không có được địa chỉ MAC tương ứng, nó sẽ thực hiện giao thức ARP để truy tìm địa chỉ MAC. Sau khi tìm được địa chỉ MAC, nó sẽ lưu địa chỉ MAC này vào trong bảng MAC table để lớp Datalink sử dụng ở các lần gửi sau. Sau khi có địa chỉ MAC thì máy gửi sẽ gởi gói tin đi.

### DEMO
```sh
Máy A có địa chi IP là :` 192.168.1.11 `  địa chỉ MAC : ` 00-0C-29-F3-01-12`
Máy B có địa chỉ IP là : 192.168.1.19
Máy A muốn tìm địa chỉ MAC của máy B
Trên máy A ta mở cmd sử dụng câu lệnh:
`arp –a`
Ta thấy hiện tại trong MAC table của máy A chưa có địa chỉ MAC của máy B
Ta thử ping đến địa chỉ IP của máy B đã biết bằng cách gõ:
`Ping 192.168.1.19`
```
<img src="http://i.imgur.com/Yx2PoEC.png">
Và kết quả thu được là đã có địa chỉ MAC của máy B trong MAC table.
<img src="http://i.imgur.com/JJiVcWL.png">

TIếp theo tại máy B:
<img src="http://i.imgur.com/FaW9cOG.png">

Đây là hình ảnh ở máy B ( sử dụng HĐH Ubuntu server)  chúng ta có thể thấy ban đầu tôi dùng lệnh:

	`arp – n`
Để kiểm tra xem có địa chỉ MAC của máy A có lưu trong MAC table của máy B không. Sau đó, tôi dung công cụ tcpdump để kiểm tra việc liên lạc giữa 2 máy bằng giao thức ARP, với câu lệnh là:

`sudo tcpdump –vnes0 –I eth0 arp` 

sau khi ở máy A tôi thực hiên việc ping đến địa chỉ IP của máy B chúng ta sẽ thấy . Đầu tiên, máy A phát ra một gói tin ARP request (dạng broadcast) trên mạng yêu cầu tìm MAC của máy nào có IP là `192.168.1.19` vì là gói tin broadcast nên các máy trên mạng sẽ nhận gói tin này và xử lý. Mỗi máy sẽ kiểm tra xem địa chỉ `192.168.1.19` có phải là IP của mình không. Nếu ko thì nó loại gói tin này. 

Trong trường hợp này máy B nhận thấy đây đúng là địa chỉ IP của máy mình vì vậy nó sẽ lấy địa chỉ MAC của nó và gửi gói tin ARP reply (dạng unicast) về cho máy A. Đồng thời máy B cũng cập nhật MAC table địa chỉ IP và MAC của máy A vào bảng MAC table của mình để giảm bớt thời gian xử lý cho các lần sau.
Lúc này máy A đã có địa chỉ MAC của máy B. Nó sẽ lưu và MAC table của nó.

Sau khi hoàn thành ta kiểm tra lại xem máy B đã có ARP của máy A chưa gõ: `arp –n`
Và kết quả như trong hình, MAC table của máy B đã cập nhật địa chỉ MAC của máy A.




