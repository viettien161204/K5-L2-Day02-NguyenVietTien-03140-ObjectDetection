# Báo cáo cá nhân — Ngày 2 Object Detection


## 1. Thông tin học viên

- Họ tên: Nguyễn Viết Tiến
- MSSV: 03140
- Email: nguyenviettien161204@gmail.com
- Lớp: c204
- Link repo bài làm: https://github.com/viettien161204/K5-L2-Day02-NguyenVietTien-03140-ObjectDetection.git
- Link Colab đã chạy: https://colab.research.google.com/drive/1VtybZa3OBEFkcMN9MHMlmYug81SiAyBq#scrollTo=8f44309015324ec0

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.

## 3. Annotation và self-QC

- Số file nhãn train: 50
- Kết quả checker và lỗi đã xử lý: kết quả f1: 0.8 và mean_IoU: 0.9 ( tất cả đều đạt)
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: drive_047.jpg, drive_046.jpg, drive_042.jpg, drive_037.jpg, drive_034.jpg, drive_032.jpg, drive_025.jpg, drive_023.jpg, drive_022.jpg, drive_017.jpg
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: sau khi dùng YOLO7 để label trước tôi thấy 1 vài ảnh bị thiếu và sai bbox, ví dụ như hình drive_012.jpg do mật độ phương tiện đông nó đánh label thiếu mất vài car ở gần dù rõ nét, và đánh nhầm truck thành bus. cách sửa là thêm bbox vào phương tiện bịthieetsu và chỉnh lại class bbox phù hợp
- Commit khóa nhãn ban đầu: https://github.com/viettien161204/K5-L2-Day02-NguyenVietTien-03140-ObjectDetection/tree/dac1d57a7aee1abff6d56c5dc9e9a820749d7806

## 4. Gold QC và rework
*Không có rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|----------:|---------------:|
| TP |     155      |                |
| FP |     41      |                |
| FN |     37      |                |
| Precision |       0.7916 |            |
| Recall |          0.8086|              |
| F1 |      0.8    |                |
| mean_iou_of_matches |    0.9938       |                |
| passed |    true       |               |

Dẫn tới `outputs/annotation_evaluation.json` trong repo. Nếu rework, ghi ảnh, lỗi đã kiểm, quyết định sửa/giữ và lý do. Không suy nguyên nhân chỉ từ FP/FN. Không có rework thì ghi rõ.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch:
- Các tham số thay đổi so với notebook nếu có và lý do:
- Đường dẫn best checkpoint trong phiên chạy:

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.3156193471665181|
| Recall | 0.30664893617021277|
| mAP50 |0.2739647516429376 |
| mAP50–95 |0.20063156304965743 |

Đặt cả 2 folder `assets/eval_predictions/` và `assets/yolo26_runs/` (giải nén từ `day2_colab_outputs.zip`) cạnh báo cáo và dẫn link tương đối. Không chỉ ghi số; chỉ ra ít nhất một ảnh và vật thể cụ thể.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
- Lớp có recall thấp nhất: Lớp bus có chỉ số Recall = 0.0 (0%) (không phát hiện được một đối tượng nào thuộc lớp này). Tiếp theo là lớp truck với Recall = 0.2 (20%).
- Ảnh minh họa:
Trực quan hóa dự báo ở cell trước cho thấy ảnh drive_052.jpg, drive_053.jpg, drive_056.jpg, drive_058.jpg, hoặc drive_060.jpg đều có chứa xe bus hoặc van nhưng mô hình thường bỏ sót hoặc nhận diện sai lệch hoàn toàn (ví dụ: mô hình nhận diện xe bus thành car hoặc không gán box nào).

2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
- Chỉ số mAP50 đo lường độ chính xác khi chỉ cần độ phủ IoU đạt 50%. Khi mAP50-95 thấp, điều này có nghĩa là khi thắt chặt yêu cầu khớp nối (IoU tăng dần từ 0.5 lên 0.95), độ chính xác giảm mạnh.
- Cần kiểm tra: Độ khít và độ chính xác của các đường viền bounding box.
 + Nhãn huấn luyện (Train labels): Có thể nhãn do con người gán bị lệch, quá rộng, quá hẹp hoặc bị lệch tâm so với vật thể thực tế.
 + Dự đoán của mô hình: Box dự đoán của mô hình bao phủ không chuẩn xác viền ngoài của phương tiện.

3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?
- Lớp liên quan: Các lớp van (mAP50 chỉ đạt 11.5%) và truck (mAP50 chỉ đạt 23.5%).
- Dữ kiện hỗ trợ:
 + Theo Guideline chuẩn, ranh giới định nghĩa giữa xe car cỡ lớn, van (xe bán tải/xe khách nhỏ), và truck (xe tải nhỏ) thường rất dễ bị nhầm lẫn.
 + Kết quả cho thấy lớp car có mAP cực kỳ cao (~73.8%), trong khi van và truck rất thấp. Điều này chứng tỏ người gán nhãn có xu hướng gán nhầm lẫn các xe cỡ trung (van/truck) thành car, hoặc tập huấn luyện bị mất cân bằng trầm trọng khiến mô hình bị thiên lệch (bias) về lớp car và bỏ qua các đặc trưng của van và truck.

4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?
- Vì sao không dùng để train: Tập eval (đóng vai trò như Test set) phải được giữ hoàn toàn độc lập với quá trình học của mô hình. Nếu đưa 10 ảnh này vào tập huấn luyện, mô hình sẽ bị "học vẹt" (overfitting) các bức ảnh này, dẫn đến việc đánh giá độ tổng quát hóa của mô hình không còn khách quan và chính xác.
- Vai trò validation trong notebook:
 + Cung cấp phản hồi khách quan trong quá trình huấn luyện để xác định thời điểm dừng tối ưu (early stopping) và chọn ra checkpoint tốt nhất (best.pt).
 + Giúp người phát triển phát hiện sớm hiện tượng Overfitting (khi loss trên tập train liên tục giảm nhưng loss trên tập eval/val lại tăng lên).

## 6. Khó khăn và cách xử lý

| Bước/thời điểm                          | Lỗi quan sát               | Cách đã thử              | Kết quả/còn thiếu                                                            |
|-----------------------------------------|----------------------------|--------------------------|------------------------------------------------------------------------------|
| khó khăn trong việc xác định guild line | chỉ đọc qua quy tắc schema | gán thử trước khi đọc kĩ | không xác định được các trường hợp khó vậy là phải đọc lại và làm lại từ đầu |

## 7. Tự đánh giá theo rubric
tự đánh giá được 100 vì đã hoàn thành tất cả công việc có trong RUBRIC.md

## 8. Công cụ AI và tài liệu tham khảo
- Dùng ChatGPT để hỗ trợ cài YOLO7 
- Dùng YOLO7 để hỗ trợ gán Bbox ban đầu sau đó kiểm tra và chỉnh sửa 
- Dùng Gemini có trong colab để phân tích hiệu quả để kết luận xem lí do kết quả đầu ra như vậy (kết quả YOLO)

Ghi công cụ đã dùng, dùng ở bước nào và phần đã tự kiểm tra; không dùng thì ghi rõ. Liệt kê nguồn ngoài repo được sử dụng. Không ghi mật khẩu/token hoặc đính kèm gold train.
