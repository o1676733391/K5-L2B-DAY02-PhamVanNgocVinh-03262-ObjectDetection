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
    
