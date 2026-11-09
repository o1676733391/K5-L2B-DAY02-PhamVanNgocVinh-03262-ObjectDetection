# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Phương tiện chở người cá nhân hoặc gia đình từ 4 đến 7 chỗ ngồi (sedan, hatchback, SUV, crossover, coupe, convertible). | Thân xe có mui trước/sau rõ ràng, chiều cao tổng thể vừa phải, khoang hành lý nằm trong thân xe hoặc cốp sau thấp. |
| `truck` | Xe tải vận chuyển hàng hóa các loại có cấu trúc cabin tách rời cơ học với thùng xe hoặc sàn chở hàng phía sau (xe tải thùng, xe ben, xe bồn, container, bán tải chuyên dụng hạng nặng). | Cabin điều khiển phía trước tách biệt với thùng hàng/sàn phẳng phía sau; đuôi xe thường vuông vức, có bửng nâng hoặc thùng cao. |
| `bus` | Xe buýt công cộng, xe khách liên tỉnh, xe đưa đón học sinh/du lịch cỡ lớn chuyên chở nhiều hành khách. | Kích thước vượt trội về chiều dài và chiều cao so với xe con; thân xe dài hình hộp chữ nhật lớn với nhiều hàng ghế và dãy cửa sổ kính liên tục. |
| `van` | Xe tải van, xe thùng chở hàng nhẹ (cargo van) hoặc xe chở khách đa dụng cỡ vừa (minivan, microbus như Ford Transit, Toyota Hiace, Mercedes Sprinter). | Thân xe dạng đơn khối hình hộp (one-box / mono-volume), trần xe cao và bằng phẳng từ đầu đến đuôi; không có thùng hàng tách rời như `truck` và không dài/nhiều khoang khách lớn như `bus`. |

## Quy tắc chung

- **Box bám sát vật thể nghĩa là:** Bounding box phải ôm sát các điểm cực biên ngoài cùng của phần thân xe thực tế nhìn thấy được trong ảnh. Không bao gồm mặt đường phía dưới, vỉa hè, bóng đổ (shadow), nền trời hoặc thân xe liền kề.
- **Vật thể bị che khuất (Occlusion):** Nếu xe bị che khuất một phần bởi xe khác, cây cối, rào chắn hoặc biển báo nhưng vẫn nhận diện được rõ lớp của xe, đóng box bao quanh đúng phần thân xe nhìn thấy được (không đoán phần bị khuất). Nếu hai xe chồng lấn, vẽ hai box độc lập, không gộp thành một box chung.
- **Vật thể bị cắt bởi rìa ảnh (Truncation):** Cạnh box phải chạm đúng mép viền ảnh (tọa độ pixel 0 hoặc 640; normalized 0.0 hoặc 1.0). Tuyệt đối không tự suy đoán phần thân xe nằm ngoài khung hình.
- **Vật thể rất nhỏ/mơ hồ:** Chỉ gán nhãn khi vật thể vẫn hiển thị đủ đặc trưng hình học để nhận diện được lớp (ví dụ các xe xa ~15–25px ở hậu cảnh). Nếu vật thể chỉ còn lại đốm mờ dưới 10–12px không thể xác định chắc chắn loại phương tiện, bỏ qua không gán để tránh tạo False Positive.
- **Vật thể ngoài schema:** Không gán người đi bộ, xe đạp, mô tô/xe máy, biển báo giao thông, cột đèn, hình ảnh xe trên bảng quảng cáo hoặc bóng phản chiếu trong gương kính.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_052.jpg` | **Box 7** (`[207.3, 131.8, 339.8, 551.3]`): Xe cỡ lớn ở tiền cảnh bên trái, góc nhìn từ trên xuống; dễ phân vân giữa `car` (SUV to) và `van`. | Gán `van` (Class 3) | Thân xe đơn khối hình hộp chữ nhật dài, trần xe cao bằng phẳng từ cabin đến đuôi, thuộc dòng xe van thương mại/chở hàng nhẹ chứ không phải SUV gia đình. |
| `drive_052.jpg` | **Box 0** (`[459.7, 8.8, 477.2, 38.3]`): Xe ở vị trí rất xa trên giao lộ, kích thước nhỏ (~17.5×29.5 px); dễ nhầm với xe con sedan. | Gán `truck` (Class 1) | Quan sát thấy cabin phía trước tách biệt cơ học với phần thùng hàng phẳng/cao phía sau, mang kết cấu đặc trưng của xe tải khác với mui sedan dốc của Box 1 ngay cạnh. |
| `drive_052.jpg` | **Box 6** (`[598.0, 161.0, 640.0, 247.0]`): Xe con di chuyển vào khung hình ở góc phải, bị rìa phải ảnh cắt mất nửa thân xe. | Gán `car` (Class 0), mép phải box chạm đúng viền ảnh `xmax = 640.0` (`1.0`) | Tuân thủ quy tắc truncation: chỉ vẽ box ôm sát nửa thân xe nhìn thấy được trong ảnh, cạnh box áp sát biên ảnh, không ước đoán nửa xe còn lại ngoài ảnh. |
| `drive_051.jpg` | **Box 3** (`[494.8, 19.0, 565.2, 148.0]`): Xe lớn màu trắng bên lề đường, phân vân giữa van chở khách dài và bus. | Gán `bus` (Class 2) | Chiều dài vượt trội (>125 px), thân xe lớn có dãy cửa sổ hành khách nhiều khoang đặc trưng của xe buýt/xe khách lớn. |
