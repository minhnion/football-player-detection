Dự án này sử dụng YOLOv8 cùng với kỹ thuật tăng cường dữ liệu (data augmentation) để phát hiện cầu thủ, trọng tài thủ môn, quả bóng trong các hình ảnh của trận đấu bóng đá. 
Mục tiêu là tối ưu hóa phát hiện các đối tượng trên với độ chính xác cao.
Dự án được xây dựng trên nền tảng PyTorch và sử dụng các thư viện như Ultralytics (YOLOv8), Albumentations, OpenCV, và matplotlib.
Nội dung
  •	Yêu Cầu
  •	Cài Đặt
  •	Cấu Trúc Dự Án
  •	Tiền Xử Lý Dữ Liệu
  •	Tăng Cường Dữ Liệu (Data Augmentation)
  •	Huấn Luyện Mô Hình
  •	Đánh Giá Mô Hình
  •	Dự Đoán và Inference
  •	Liên Hệ

Yêu Cầu
  •	Python 3.7+
  •	PyTorch (phiên bản tương thích với CUDA nếu có GPU)
  •	Thư viện Ultralytics (YOLOv8)
  •	Albumentations
  •	OpenCV
  •	matplotlib
  •	PyYAML
  •	Pillow
  •	IPython

Dataset: https://www.kaggle.com/datasets/borhanitrash/football-players-detection-dataset

Cài Đặt
Cài đặt các gói cần thiết bằng lệnh:
  !pip install ultralytics albumentations -q

I. Tiền Xử Lý Dữ Liệu
  •	Đường dẫn dữ liệu gốc:
    Dữ liệu được lấy từ /kaggle/input/football-players-detection-dataset với file cấu hình data.yaml chứa thông tin về các lớp: "ball", "goalkeeper", "player", "referee".
  •	Tạo thư mục làm việc:
    Tạo thư mục lưu trữ kết quả và dữ liệu đã xử lý tại /kaggle/working/football_detection_improved.
  •	Cập nhật cấu hình dữ liệu:
    File cấu hình dữ liệu gốc được đọc và cập nhật thêm trọng số cho các lớp hiếm (ball, goalkeeper, referee) nhằm nhấn mạnh việc huấn luyện.
II. Tăng Cường Dữ Liệu (Data Augmentation)
•	Mục đích:
  Tăng cường các ảnh chứa đối tượng hiếm (ball, goalkeeper, referee) bằng cách áp dụng các biến đổi như:
    o	Lật ngang (Horizontal Flip)
    o	Thay đổi độ sáng và độ tương phản
    o	Thay đổi tỷ lệ (Scale)
    o	Làm mờ (Blur, Motion Blur)
  •	Quy trình:
    1.	Sao chép ảnh và nhãn từ tập train gốc vào thư mục mới.
    2.	Đối với những ảnh có chứa các đối tượng hiếm, tạo ra 3 phiên bản tăng cường bằng cách áp dụng các biến đổi được định nghĩa trong Albumentations.
    3.	Cập nhật file data.yaml trong thư mục augmented để trỏ đến dữ liệu train và validation mới.
III. Huấn Luyện Mô Hình
  •	Mô hình sử dụng:
    YOLOv8 (phiên bản yolov8m.pt) được load và chuyển sang thiết bị phù hợp (GPU nếu có, ngược lại CPU).
  •	Tham số huấn luyện:
    o	Kích thước ảnh: IMG_SIZE = 1280
    o	Batch size: BATCH_SIZE = 8
    o	Số epoch: EPOCHS = 50
    o	Tốc độ học: LEARNING_RATE = 0.001
    o	Trọng số decay: WEIGHT_DECAY = 0.0005
    o	Mosaic, Mixup, Copy Paste và các augmentation khác được bật với xác suất nhất định.
    o	Early Stopping với patience = 15 và lưu checkpoint mỗi 5 epochs.
  •	Lệnh train:
    Huấn luyện được thực hiện thông qua phương thức model.train(...) với các tham số đã định nghĩa.
IV. Đánh Giá Mô Hình
  •	Validation:
    Sau quá trình huấn luyện, mô hình được đánh giá trên tập validation. Các chỉ số như mAP50-95, mAP50 cùng với Precision, Recall cho từng lớp được in ra.
  •	Test:
    Mô hình sẽ thực hiện đánh giá tương tự như validation trên tập test.
V. Dự Đoán và Inference
  •	Test Time Augmentation (TTA) và Ensemble:
  Để cải thiện kết quả dự đoán, dự án áp dụng kỹ thuật TTA bằng cách tạo nhiều biến thể của cùng một ảnh và kết hợp dự đoán. Ngoài ra, nếu có nhiều checkpoint (best.pt và last.pt), các mô hình được ensemble lại để kết hợp kết quả.
  •	Visualization:
  Các ảnh dự đoán được load từ thư mục test và hiển thị với bounding box, nhãn và xác suất trên mỗi đối tượng. Màu sắc của bounding box được định nghĩa dựa trên lớp (ball: đỏ, goalkeeper: xanh lá, player: xanh dương, referee: vàng).

    ![image](https://github.com/user-attachments/assets/4464c368-b354-4249-9b3b-f1ca2babf618)

  
