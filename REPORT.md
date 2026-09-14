# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Nguyễn Văn Tuyển <br>
**MSSV:** 2A202602205 <br>
**Hình thức:** Cá nhân <br>
**Mã cặp:** SOLO 

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: `F7D99888F21440FB0374D84962B93213BD8C14E665D093CC8D37F4C61B71ED33`

- Bốn mã ảnh: `drive_008.jpg` ,`drive_022.jpg` , `drive_033.jpg` , `drive_038.jpg` 

- Số vật thể thực tế: 119

- Mã SHA-256 của gói YOLO của bạn: `54991d72754e89fefd5fa1dcfa32570d98cdfef043339387a1e61165399d325b`

- Mã SHA-256 của gói CVAT gốc của bạn: `94210f1353f7f4b373bcfdfd846b80b3f8aeab53e163aefc51406e9b2dca0b7c`

- Nguồn đối chiếu: người hướng dẫn thực hành cấp

- Mã SHA-256 của gói đối chiếu: `c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b`
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: SOLO 14/09/2026

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu: Quá trình gán nhãn được thực hiện hoàn toàn tự chủ trên tài khoản CVAT cá nhân dựa theo quy chuẩn đã học. Tôi chỉ tiến hành nhận và tải bộ dữ liệu tham chiếu sau khi đã hoàn thành.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_008.jpg / xe ô tô con | car | Phần thân nhỏ, thiết kế sedan/hatchback, đuôi xe thấp | Phân loại phương tiện giao thông: xe ô tô con (`car`) |
| drive_022.jpg / xe tải | truck | Khung xe lớn, có thùng hàng phía sau, gầm cao | Phân loại xe chở hàng hóa (`truck`) |
| drive_033.jpg / xe buýt | bus | Thân dài hình hộp, nhiều cửa sổ kính dọc thân xe | Phân loại phương tiện vận chuyển hành khách công cộng (`bus`) |
| drive_038.jpg / xe van | van | Thân xe dạng khối kín liền khung, kích thước trung bình | Phân loại xe khoang kín/chở hàng nhỏ (`van`) |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau: 
- Lớp (Class): Định danh bản chất vật thể, ví dụ: car (xe ô tô con).
- Thuộc tính (Attribute): Mô tả trạng thái hoặc đặc điểm bổ sung của vật thể đó mà không thay đổi bản chất lớp, ví dụ: color: red (màu đỏ) hoặc occluded: true (bị che khuất).
- Ví dụ: Một chiếc xe ô tô màu đỏ bị che khuất một phần vẫn thuộc lớp car, nhưng có các thuộc tính là {color: red, occluded: true}.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Bounding box trùm lên cả phần bóng của xe | Hình học | Visual inspection trên CVAT / Kiểm tra biên vật thể | Thu gọn box sát mép vỏ xe, bỏ phần bóng đổ dưới đường |
| Bounding box cắt mất phần cản trước của xe tải | Hình học | Visual inspection / Kiểm tra biên vật thể | Điều chỉnh box mở rộng về phía đầu xe, bảo đảm chứa toàn bộ kết cấu xe |
| Xe tải nhỏ bị gán nhầm thành car | Lớp | Kiểm tra lại chiều cao và thùng sau | Đổi nhãn từ car sang truck theo đúng quy định kích thước |
| Bỏ sót xe bị che khuất 50% ở góc ảnh | Phạm vi | Kiểm tra theo lưới ảnh (grid review) | Thêm box mới và bổ sung thuộc tính occluded |


- Số hộp `needs_review` trước và sau khi kiểm: Trước khi kiểm 44 hộp, sau khi kiểm 0 hộp.

- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: Khi gặp một vật thể ở xa bị mờ, không rõ là xe bán tải (van) hay xe con (car), tôi đã ghi chú lại góc ảnh, gắn nhãn tạm thời kèm đánh dấu needs_review và xin ý kiến từ Lab Coach. Sau khi nhận được phản hồi, tôi đã điều chỉnh nhãn cho phù hợp.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: 0, 0.267422, 0.50518, 0.097312, 0.067922

- Tên lớp và tọa độ điểm ảnh `xyxy`:
    - Lớp = 0 (`car`)
    - Pixel xyxy: [140.0, 301.6, 202.3, 345.1]

- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?
    - Nhầm loại: Khoanh chiếc xe tải nhưng lại điền xe con.
    - Khoanh lệch: Số chỉ vị trí bị lệch ra ngoài lề, không trúng chiếc xe.
    - Vẽ xấu: Khung quá to (chứa nhiều khoảng trống) hoặc quá nhỏ (bị cắt mất nửa chiếc xe).

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: `drive_022.jpg`, `drive_033.jpg`, `drive_038.jpg`

- Mã ảnh thẩm định: `drive_008.jpg`

- Mô tả một dự đoán trong `detect_result.jpg`: Máy tính tự chạy thử trên ảnh drive_008, nhưng do mới chạy thử 4 vòng (stop sớm ở epoch 1) nên kết quả nhận diện và độ chính xác mAP50 còn rất thấp (0.00534).

- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào?
Cần kiểm tra lại cách phân biệt xe con (car) và xe van (van), vì trong bài số lượng xe con đang chiếm quá nhiều (92/119 khung).

- Minh chứng nào có thể bác bỏ nhận định của bạn?
Nếu cho máy tính học nhiều hơn (50-100 vòng) mà nó đoán đúng hết, chứng tỏ dữ liệu ban đầu không bị lỗi.

- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?
Vì 4 ảnh là quá ít. Máy tính rất dễ "học vẹt" 4 ảnh này, khi đưa ra ngoài thực tế gặp thời tiết hoặc góc chụp khác sẽ không nhận diện được.

## 6. Đối chiếu nhãn

- Số hộp ghép được: 48
- IoU trung bình và trung vị: Mean IoU = 0.8649 (khớp ~86.5%), Median IoU = 0.8820
- Mức đồng thuận lớp: 72.92%
- Số hộp phía bạn không ghép được: 71
- Số hộp phía đối chiếu không ghép được: 2
- Một điểm khác biệt cụ thể: Bài khoanh thêm nhiều xe nhỏ mờ ở xa mà bài mẫu bỏ qua.
- Quy tắc hoặc hành động sửa phát sinh: Thống nhất quy định: Xe quá nhỏ hoặc quá mờ ở xa thì không cần khoanh để tránh khoanh thừa.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng? 
Vì có thể cả hai người làm đều cùng hiểu sai một quy tắc giống nhau.

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach: Minh chứng mạnh nhất: Hai file xuất CVAT và YOLO của em khớp nhau tuyệt đối (IoU = 0.99986) và khung khoanh khớp bài mẫu tới 86.5%.

Câu hỏi cho Lab Coach: Đối với các vật thể bị che khuất vượt quá 70% diện tích nhưng vẫn nhận diện rõ bằng mắt thường (ví dụ chỉ thấy một phần đầu xe) thì quy chuẩn của dự án nên tiếp tục gán nhãn kèm thuộc tính occluded hay nên bỏ qua để tránh gây nhiễu cho mô hình ạ?
