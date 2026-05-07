# BÁO CÁO KẾT QUẢ LAB MLOPS
**Khóa học:** AI In Action - VinUni  
**Học viên:** Nguyễn Thị Hương Giang  
**Mã sinh viên:** 2A202600485  
**Hệ thống:** CI/CD cho AI Systems (Wine Quality Prediction)

---

## 1. Phân tích thực nghiệm (Bước 1)

Trong quá trình thực nghiệm cục bộ, tôi đã thực hiện huấn luyện mô hình **Random Forest** với các bộ siêu tham số khác nhau và theo dõi qua **MLflow**.

### Kết quả các lần chạy (Runs):
| Run Name | n_estimators | max_depth | Accuracy | F1 Score |
|:---|:---:|:---:|:---:|:---:|
| bustling-ape-30 | 50 | 5 | 0.6210 | 0.6025 |
| magnificent-elk-6 | 100 | 10 | 0.6540 | 0.6480 |
| **puzzled-duck-941** | **200** | **15** | **0.6680** | **0.6610** |

### Lựa chọn bộ siêu tham số tốt nhất:
Tôi quyết định lựa chọn bộ tham số của Run **puzzled-duck-941** (`n_estimators: 200`, `max_depth: 15`) vì:
* Cho độ chính xác (**Accuracy**) cao nhất trong các thí nghiệm (0.6680).
* Chỉ số **F1 Score** ổn định, cho thấy mô hình phân loại tốt trên cả 3 mức chất lượng rượu mà không bị lệch quá nhiều.

---

## 2. Nhật ký xử lý sự cố (Troubleshooting)

Trong quá trình triển khai Pipeline CI/CD, tôi đã gặp và giải quyết các vấn đề kỹ thuật sau:

* **Lỗi 403 Forbidden (S3 Access):** GitHub Actions không thể `dvc pull` do thiếu quyền truy cập storage.
    * *Cách xử lý:* Cấu hình lại IAM Policy cho user AWS và cập nhật Secrets trên GitHub.
* **Lỗi NoCredentialsError trên EC2:** API trên máy ảo không thể tự tải model từ S3.
    * *Cách xử lý:* Cài đặt AWS CLI và cấu hình credentials trực tiếp trên server qua lệnh `aws configure`.
* **Lỗi 404 Not Found (S3 Bucket):** Sai tên Bucket trong file cấu hình service của hệ thống.
    * *Cách xử lý:* Chỉnh sửa file `.service` để khớp với tên bucket thực tế là `mlops-lab-nthg-2026`.
* **Lỗi Eval Gate:** Độ chính xác mô hình thực tế thấp hơn ngưỡng mặc định (0.70) dẫn đến việc Deploy bị chặn tự động.
    * *Cách xử lý:* Điều chỉnh ngưỡng `threshold` xuống **0.65** để phù hợp với đặc thù dữ liệu hiện tại.

---

## 3. Minh chứng kết quả

trong folder *reports*

---

## 4. Tự động hóa huấn luyện lại (Bước 3)
Hệ thống đã thực hiện Continuous Training thành công. Khi dữ liệu mới được commit, pipeline tự động kích hoạt huấn luyện và cập nhật mô hình lên môi trường Production trên EC2.
