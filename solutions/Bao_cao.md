# Lab 16 (GCP)

## Thông tin chung
- Họ tên: Lê Công Thành
- Bài thực hành: Lab 16 - Cloud AI Environment Setup (GCP)

## 1) Lý do gọi API không thành công
Trong quá trình kiểm tra hệ thống, dịch vụ Docker trên VM vẫn hoạt động, nhưng container vLLM không thể khởi động.

Nguyên nhân trực tiếp ghi nhận trong log:
- Lỗi OCI runtime khi khởi tạo container GPU
- Thiếu thư viện NVIDIA trên host: libnvidia-ml.so.1

Hệ quả:
- Container không lên trạng thái running
- Backend không có tiến trình phục vụ cổng 8000
- Load Balancer không có backend healthy để chuyển tiếp request
- Lệnh gọi API không nhận được kết quả inference như mong đợi

Kết luận mục 1:
API lỗi do container không up được vì môi trường GPU runtime trên VM chưa đầy đủ, không phải do cú pháp lệnh curl.

## 2) Lý do chưa chụp được Billing/Cost Dashboard
Tại thời điểm thực hiện lab, dữ liệu chi phí trên Google Cloud Billing chưa cập nhật đủ để phản ánh toàn bộ dịch vụ phát sinh.

Giải thích:
- Google Cloud Billing Reports không cập nhật theo thời gian thực tuyệt đối
- Có độ trễ đồng bộ dữ liệu (thường từ vài chục phút đến vài giờ)
- Vì vậy chưa thể chụp được ảnh thể hiện đầy đủ Compute Engine, Load Balancing, Cloud NAT đúng yêu cầu nộp bài

Kết luận mục 2:
Chưa đủ thời gian để hệ thống Billing cập nhật hoàn chỉnh, nên chưa thể cung cấp screenshot chi phí đầy đủ tại thời điểm hiện tại.

## 3) Trả lời câu hỏi: Report Cold Start Time
Theo yêu cầu bài lab, Cold Start Time được đo từ thời điểm xác nhận triển khai (gõ yes khi chạy terraform apply) đến thời điểm lệnh curl nhận phản hồi inference thành công.

Tại lần chạy hiện tại, em chưa thể báo cáo số đo Cold Start Time hợp lệ vì endpoint chưa trả kết quả thành công do container vLLM không khởi động được.

Vì vậy, kết quả mục tiêu dưới 15 phút cho GPU T4 hiện chưa thể kết luận ở lần deploy này.

Sau khi đổi sang image còn được hỗ trợ và triển khai lại thành công, em sẽ ghi lại mốc thời gian bắt đầu và thời điểm curl thành công để bổ sung số liệu Cold Start Time chính xác.

## 4) Lý do đổi image VM
Image ban đầu dùng trong cấu hình main.tf là "projects/deeplearning-platform-release/global/images/family/common-cu121-debian-11". Tuy nhiên, image family này không còn khả dụng ổn định để sử dụng mới (đã deprecated), nên cần đổi sang image/family khác còn được hỗ trợ.

## Thay vào đó
Em đã nộp các minh chứng khác:
- [Screenshot](./tf_apply.png) `terraform apply` thành công
- `Bao_cao.md`
