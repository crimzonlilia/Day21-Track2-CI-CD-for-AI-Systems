# BÁO CÁO KẾT QUẢ LAB: XÂY DỰNG PIPELINE MLOPS CI/CD

Họ và tên: Nguyễn Thị Diệu Linh   
Mã HV: 2A202600209

## 1. Bộ siêu tham số đã chọn (Giai đoạn thực nghiệm)

- **Thuật toán:** RandomForestClassifier
- **Bộ siêu tham số tối ưu:**
  - `n_estimators: 200`
  - `max_depth: 20`
  - `min_samples_split: 2`
- **Lý do lựa chọn:** Qua nhiều lần thí nghiệm với MLflow ở Bước 1, bộ tham số này cho kết quả Accuracy cao nhất (0.684) trên tập dữ liệu ban đầu. Khi kết hợp với dữ liệu bổ sung ở Bước 3, mô hình đạt hiệu năng ấn tượng với Accuracy = **0.7540**.

## 2. Khó khăn gặp phải và cách giải quyết

| Khó khăn | Cách giải quyết |
| :--- | :--- |
| **Lỗi xác thực GitHub Actions** khi chỉnh sửa file workflow từ local (`workflow scope`). | Khởi tạo **Personal Access Token (Classic)** với đầy đủ quyền `repo` và `workflow` để thực hiện lệnh `git push --force`. |
| **Server trên VM không khởi động kịp** dẫn đến lỗi Health Check trong Pipeline. | Tăng thời gian chờ (`sleep`) trong file `.github/workflows/mlops.yml` từ 5 giây lên **20 giây** để đảm bảo mô hình từ GCS được tải xuống hoàn tất. |
| **Lỗi kết nối API từ máy cá nhân** do tường lửa của Google Cloud chặn cổng 8000. | Sử dụng lệnh `gcloud compute firewall-rules` để mở cổng TCP 8000 và gán tag tương ứng cho VM. |
| **Xung đột phiên bản MLflow** giữa môi trường local (Windows) và CI (Linux). | Cấu hình cố định `mlflow.set_tracking_uri("sqlite:///mlflow.db")` trong mã nguồn để đảm bảo tính nhất quán của cơ sở dữ liệu tracking. |

## 3. Kết luận
Hệ thống đã vận hành đúng theo chu trình MLOps:
1. **Dữ liệu mới** được cập nhật qua DVC.
2. **Pipeline tự động** kích hoạt, huấn luyện và kiểm tra chất lượng mô hình.
3. **Mô hình đạt chuẩn** (Accuracy > 0.70) được tự động triển khai lên máy chủ thực tế (Cloud VM).
4. **API phục vụ dự đoán** hoạt động ổn định và có thể truy cập từ xa.
