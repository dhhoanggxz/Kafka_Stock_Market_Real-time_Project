## Dưới đây là một hướng dẫn chi tiết về các bước thực hiện dự án Stock Market real-time, từ việc thiết lập môi trường đến việc truy vấn dữ liệu:

![Architecture](https://github.com/user-attachments/assets/9c55a134-7f0c-4668-aabb-01d0f30654a7)


### 1. **Cài đặt môi trường phát triển**

   - **Hệ điều hành**: Cài đặt Ubuntu trên Windows (có thể sử dụng WSL hoặc máy ảo).
   - **Cài đặt Java**: Tải và cài đặt Java Development Kit (JDK) để chạy Kafka.
   - **Cài đặt Kafka**: Tải Kafka từ trang chính thức và giải nén vào thư mục làm việc.

### 2. **Cài đặt Zookeeper và Kafka**

   - **Khởi động Zookeeper**:
     ```bash
     bin/zookeeper-server-start.sh config/zookeeper.properties
     ```
   - **Khởi động Kafka**:
     ```bash
     bin/kafka-server-start.sh config/server.properties
     ```
   - **Cấu hình Kafka**: Thêm dòng sau vào file `config/server.properties` để điều chỉnh bộ nhớ:
     ```bash
     export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"
     ```

### 3. **Cấu hình IP**

   - **Chỉnh sửa file `server.properties`** để thay đổi từ IP Private thành Public:
     ```bash
     sudo nano config/server.properties
     ```

### 4. **Tạo Producers và Consumers**

   - Mở thêm 2 phiên Ubuntu khác để tạo các server Producer và Consumer.
   - Sử dụng các lệnh sau để tạo các topic:
     ```bash
     bin/kafka-topics.sh --create --topic demo_test --bootstrap-server <your_public_ip>:9092 --replication-factor 1 --partitions 1
     ```

### 5. **Cấu hình AWS**

   - **Tạo IAM Role**: Vào AWS IAM, tạo một Role với quyền truy cập S3 và Glue.
   - **Tạo Access Key**: Tải xuống file CSV chứa Access Key và Secret Key.

### 6. **Cấu hình AWS CLI**

   - **Cấu hình AWS CLI**:
     ```bash
     aws configure
     ```
   - Nhập `AWS Access Key ID`, `AWS Secret Access Key`, `Default region name`, và `Default output format`.

### 7. **Sử dụng Jupyter Notebook**

   - **Cài đặt thư viện cần thiết**: Chạy các lệnh sau trong Jupyter Notebook:
     ```bash
     pip install kafka-python
     pip install s3fs
     ```
   - **Viết mã cho Producer**: Tạo Producer để gửi dữ liệu lên Kafka từ file CSV.
   - **Viết mã cho Consumer**: Tạo Consumer để nhận dữ liệu từ Kafka và lưu vào S3 bucket.

### 8. **Crawl Data từ S3 bằng AWS Glue**

   - **Tạo Crawler**: Vào AWS Glue, tạo một Crawler để quét dữ liệu trong S3.
   - **Tạo Database**: Tạo một database mới trong Glue để lưu thông tin cấu trúc dữ liệu.

### 9. **Truy vấn dữ liệu bằng AWS Athena**

   - **Mở AWS Athena**: Vào trang AWS Athena và chọn database đã tạo.
   - **Viết câu truy vấn**: Sử dụng SQL để truy vấn dữ liệu từ các bảng mà Glue đã tạo.

### 10. **Lấy dữ liệu trực tiếp từ web (Nếu cần)**

   - **Sử dụng API**: Nếu muốn lấy dữ liệu real-time từ web, sử dụng các API từ các trang như Alpha Vantage hoặc IEX Cloud.
   - **Thêm logic vào Producer**: Cập nhật Producer để lấy dữ liệu từ API và gửi vào Kafka.

### Lưu ý

- Kiểm tra các lỗi truy cập (Access Denied) trên IAM để đảm bảo rằng IAM Role có quyền truy cập S3 và Glue.
- Đảm bảo rằng bạn có đủ bộ nhớ trên server để chạy Kafka mà không gặp lỗi về bộ nhớ.
