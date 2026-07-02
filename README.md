# BÀI LAB: GIÁM SÁT HIỆU NĂNG VÀ AN NINH HỆ THỐNG LINUX THEO THỜI GIAN THỰC VỚI NETDATA

## 1. Tổng quan dự án (Project Overview)
Dự án xây dựng giải pháp giám sát hiệu năng (Performance) và phát hiện các hành vi bất thường (Anomalies Detection) trên hệ điều hành Linux (Kali Linux) sử dụng công cụ **Netdata**. Hệ thống cho phép người quản trị theo dõi trạng thái phần cứng theo từng giây, từ đó phát hiện sớm các nguy cơ tấn công vắt kiệt tài nguyên (DoS/DDoS) hoặc các tiến trình độc hại chạy ngầm (Malware, Crypto Miner).

## 2. Các tính năng cốt lõi
- **Giám sát thời gian thực (Real-time):** Thu thập thông số CPU, RAM, Disk, Network I/O liên tục với tần suất 1s/lần.
- **Hệ thống cảnh báo chủ động (Alarms):** Tự động kích hoạt trạng thái Warning/Critical khi tài nguyên bị chiếm dụng bất thường.
- **Tối ưu tài nguyên:** Hoạt động dạng Agent siêu nhẹ (ngốn < 50MB RAM), tối ưu hơn các hệ thống SIEM cồng kềnh.

---

## 3. Hướng dẫn triển khai chi tiết từng bước (Step-by-Step Deployment)

Dưới đây là cụm lệnh toàn bộ quá trình từ cài đặt hệ thống, kiểm tra dịch vụ cho đến cài đặt công cụ `stress` và thực hiện ép tải CPU 100% để test hệ thống:

```bash
# 1. Cài đặt Netdata Agent bằng script chính thức của Netdata Cloud
wget -O /tmp/netdata-kickstart.sh [https://get.netdata.cloud/kickstart.sh](https://get.netdata.cloud/kickstart.sh) && sh /tmp/netdata-kickstart.sh

# 2. Kiểm tra trạng thái hoạt động để đảm bảo Service Netdata đã chạy ngầm ổn định
sudo systemctl status netdata

# 3. Cài đặt công cụ stress và thực hiện ép tải CPU 100% công suất trong vòng 30 giây
sudo apt update && sudo apt install stress -y && stress --cpu $(nproc) --timeout 30s

## 4. Dưới đây là nội dung chi tiết mô tả tường tận từng bước thực hiện trong bài Lab kết hợp cùng chuỗi hình ảnh chứng minh kết quả thực tế thu được từ hệ thống:

Bước 4.1: Quá trình triển khai cài đặt Netdata thành công
Thực hiện chạy script cài đặt tự động từ Netdata Cloud trên Terminal của Kali Linux. Trình cài đặt sẽ tự động nhận diện bản phân phối Linux, tải xuống các gói tài nguyên cần thiết, thiết lập quyền hạn hệ thống thích hợp và cấu hình thành công các plugin giám sát phần cứng mặc định. Kết quả cuối cùng trả về thông báo cài đặt hoàn tất thành công.

Bước 4.2: Kiểm tra dịch vụ Netdata hoạt động ngầm (Active Running)
Sử dụng hệ thống quản lý dịch vụ systemctl để xác thực trạng thái hoạt động của tiến trình Netdata ngầm. Kết quả trên màn hình Terminal hiển thị rõ dòng chữ active (running) màu xanh lá cây, chứng tỏ service đã được nạp vào hệ thống ổn định và đã bắt đầu thu thập dữ liệu phần cứng theo thời gian thực.

Bước 4.3: Truy cập giao diện Console ban đầu của Netdata
Sử dụng trình duyệt Web (như Firefox trên Kali Linux) truy cập vào địa chỉ cổng dịch vụ mặc định http://localhost:19999. Giao diện Web Console hiện ra trực quan với các biểu đồ hiển thị thời gian thực. Ở trạng thái tĩnh ban đầu (idle) khi hệ thống chưa có tải, biểu đồ tổng quan hiển thị mức độ tiêu thụ tài nguyên của các thành phần CPU, RAM, Disk và Network đều ở mức rất thấp và an toàn.

Bước 4.4: Thực thi kịch bản ép tải vắt kiệt hệ thống
Tiến hành cài đặt công cụ sinh áp lực hệ thống stress từ kho lưu trữ APT của Kali Linux. Sau khi cài đặt xong, kích hoạt lệnh bắt toàn bộ các lõi logic của CPU máy chủ ảo hoạt động ở mức tối đa 100% công suất liên tục trong khoảng thời gian 30 giây. Kịch bản này nhằm giả lập chính xác hành vi phá hoại của các đòn tấn công từ chối dịch vụ DoS hoặc hành vi dính mã độc đào tiền ảo chiếm quyền kiểm soát phần cứng.

Bước 4.5: Kết quả Netdata bắt quả tang hành vi bất thường và nổ Báo động (Alert)
Ngay khi lệnh ép tải hoạt động, quay trở lại tab trình duyệt Dashboard giám sát an ninh. Kết quả ghi nhận biểu đồ Total CPU Utilization lập tức dựng một đường thẳng đứng lên sát mức tối đa 100% (Phần biểu đồ màu hồng/tím dâng cao liên tục). Đồng thời, module phân tích an ninh thông minh của Netdata lập tức phát hiện sự thay đổi tài nguyên đột ngột này và kích hoạt ngay một trạng thái Báo động (Alerts) màu cam ở thanh công cụ phía trên để cảnh báo cho quản trị viên SOC.

5. Kết luận
Dự án thực nghiệm đã chứng minh năng lực giám sát trực quan, cập nhật dữ liệu nhạy bén theo từng giây của giải pháp Netdata trước các sự cố quá tải hệ thống hoặc các nguy cơ an ninh mạng. Đây là một phương án giám sát tinh gọn, tối ưu, đạt hiệu quả cao phù hợp để triển khai bảo vệ cho các môi trường máy chủ Linux vừa và nhỏ mà không gây hao tổn tài nguyên phần cứng của hệ thống cốt lõi.
