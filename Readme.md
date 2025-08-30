# Học Docker cơ bản

## Tạo docker file

```
#syntax=docker/dockerfile:1

FROM node:lts-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
```

### Giải thích

`FROM node:lts-alpine`

-   `FROM` là hướng dẫn đầu tiên và quan trọng nhất. Nó định nghĩa image cơ sở (base image) mà image của bạn sẽ được xây dựng dựa trên.
-   `node` là image chính thức của Node.js.
-   `alpine` là một phiên bản Linux rất nhỏ và nhẹ, giúp giảm đáng kể kích thước của image cuối cùng.

`WORKDIR /app`

-   `WORKDIR` (Work Directory) đặt thư mục làm việc cho tất cả các lệnh tiếp theo (như `COPY` và `RUN`).
    Trong trường hợp này, nó sẽ tạo một thư mục /app bên trong container và chuyển tất cả các lệnh sau đó vào làm việc trong thư mục này.

`COPY . .`

-   `COPY` sao chép các tệp và thư mục từ máy tính của bạn (máy chủ) vào container.

-   `.` (dấu chấm đầu tiên) đại diện cho tất cả các tệp và thư mục trong thư mục hiện tại của bạn (nơi chứa Dockerfile).

-   `.` (dấu chấm thứ hai) đại diện cho thư mục làm việc (/app) bên trong container mà bạn đã đặt ở bước trước.

-   Lệnh này sẽ sao chép toàn bộ mã nguồn ứng dụng của bạn vào thư mục /app trong container.

`RUN yarn install --production`

-   RUN chạy một lệnh trong môi trường của container và cam kết kết quả vào một layer mới của image.
-   `yarn install --production` là lệnh được thực thi. Nó cài đặt tất cả các gói phụ thuộc (dependencies) của ứng dụng được liệt kê trong `package.json`. Cờ `--production` đảm bảo chỉ các gói cần thiết cho môi trường sản xuất được cài đặt, giúp giảm dung lượng image.

`CMD ["node", "src/index.js"]`

-   CMD (Command) chỉ định lệnh mặc định sẽ được thực thi khi một container được khởi động từ image này.
-   `["node", "src/index.js"]` là lệnh khởi động ứng dụng Node.js của bạn. Nó sẽ chạy tệp `index.js` bằng trình thông dịch `node`.
-   Một Dockerfile chỉ nên có một lệnh `CMD`.

`EXPOSE 3000`

-   EXPOSE thông báo cho Docker biết rằng container sẽ lắng nghe trên cổng 3000 khi chạy.

-   Lưu ý quan trọng: Lệnh này chỉ là tài liệu và không tự động ánh xạ cổng. Để truy cập ứng dụng từ bên ngoài, bạn vẫn cần sử dụng cờ `-p` (hoặc `--publish`) với lệnh `docker run`. Ví dụ: `docker run -p 3000:3000 <tên_image>`.

## Buid image

Mở teminal tại thư mục dự án. Build image theo cú pháp sau: `docker build -t getting-started .`

-   `-t`: tags của iamge
-   `.`: dấu chấm cuối cùng của lệnh nói cho Docker hãy tìm kiếm file `Dockerfile` ở thư mục hiện tại

## Chạy 1 app container

`docker run -d -p 127.0.0.1:3000:3000 getting-started`

-   `docker run`: Đây là lệnh chính để tạo và khởi động một container mới từ một image. Nó là sự kết hợp của `docker create` và `docker start`.
-   `-d ( --detach)`: lệnh này yêu cầu docker chạy container ở chố độ nền (detached). Container sẽ chạy mà không chiếm giữ terminal của bạn, cho phép bạn tiếp tục gõ lệnh.
-   `127.0.0.1:3000:3000`: Cấu trúc này có định dạng `IP_máy_chủ:cổng_máy_chủ:cổng_container`. Lệnh này cho phép bạn truy cập ứng dụng đang chạy bên trong container bằng cách mở trình duyệt và gõ http://127.0.0.1:3000 hoặc http://localhost:3000.
-   `getting-started`: Tên của image Docker mà bạn muốn sử dụng để tạo container. Docker sẽ tìm image này trên máy của bạn và sử dụng nó làm "khuôn" để tạo container mới.
