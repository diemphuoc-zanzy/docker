## Docker - Giải pháp cho việc ảo hóa cấp hệ điều hành trên Linux
Là một developer, một trong các vấn đề mà các bạn quan tâm đến khi bắt đầu học một ngôn ngữ lập trình mới đó là môi trường để setup và các package đi kèm để có thể chạy được ứng dụng khi các bạn xây dựng lên? Nhiều lúc bạn sẽ cảm thấy căng thẳng và nhiều lúc hơi bực mình vì tốc độ build hoặc chạy server ứng dụng quá lâu vì bạn phải cài quá nhiều package hoặc môi trường trên máy (điều này giành cho các bạn học nhiều ngôn ngữ hoặc cái nhiều phần mềm học các công nghệ)? Hay khi bạn có 1 đứa bạn nhờ bạn chạy code, và nó chỉ đưa cho bạn source code, và khi đó bạn lại phải hì hục cài môi trường vào máy cho nó, lại một loạt package phải cài đặt... Mình cũng đã từng rơi vào tình cảnh phải chạy API bằng rails trên windows trên máy của 1 đứa bạn, và bao nhiêu rắc rối để có thể chạy server và hướng dẫn nó sử dụng. Gần đây mình mới biết đến 1 công nghệ khá hay đó là Docker, nó có thể giải quyết hầu hết các câu hỏi mà mình đã nêu ra ở trên. Hôm nay mình xin giới thiệu đến các bạn một chút căn bản về Docker (giành cho developers) để các bạn có thể có 1 cái nhìn mới về việc chạy ứng dụng mà máy mình không phải cài thêm phần mềm hay các package làm cho máy càng ngày càng nặng và tốc độ chạy thì chậm.

## Docker là gì ?
Docker là nền tảng phần mềm hàng đầu thế giới. Các developer sử dụng Docker để loại bỏ vấn đề "works on my machine" khi cộng tác trên mã code với các đồng nghiệp. Docker mang lại nhiều tiện ích cho dev: - Tự động hóa các nhiệm vụ lặp đi lặp lại trong việc thiết lập và cấu hình môi trường phát triền, giúp cho các nhà phát triển có thể tập trung vào những vấn đề quan trọng hơn đó là xây dựng phần mềm thật tốt. - Các developer sử dụng Docker không phải cài đặt hoặc cấu hình cơ sở dữ liệu phức tạp cũng không phải lo lắng về việc chuyển đổi giữa các phiên bản ngôn ngữ. Việc đó được xử lý 1 cách đơn giản với Dockerfiles. - Image: là template định nghĩa các thành phần, các ứng dụng được cài đặt trong 1 container của Docker. Image được sử dụng để tạo ra các Container - Container: đóng gói thành phần (được định nghĩa trong image )ở định dạng có thể chạy tách biệt mới trên hệ điều hành chia sẻ.

## Cấu trúc của Docker
Chắc hẳn các bạn đã từng biết đến các công nghệ máy ảo như VMWare hay Virtualbox, Docker cũng có cấu trúc cơ bản giống với chúng nhưng điều đặc biệt hơn từ Docker đó là bạn không phải tốn bất kỳ 1 chút bộ nhớ nào của máy để build lên các OS cho môi trường ảo mà bạn cần dụng:

<p align="center">
  <img src="https://images.viblo.asia/4ab5ee32-3c44-4e59-8703-cb04863f3b57.jpg" width="90%" alt="Docker" />
</p>

Không giống các máy áo, Docker container không phải build 1 hệ điều hành hoàn chỉnh, chỉ cần có các thư viện và các thiết lập cần thiết để tạo ra môi trường làm việc. Điệu này làm cho hiệu quả, nhẹ, khép kín hệ thống và đảm bảo rằng phần mềm sẽ luôn luôn chạy như nhau, bất kể nó được triển khai ở đâu. Docker cung cấp thêm một lớp trừu tượng và tự động hóa việc ảo hóa cấp hệ điều hành trên Linux, chính vì vậy, dù bạn có sài Docker trên Windows hay MacOs, thì nó vẫn có nhân là Linux.

Chắc hẳn đến đây, bạn cũng có thể hình dung được cơ bản nhất Docker là gì và nó giúp bạn những gì rồi đúng không. Chúng ta cùng làm 1 ví dụ đơn giản về Docker nhé.

## Sử dụng
Mình là một Java Dev nên trong bài viết này, mình sẽ thực hành với việc cài đặt các package đi kèm bằng Dockerfiles để có môi trường phát triển cho Java và framework Spring Boot nhé.


```dockerfile

```

Vậy là mình đã có được 1 Dockerfile chứa đầy đủ các package cần phải có để lập trình ROR. Bước tiếp theo, bạn sẽ phải build image từ chính Dockerfile này. Để thực hiện việc này bạn cần chạy:
```dockerfile
docker build -t java:docker .
```

và kết quả bạn nhận được:
```dockerfile
Successfully built 0558b7bf7525
Successfully tagged java:docker
```

Có 2 cách để bạn có thể build image từ Dockerfile: Cách 1: là build trực tiếp từ Dockerfile như mình đã làm. Cách 2: build automatic từ github sang dockerhub (mình sẽ giới thiệu trong các bài viết tiếp theo). Như vậy là chúng ta đã có 1 image chứa đầy đủ các thứ cần thiết để lập trình ROR rồi đó.
Hãy bắt đầu kiểm tra nhé:

<li> Đầu tiền: bạn hãy chạy câu lệnh sau để dựng môi trường bằng image vừa có nhé:</li>

```dockerfile
docker run -ti ruby:docker /bin/bash
```

check qua môi trường nhé:
```dockerfile
root@0625925cc8d4:/ruby-2.4.2# ruby -v
ruby 2.4.2p198 (2017-09-14 revision 59899) [x86_64-linux]
root@0625925cc8d4:/ruby-2.4.2# rails -v
Rails 5.1.4
```
Thật đơn giản đúng không nào, không phải cài đặt bất cứ thứ gì vào máy của bạn. Bây giờ bạn có thể tạo một project ROR mới và làm việc dễ dàng. Trên đây là 1 ví dụ đơn giản về việc build môi trường cho ROR bằng Dockerfile để thấy được sức mạnh của Docker. Trong các bài viết tiếp theo, mình sẽ giới thiệu các bạn về các option trong Dockerfile và 1 cái hay ho nữa đó là docker-compose.





















