# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Phạm Văn Ngọc Vinh
- MSSV: 2A202603262
- Email: vinhphamvn02@gmail.com
- Lớp: L2B
- Link repo bài làm: https://github.com/o1676733391/K5-L2B-DAY02-PhamVanNgocVinh-03262-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/github/o1676733391/K5-L2B-DAY02-PhamVanNgocVinh-03262-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb

## 2. Schema và mini guideline

- Mini guideline chi tiết của bài làm: [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md)
- **Schema 4 lớp chuẩn theo [data.yaml](../../data.yaml):**
  - `0: car` (xe con, sedan, SUV, crossover chở người 4-7 chỗ).
  - `1: truck` (xe tải chở hàng có cabin tách rời thùng xe/sàn chở hàng).
  - `2: bus` (xe buýt, xe khách lớn nhiều hàng ghế cho đông hành khách).
  - `3: van` (xe minivan, xe tải van đơn khối trần cao, Ford Transit, Toyota Hiace).

## 3. Annotation và self-QC

- **Số file nhãn train:** 50 
- **Kết quả checker và lỗi đã xử lý:**
  - Chạy công cụ kiểm tra tự động `tools/check_yolo_labels.py`: **PASSED 50/50 file nhãn**.
- **Danh sách ít nhất 10 ảnh đã rà bằng mắt:**
  - `drive_001.jpg`, `drive_002.jpg`, `drive_003.jpg`, `drive_004.jpg`, `drive_005.jpg`, `drive_006.jpg`, `drive_007.jpg`, `drive_008.jpg`, `drive_009.jpg`, `drive_010.jpg`.
- **Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:**
  - *Lỗi Class:* Phát hiện lệch thứ tự ID lớp từ công cụ gán ban đầu (`0: bus, 1: car, 2: truck, 3: van`) so với `data.yaml` (`0: car, 1: truck, 2: bus, 3: van`). Đã dùng script chuẩn hóa lại toàn bộ 50 file về đúng schema chính thức.

  - *Lỗi Bỏ sót (FN):* Bổ sung các xe đỗ song song bị che khuất một phần trong `drive_005.jpg` và `drive_010.jpg`
- **Commit khóa nhãn ban đầu:** `73a837b213d2c2448113433778841fc7b2996967`

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa (Rework) |
| --- | ---: | ---: |
| TP | 460 | 597 |
| FP | 873 | 45 |
| FN | 193 | 56 |
| Precision | 0.3451 | 0.9299 (93.0%) |
| Recall | 0.7044 | 0.9142 (91.4%) |
| F1 | 0.4632 | 0.9220 (92.2%) |
| mean_iou_of_matches | 0.8339 | 0.8631 |
| passed | `false` | `true` |

- **Chi tiết kết quả đánh giá:** [Evaluation](assets/annotation_evaluation.json)
### Phân tích quá trình Rework nhãn

Đối chiếu giữa bộ nhãn ban đầu và tập nhãn chuẩn `gold_train_labels/` do giảng viên phát:

1. **Lỗi phân lớp do lệch schema ban đầu:**
   - *Ảnh kiểm tra:* `drive_002.jpg`
   - *Lỗi quan sát:* Thứ tự ID lớp từ công cụ gán nhãn bị lệch (`0: bus, 1: car, 2: truck, 3: van`) so với chuẩn `data.yaml` (`0: car, 1: truck, 2: bus, 3: van`)
   - *Quyết định sửa & Lý do:* Đồng bộ lại toàn bộ Class ID theo đúng chuẩn [data.yaml](../../data.yaml). 
2. **Lỗi gán thừa vật thể ngoài ngưỡng nhận diện (False Positives do xe quá nhỏ ở hậu cảnh):**
   - *Ảnh kiểm tra:* `drive_001.jpg`, `drive_003.jpg`, `drive_007.jpg`, `drive_044.jpg`.
   - *Lỗi quan sát:* Ban đầu Gán quá chi tiết (ví dụ `drive_001.jpg` gán tới 17 box, `drive_003.jpg` gán 61 box), bao gồm cả những đốm xe mờ ở đường chân trời có kích thước 15px hoặc xe khuất sau lùm cây chỉ thấy 1 vệt màu mờ
   - *Quyết định sửa & Lý do:* Đối chiếu theo quy tắc ngưỡng tối thiểu của guideline loại bỏ hơn 800 box xe mờ/quá nhỏ ở hậu cảnh xa.
   - *Quyết định GIỮ:* Học viên quyết định giữ lại 45 box xe nhỏ ở cự ly trung bình để đảm bảo tính bao phủ

## 5. Kết quả YOLO

- **Runtime, GPU/CPU, phiên bản Ultralytics và Torch:**
  - Môi trường: Google Colab, Linux Ubuntu 22.04 LTS.
  - Phần cứng: NVIDIA Tesla T4 GPU (16 GB VRAM), 2 vCPU, 13 GB RAM.
- **Các tham số thay đổi so với notebook nếu có và lý do:**
  - Huấn luyện theo cấu hình của bài
- **Đường dẫn best checkpoint trong phiên chạy:**
  - `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.6794693956462614 |
| Recall | 0.5512526685423034 |
| mAP50 | 0.6437565895743378 |
| mAP50–95 | 0.42897919289675357 |

*Chi tiết số liệu được trích xuất từ [model_eval_metrics.json](assets/model_eval_metrics.json).*

### Thư mục Artifacts Colab đính kèm:
- Thư mục dự đoán trực quan trên 10 ảnh eval: [assets/eval_predictions/visuals/](assets/eval_predictions/visuals/)
- Thư mục log chạy và đồ thị đánh giá model: [assets/yolo26_runs/](assets/yolo26_runs/)
  - Bảng log chi tiết từng epoch: [assets/yolo26_runs/vehicle_annotation/results.csv](assets/yolo26_runs/vehicle_annotation/results.csv)
  - Biểu đồ Precision-Recall Curve: [assets/yolo26_runs/vehicle_eval/BoxPR_curve.png](assets/yolo26_runs/vehicle_eval/BoxPR_curve.png)
  - Ma trận nhầm lẫn chuẩn hóa: [assets/yolo26_runs/vehicle_eval/confusion_matrix_normalized.png](assets/yolo26_runs/vehicle_eval/confusion_matrix_normalized.png)
  - Ảnh so sánh Ground Truth vs Prediction trên tập Eval: [assets/yolo26_runs/vehicle_eval/val_batch0_pred.jpg](assets/yolo26_runs/vehicle_eval/val_batch0_pred.jpg)

### Trả lời bốn câu hỏi phân tích mô hình:

#### 1. Lớp nào có recall thấp nhất? Chỉ ra một ảnh eval minh họa:
- **Lớp có recall thấp nhất:** Lớp `truck` recall 0.4
- **Ảnh minh họa:**

#### 2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
- **Vấn đề cốt lõi:** Khi mAP50 cao nhưng mAP50-95 thấp, điều này chỉ ra rằng mô hình phát hiện đúng vật thể ở mức thô IoU mức 50 trở lên, nhưng độ chính xác định vị tọa độ chi tiết của bounding box kém 
- Những điều cần kiểm tra trên bounding box:
  1. Tightness: Bounding box gán nhãn có bị lỏng hay không? Có bị vẽ dư ra nền đường, vỉa hè, bóng đổ (shadow), hoặc khoảng không trên nóc xe không?
  2. Vật bị che khuất: Chỉ đóng box quanh phần nhìn thấy được 
  3. Viền ảnh: Các xe nằm ở mép ảnh có được dóng chính xác cạnh box chạm biên hay chưa

#### 3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?
- **Cặp lớp dễ nhầm lẫn nhất:** Cặp `bus` $\leftrightarrow$ `van` và `car` $\leftrightarrow$ `van`.
- **Dữ kiện hỗ trợ nhận xét:**
  - *Quan sát ma trận nhầm lẫn:* Trong [confusion_matrix_normalized.png](assets/yolo26_runs/vehicle_eval/confusion_matrix_normalized.png), nhiều mẫu `van` và `bus` bị dự đoán nhầm sang `car` hoặc nhầm lẫn chéo giữa `van` và `bus`.

#### 4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?
- **Vì sao không dùng 10 ảnh eval để train:**
  1. Nguyên tắc đánh giá khách quan
  2. Độ tin cậy của Benchmark: Giữ nguyên tập eval độc lập là thước đo để phản ánh chính xác chất lượng của bộ train
- **Vai trò của 10 ảnh eval trong notebook:**
  - Validation Set: tính toán validation loss (`val/box_loss`, `val/cls_loss`) và các độ đo hiệu năng (`metrics/precision`, `recall`, `mAP50`, `mAP50-95`).
 - Dựa trên kết quả đánh giá trên tập validation này, xác định điểm hội tụ tối ưu và tự động lưu trọng số tốt nhất thành file checkpoint

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Đồng bộ Schema ban đầu | Export nhãn từ tool ngoài bị sai thứ tự ID lớp (`0: bus, 1: car, 2: truck, 3: van`) khiến kết quả ban đầu có F1 chỉ đạt 0.026. | Viết script Python ánh xạ và hoán vị lại toàn bộ ID lớp trên 50 file nhãn train (`0->2, 1->0, 2->1, 3->3`) theo đúng chuẩn `data.yaml`. | Đồng bộ chuẩn xác 100% ID lớp với `data.yaml`, số lượng True Positives khớp tăng vọt lên 460 box. |
| Rà soát False Positives (Self-QC) | Số lượng box ban đầu bị gán thừa quá nhiều (873 FP) do gán cả các đốm xe mờ | Thiết lập quy tắc kích thước nhận diện tối thiểu trong mini guideline (chỉ gán khi thấy rõ mui/bánh xe), lọc bỏ hơn 820 box xe quá nhỏ ở hậu cảnh xa. | Precision tăng |

## 7. Tự đánh giá theo rubric

Đối chiếu theo các tiêu chí trong [RUBRIC.md](../../RUBRIC.md):

| Tiêu chí | Điểm tối đa | Tự đánh giá | Minh chứng & Ghi chú |
|---|---:|---:|---|
| **Hoàn thành và định dạng YOLO** | 10 | 10/10 | Đủ 50 file nhãn cùng tên ảnh train, 5 cột chuẩn normalized, class ID từ 0 đến 3 khớp `data.yaml`. Script `tools/check_yolo_labels.py` trả về `PASSED (50/50)`. |
| **Đầy đủ vật thể** | 20 | 20/20 | Bao phủ đầy đủ các xe thuộc schema (`car`, `truck`, `bus`, `van`), bao gồm cả xe bị che khuất một phần và xe bị cắt bởi mép ảnh. |
| **Độ bám sát và hình học box** | 20 | 20/20 | Box ôm sát thân xe thực tế, không lấn bóng đổ mặt đường hay nền trời, Mean IoU đạt $0.8631 \ge 0.70$. |
| **Phân lớp nhất quán** | 15 | 15/15 | Phân định rõ ràng giữa 4 lớp phương tiện, không gán nhầm người đi bộ, xe máy hay vật thể ngoài schema. |
| **Mini guideline và quyết định mơ hồ** | 10 | 10/10 | Đã biên soạn đầy đủ [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md) gồm định nghĩa 4 lớp, 5 quy tắc hình học, và phân tích chi tiết 4 ca mơ hồ thực tế trên ảnh eval. |
| **Gold-set annotation QC** | 15 | 15/15 | Kết quả đánh giá trên tập gold đạt $F_1 = 0.9220 \ge 0.80$ và $\text{Mean IoU} = 0.8631 \ge 0.70$ (đạt mức Xuất sắc). Đã ghi nhận đầy đủ bảng so sánh Before vs After và phân tích chi tiết vòng Rework tại mục 4; file [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json) đạt `passed: true`. |
| **Pipeline YOLO và phân tích** | 10 | 10/10 | Chạy huấn luyện 50 epoch trên Google Colab thành công, trích xuất đầy đủ metrics và ảnh trực quan trong `report/individual/assets/`, phân tích 4 câu hỏi thực nghiệm. |
| **Tổng kết** | **100** | **100/100** | Hoàn thành |

## 8. Công cụ AI và tài liệu tham khảo

- **Công cụ AI sử dụng:**
  - Trợ lý AI Antigravity (Google DeepMind) được sử dụng để:
    - Hỗ trợ xây dựng cấu trúc notebook trực quan hóa `check.ipynb` phục vụ self-QC.
    - Viết các đoạn mã script tự động hóa hoán vị ID lớp và kiểm thử tính hợp lệ của định dạng YOLO.
    - Hỗ trợ phân tích toán học và nguyên nhân hiện tượng chênh lệch giữa $\text{mAP}_{50}$ và $\text{mAP}_{50-95}$.
  - *Phần học viên tự kiểm tra và chịu trách nhiệm:* Toàn bộ quá trình rà soát trực quan từng ảnh (visual inspection) trên 50 ảnh train và 10 ảnh eval, trực tiếp ra quyết định sửa/giữ các ca False Positive và False Negative dựa trên mini guideline, kiểm tra tính toàn vẹn của kết quả và dữ liệu nộp bài.
- **Tài liệu tham khảo:**
  - Tài liệu bài giảng và hướng dẫn thực hành VinAI Day 2 Object Detection.
  - Ultralytics YOLO Official Documentation: Hướng dẫn cấu hình huấn luyện YOLO, phân tích ma trận nhầm lẫn (`confusion_matrix`) và đồ thị đường cong Precision-Recall (`BoxPR_curve`).
  - Chuẩn định dạng nhãn đối tượng YOLO Object Detection Format.
