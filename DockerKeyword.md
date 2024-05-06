## Dockerfile references
sau đây là các references để viết trong Dockerfile

### 1. FROM
```dockerfile
FROM <image> [AS <name>]
```
or
```dockerfile
FROM <image>[:<tag>] [AS <name>]
```
or
```dockerfile
FROM <image>[@<digest>] [AS <name>]
```

- Các bạn có thể hiểu đơn giản, đây là thằng trung tâm mà bạn sẽ sử dụng nó làm cái base cho các action ở phía sau. Một Dockerfile hợp lệ phải bắt đầu bằng lệnh FROM. Bạn có thể sử dụng các images của mình tạo ra, hoặc có thể sử dụng hình ảnh do đồng nghiệp trong team tạo sẵn, hoặc đơn giản hơn là nó được chính thằng Docker build lên.
- FROM có thể xuất hiện nhiều lần trong 1 Dockerfile để tạo nhiều ảnh hoặc sử dụng trong 1 giai đoạn build khác nhau. VỚi mỗi lệnh FROM, sẽ xóa đi trạng thái của FROM được tạo ra ở trước đó.
- Tag và digest là các tùy chọn của image, mặc định nếu không có tag thì nó sẽ lấy version cuối cùng: latest. Example: FROM ubuntu:16:04 AS ubuntu Ở đây, mình đang sử dụng images được Docker build lên có tên là ubuntu, với tag là 16.04, và mình đặt cho nó một cái định danh ngắn gọn là ubuntu. Có một từ khóa nữa mà chắc bạn sẽ gặp nó ở đâu đó rồi, vì nó là thằng duy nhất có thể đứng trước FROM trong Dockerfile, đó là ARG.
- ARG thường khai báo các giá trị của biến hỗ trợ cho quá trình build từ FROM. Thường là khai báo tag của image mà mình muốn sử dụng:

```dockerfile
ARG  CODE_VERSION=16.04
FROM ubuntu:${CODE_VERSION}
CMD  /code/run-app
```

### 2. RUN
Với câu lệnh RUN, có 2 forms cho các bạn lựa chọn:

```dockerfile
RUN <command>
```

(shell form, command được chạy bên trong shell, mặc định là /bin/sh -c trên Linux or cmd /S /C trên Windows). or

```dockerfile
RUN ["executable", "param1", "param2"]
```
(exec form)
- Lệnh RUN sẽ thực hiện bất kỳ lệnh nào trên layer của image hiện tại và hiển thị kết quả. Kết quả của nó thường sẽ được sử dụng cho bước tiếp theo trong Dockerfile.
- Việc phân lớp các lệnh RUN và tạo ra các commit theo các concepts của Docker, nới có các commit dễ dàng và các container có thể được tạo từ một số điểm trong lịch sử của image, giống như kiểm soát nguồn.
- Shell mặc định cho form shell có thể thay đổi bằng cách sử dụng lệnh SHELL.
- Trong shell, bạn có thể sử dụng .\ để tiếp tục RUN trên câu lệnh RUN trong dòng tiếp theo trên cùng một line. Ví dụ:

```dockerfile
RUN /bin/bash -c 'source $HOME/.bashrc; \
echo $HOME'
```
Chúng cùng lúc được chạy trên một dòng
```dockerfile
RUN /bin/bash -c 'source $HOME/.bashrc; echo $HOME'
```

### 3. CMD
#### VỚi CMD, có 3 forms cho việc thực hiện:
```dockerfile
CMD ["executable","param1","param2"]
```
(hình thức exec, đây là hình thức được mọi người ưa thích sử dụng).
```dockerfile
CMD ["param1","param2"]
```
(là tham số mặc định cho ENTRYPOINT).
```dockerfile
CMD command param1 param2
```
(shell form).
- Trong mỗi Dockerfile chỉ được có duy nhất một câu lệnh CMD. Nếu bạn sử dụng nhiều hơn 1 câu lệnh CMD thì chỉ có câu lệnh CMD cuối cùng được chạy.
- Mục đích chính của CMD là cung cấp lệnh chạy mặc định cho 1 container. Những điều mặc này có thể bảo gồm một câu lệnh thực thi, hoặc chúng có thể bỏ qua các câu lệnh thực thi, trong trường hợp đó bạn cũng phải chỉ định một lệnh ENTRYPOINT.
- Khi bạn sử dụng hình thức shell cho lệnh CMD, thì <command> sẽ thực thi với /bin/sh -c:

```dockerfile
FROM ubuntu
CMD echo "This is a test CMD."
```
Còn nêu bạn không muốn chạy <command> với sherll thì bạn cần phải trình bày đầy đủ câu lệnh với đường dẫn chi tiết như mảng JSON:
```dockerfile
FROM ubuntu
CMD ["/usr/bin/wc","--help"
```
Còn nếu bạn không muốn container của bạn chạy 1 câu lệnh trong các lần sử dụng, bạn có thể xem xét việc sử dụng ENTRYPOINT bên với câu lệnh CMD

#### 4. MAINTAINER
```dockerfile
MAINTAINER <name>
```
Đây là một option giúp cho bạn có thể biết ai là người viết và build lên images cho mình sử dụng.
```dockerfile
MAINTAINER NGUYEN HA PHAN
```

#### 5. ADD
Có 2 hình thức sử dụng:

```dockerfile
ADD <src>... <dest>
```
or
```dockerfile
ADD ["<src>",... "<dest>"]
```
(hình thức này yêu cầu cho các đường dẫn chứa khoảng trắng).
- ADD sẽ sao chép các files, thư mục hoặc remote của file từ URLs từ <src> và thêm vào hệ thống tệp của hình ảnh ở đường dẫn <dest>
- Nhiều tài nguyên <src> có thể được chỉ định nhưng nếu chúng là các tệp hoặc thư mục thì chúng phải tương đối so với thư mục nguồn đang được xây dụng.
- Mỗi <src> có thể chứa ký tự đạị diện và kết hợp sẽ được thực hiện bằng cách sử dụng luật Go's filepath.Match:

```dockerfile
ADD test relativeDir/          # adds "test" to `WORKDIR`/relativeDir/
ADD test /absoluteDir/         # adds "test" to /absoluteDir/
```
Khi thêm tệp hoặc thư mục chứa các ký tự đặc biệt (như [ và ]), cần phải thoát khỏi những đường dẫn theo nguyên tắc Golang để ngăn chúng không được coi như là mẫu phù hợp. VD: để thêm file có tên là arr[0].txt, sử dụng như sau:
```dockerfile
ADD arr[[]0].txt /mydir/    # copy a file named "arr[0].txt" to /mydir
```

### 6. EXPOSE
EXPOSE <port> [<port>/<protocol>...]
Lệnh EXPOSE thông báo cho Docker cho container lắng nghe trên các cổng mạng được chỉ định khi chạy. Bạn có thể chỉ định cổng lắng nghe TCP hay UDP, và mặc định của nó sẽ là TCP nếu giao thức không được chỉ định. Lệnh EXPOSE không thực sự publish cổng. Nó hoạt động như một loại tài liệu hướng dẫn giữa người xây dựng images và người chạy container, về những cổng nào dự định được published. Để thực sự publish cổng khi chạy container, sử dụng -p để cho phép docker chạy và map đến một hoặc nhiều cổng, hoặc -P để cho phép nó tạo các cổng và map chúng tới các cổng cao cấp. Để thiết lập chuyển hướng cổng trên hệ thống máy chủ, sử dụng option -P. Lệnh docker network hỗ trợ tạo ra các mạng cho việc truyền thông giữa các container mà không cần phải publish cổng hoặc hiển thị, bới bì container kết nối đến mạng có thể giao tiếp với nhau qua bất kì cổng nào. Đây là một phần ứng dụng khá hay của Docker cho phép các kỹ sư mạng, mình sẽ tìm hiểu nó chi tiết hơn trong 1 bài viết sắp tới!!!!.

### 7. COPY
COPY có hai forms chính cho bạn sử dụng:
```dockerfile
COPY <src>... <dest>
```
or
```dockerfile
COPY ["<src>",... "<dest>"]
```
(hình thức này yêu cầu cho các đường dẫn có chứa khoảng trắng).

- COPY sao chép các tệp tin hoặc thư mục từ <src> và thêm chúng vào hệ thống tệp tin của container ở đường dẫn <dest>.
- Nhiều tài nguyên từ <src> có thể được chỉ định nhưng chúng phải liên quan với thư mục nguồn đang được xây dựng (bối cảnh của việc xây dựng).
- Với mỗi <src> có thể chứa ký tự đại diện và kết hợp sẽ được thực hiện bằng sử dụng luật Go's filepath.Match. Ví dụ:

```dockerfile
COPY hom* /mydir/        # sao chép tất cả các tệp tin có tên bắt đầu bằng "hom" vào thư mực mydir
COPY hom?.txt /mydir/    # ? được thay thế bằng bất kỳ ký tự đơn, ví dụ., "home.txt"
```

<dest> là một đường dẫn tuyệt đối, hoặc một path liên quán đến WORKDIR, trong đó resource sẽ được sao chép bên trong container đích.

```dockerfile
COPY test relativeDir/   # sao chép "test" vào `WORKDIR`/relativeDir/
COPY test /absoluteDir/  # sao chép "test" to /absoluteDir/
```
Khá giống với ADD, khi sao chép các tệp tin hoặc thư mục có các ký tự đặc biệt, cần phải escape những đường dẫn theo quy tắc Golang để ngăn chúng khỏi bị hiểu nhầm như một mẫu phù hợp. Ví dụ tệp tin có têm là arr[0].txt, sử dụng như sau:

```dockerfile
COPY arr[[]0].txt /mydir/    # sao chép tệp tin có tên "arr[0].txt" vào /mydir/.
```

### 8. VOLUME
Command: ``` VOLUME ["/data"] ```

<p align="center">
    <img src="https://images.viblo.asia/93bcf53e-5a17-441d-b989-dd0611ea805c.png" width="90%" alt="Docker">
</p>

Lệnh VOLUME tạo ra một điểm kết nối với tên được chỉ định và đánh dấu nó như là giữ các khối được gắn từ bên ngoài máy chủ lưu trữ hoặc từ các container khác. Giá trị có thể là mảng JSON, VOLUME ["/var/log"], hoặc một chuỗi với nhiều đối số, giống như VOLUME /var/log hoặc VOLUME /var/log /var/db. Để hiểu thêm về nó, hoặc các ví dụ đơn giản, bạn có thể xem tại Share Directories via Volumes Lệnh chạy docker docker run khởi tạo số lượng mới được tạo ra với bất kỳ dữ liệu nào tồn tại tại vị trí được chỉ định bên trong image cơ sở. Ví dụ:

```dockerfile
FROM ubuntu
RUN mkdir /myvol
RUN echo "hello world" > /myvol/greeting
VOLUME /myvol
```

Kết quả của Dockerfile trong image được tạo ra khi docker run, một điểm mới được tạo ra /myvol và sao chép tệp tin greeting vào trong volume vừa được tạo.

### 9. ENTRY POINT
Với ENTRY POINT có hai hình thức sử dụng chính:

```dockerfile
ENTRYPOINT ["executable", "param1", "param2"]
```
(hình thức thực thi, được nhiều người sử dụng). or
```dockerfile
ENTRYPOINT command param1 param2
```
(hình thức shell).
    <li>ENTRYPOINT cho phép cấu hình container sẽ chạy như một tệp tin thi hành. Ví dụ: start nginx với nội dung mặc định, lắng nghe trên cổng 80:</li>
```dockerfile
docker run -i -t --rm -p 80:80 nginx
```

<b>NOTE</b>: cũng giống như CMD, mỗi một tệp Dockerfile cũng chỉ có một câu lệnh ENTRYPOINT có hiệu lực.

### 10. WORKDIR
Command:
```dockerfile
WORKDIR /path/to/workdir
```
WORKDIR thiết lập thư mục làm việc cho các lệnh RUN, CMD, ENTRYPOINT, COPY và ADD nào nằm trong Dockerfile. Nếu WORKDIR không tồn tại, nó sẽ được tạo ra ngay cả khi nó không được sử dụng trong bất kỳ lệnh Dockerfile tiếp theo nào. WORKDIR có thể sử dụng nhiều lần trong Dockerfile. Nếu đường dẫn được cung cấp là tương đối, nó sẽ tương ứng với đường dẫn của lệnh WORKDIR trước đó. Ví dụ:

```dockerfile
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
```

Đầu ra của lệnh pwd cuối cùng trong Dockerfile này sẽ là /a/b/c. WORKDIR có thể giải quyết các biến môi trường được đặt trước bằng ENV. Chỉ có thể sử dụng các biến môi trường được đặt rõ ràng trong Dockerfile

```dockerfile
ENV DIRPATH /path
WORKDIR $DIRPATH/$DIRNAME
RUN pwd
```

Đầu ra của lệnh pwd cuối cùng trong tệp Dockerfile này sẽ là /path/$DIRNAME.

Trên đây mình giới thiệu một số lệnh của Dockerfile được sử dụng khá thông dụng trong quá trình tạo Dockerfile. Tuy chưa đầy đủ nhưng với những lệnh này, nếu nắm rõ chúng thì việc thiết lập môi trường phát triển ứng dụng khá là dễ dàng.




