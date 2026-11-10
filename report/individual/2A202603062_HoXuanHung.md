# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Hồ Xuân Hùng
- MSSV: 2A202603062
- Email: 26ai.hunghx@vinuni.edu.vn
- Lớp: K5 L2A
- Link repo bài làm: https://github.com/Hiung0703/K5-L2A-DAY02-HoXuanHung-2A202603062-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/1gZXqYT6B9VDp41JR2uZxOFZZ9p0Q2P1O?usp=sharing

## 2. Schema và mini guideline

Chi tiết trong [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Schema giữ nguyên: car=0, truck=1, bus=2, van=3.

Cách áp dụng schema:

- `car`: xe con 4/7 chỗ, sedan, SUV, taxi, bán tải nhỏ.
- `truck`: xe tải chở hàng, xe đầu kéo, thùng dài hoặc cabin cao.
- `bus`: xe buýt chở khách, minibus, nhiều cửa sổ dọc thân.
- `van`: xe van/minivan, thân hộp, cao hơn car, nhỏ hơn bus, thường có cửa trượt.
- Box chỉ bao phần thân xe nhìn thấy; xe bị che vẫn gán nếu xác định được lớp; xe bị cắt khung thì box chạm mép ảnh; vật thể quá nhỏ hoặc nhòe nặng không nhận ra loại xe thì không gán.

Ba ca mơ hồ đã quyết định:

| Ảnh | Tình huống | Quyết định |
|---|---|---|
| `drive_003` | Nhiều xe sát nhau, một số bị che lẫn | Vẽ box riêng cho từng xe nhìn thấy được |
| `drive_013` | Xe di chuyển nên nhòe nhẹ | Vẫn gán bình thường |
| `drive_007` | Xe sau hàng rào / bị bóng cây che một phần | Gán box quanh phần thân xe còn nhìn thấy |

Guideline được bổ sung nội dung ở commit `ada8607` ("Update definitions and guidelines for vehicle classes"), sau commit khóa nhãn.

Phần cần giảng viên làm rõ: ranh giới giữa `bus` (minibus) và `van` (van chở người) trong định nghĩa lớp.

## 3. Annotation và self-QC

- Số file nhãn train: 50 (commit `f87e762`: 50 files changed, +1.136 dòng, tương ứng 1136 box).
- Kết quả checker và lỗi đã xử lý: `python tools/check_yolo_labels.py` đạt. 
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: đã rà lại bằng mắt toàn bộ 50 ảnh train (drive_001 đến drive_050), trong đó đã xem nhãn chồng lên ảnh gốc ở drive_001, drive_018, drive_028, drive_036, drive_044, drive_049.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: Box bị lệch so với vật thể và vật thể bị định dạng nhầm.
- Commit khóa nhãn ban đầu: `f87e762` ("Add YOLO labels for train set - CP4 lock"), parent `786fe1a`, ngày 10/11/2026.

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 500 | 450 |
| FP | 636 | 442 |
| FN | 153 | 203 |
| Precision | 0.4401 | 0.5045 |
| Recall | 0.7657 | 0.6891 |
| F1 | 0.559 | 0.5825 |
| mean_iou_of_matches | 0.8504 | 0.8462 |
| passed | false | false |

Kết quả: `outputs/annotation_evaluation.json` trong repo (IoU match 0.50, tổng 1136 box của tôi so với 653 box gold). Mean IoU đạt ngưỡng (≥ 0.70); F1 chưa đạt ngưỡng (≥ 0.80).

## 5. Kết quả YOLO

- Runtime: Google Colab, GPU Tesla T4 (CUDA:0, 14913 MiB); Python 3.13.16, Ultralytics 8.4.208, torch 2.14.1+cu130.
- Tham số thay đổi so với notebook: [ĐIỀN, hoặc ghi "không đổi"]. Log cho thấy train 50 epoch trong 0.024 giờ.
- Best checkpoint: `[link](https://drive.google.com/file/d/1WsG1i3PLQZpiOhxakU0bnYvmq317z_UG/view?usp=drive_link)`.
- Split giữ nguyên: 50 ảnh train, 10 ảnh eval (113 instance), không dùng eval để train.
- Số liệu lấy từ log phiên Colab ngày 10/11. Lần train này chạy khi F1 ở mục 4 chưa đạt ngưỡng.

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.384 |
| Recall | 0.448 |
| mAP50 | 0.388 |
| mAP50–95 | 0.292 |

(Theo `model_eval_metrics.json`: 0.3840 / 0.4480 / 0.3881 / 0.2917.)

Theo từng lớp:

| Lớp | Ảnh | Instance | P | R | mAP50 | mAP50–95 |
|---|---:|---:|---:|---:|---:|---:|
| car | 10 | 94 | 0.411 | 0.926 | 0.788 | 0.526 |
| truck | 5 | 5 | 0.0585 | 0.200 | 0.0323 | 0.0277 |
| bus | 2 | 2 | 0.843 | 0.500 | 0.528 | 0.456 |
| van | 6 | 12 | 0.223 | 0.167 | 0.204 | 0.158 |

Ảnh và log: [assets/eval_predictions/visuals/](assets/eval_predictions/visuals/), [assets/yolo26_runs/](assets/yolo26_runs/). 

Ảnh/vật thể cụ thể: `drive_059` (ảnh eval, ảnh prediction trong `visuals/`). Model dự đoán chủ yếu `car` với độ tin cậy 0.93, 0.90, 0.87, 0.83 và 0.72. Vật thể điển hình: chiếc xe tối ở giữa ảnh, gần camera, bị nhòe do chuyển động, được dự đoán là `car` với độ tin cậy 0.72, thấp hơn các xe nhỏ rõ nét hơn ở xa; box ôm sát thân xe. Ở góc trên bên trái, giữa cụm xe nhỏ ở xa, có một box dự đoán `truck` với độ tin cậy chỉ 0.52, thấp nhất ảnh. 
Trả lời bốn câu hỏi cuối notebook:

1. **Lớp recall thấp nhất:** `van` (R ≈ 0.167, 12 instance), kế đến `truck` (R = 0.2, 5 instance). Ảnh minh họa:
2. <img width="640" height="640" alt="drive_059" src="https://github.com/user-attachments/assets/e434683e-028d-4485-94f8-a7459f231732" />

3. **mAP50 cao nhưng mAP50–95 thấp:** cần kiểm tra độ sát của box (cạnh box lệch, lỏng hoặc cắt vào vật thể) ở các ngưỡng IoU cao. Ở lần chạy này `car` có mAP50 0.788 nhưng mAP50–95 0.526. 
4. **Lỗi class liên quan guideline:** nhóm `truck`/`van`/`bus`, vì ranh giới minibus (`bus`) và van chở người (`van`) trong guideline dễ chồng lấn. Dữ kiện: các lớp này chỉ có 5, 12 và 2 instance trong eval trong khi `car` có 94/113; recall `van` 0.167 và `truck` 0.2. Với số instance ít, chỉ số rất dao động nên chưa đủ để kết luận lỗi do nhãn.
5. **Không train bằng 10 ảnh eval** để tập đánh giá tách khỏi tập huấn luyện, nếu không số đo bị thổi phồng. Trong notebook, 10 ảnh eval cũng làm validation để chọn `best.pt`, nên không phải test set độc lập.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Chấm nhãn với gold | F1 0.559 < 0.80, mean IoU 0.8504 đạt | Chạy evaluator | Chạy lại |
| Chạy evaluator trên PowerShell | Lỗi cú pháp do dùng dấu `\` xuống dòng | Viết lệnh trên một dòng | Chạy được |
| Lưu kết quả Colab | Chưa lưu `day2_colab_outputs.zip` | Lấy số từ log phiên chạy | Vẫn ra kết quả chưa đạt |

## 7. Tự đánh giá theo rubric

| Tiêu chí | Tình trạng |
|---|---|
| Hoàn thành và định dạng YOLO | Đủ 50 file, checker đạt, commit `f87e762` |
| Đầy đủ vật thể | Kiểm chứng đầy đủ; số box của tôi nhiều hơn gold (1136 so với 653) |
| Độ bám sát box | Mean IoU 0.8504 (đạt, mức Xuất sắc) |
| Phân lớp nhất quán | Kiểm chứng đầy đủ; chưa phân tích chi tiết theo lớp so với gold |
| Mini guideline | Có định nghĩa 4 lớp, quy tắc box và 3 ca mơ hồ |
| Gold-set annotation QC | Đã chạy evaluator: F1 0.559 (chưa đạt 0.80), mean IoU 0.8504 (đạt); |
| Pipeline YOLO và phân tích | Colab đã chạy, giữ đúng split, có 4 chỉ số và số liệu từng lớp |

Chưa hoàn thành: F1 chưa đạt 0.80.

## 8. Công cụ AI và tài liệu tham khảo

- YOLO26 (Ultralytics): train và đánh giá mô hình trên Colab (mục 5). Không dùng để sửa nhãn nộp.
- Phần tự thực hiện/kiểm tra: gán nhãn 50 ảnh, chạy checker và evaluator, chạy code trên máy và Colab, xem ảnh kết quả, commit/push.
