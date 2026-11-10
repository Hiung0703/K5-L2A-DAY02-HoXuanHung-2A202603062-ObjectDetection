# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp     | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| ------- | ------------------ | -------------------- |
| `car`   | Xe con 4 chỗ hoặc 7 chỗ, sedan, SUV, taxi, bán tải nhỏ | Phân biệt với van bằng phần cabin và thùng ngắn hơn; không phải xe tải lớn |
| `truck` | Xe tải chở hàng, xe đầu kéo, thùng dài hoặc cabin cao | Thùng chở hàng rõ ràng, kích thước lớn hơn van và car |
| `bus`   | Xe buýt chở khách, minibus | Có nhiều cửa sổ dọc thân xe, kích thước dài hơn van |
| `van`   | Xe van, minivan dùng để chở hàng hoặc chở người | Thân xe hộp, cao hơn car nhưng nhỏ hơn bus, thường có cửa trượt |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: chỉ bao quanh phần thân xe nhìn thấy được, không lấy thêm mặt đường, bóng đổ hay xe bên cạnh.
- Vật thể bị che khuất: vẫn gán box quanh phần còn nhìn thấy nếu xác định được lớp. Nếu bị che quá nhiều đến mức không chắc class thì bỏ qua.
- Vật thể bị cắt bởi rìa ảnh: box chạm đúng mép ảnh, không đoán phần nằm ngoài khung hình.
- Vật thể rất nhỏ/mơ hồ: nếu chỉ còn một mảnh nhỏ hoặc bị nhòe nặng đến mức không nhận ra loại xe thì không gán.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh          | Tình huống                                      | Quyết định cuối      | Lý do theo schema |
| ------------ | ----------------------------------------------- | -------------------- | ----------------- |
| `drive_003`  | Nhiều xe đứng sát nhau, đông đúc, một số xe bị che lẫn | Vẫn vẽ box riêng cho từng xe nhìn thấy được | Mỗi xe là một object riêng, dù bị che một phần vẫn gán nếu còn nhận diện được lớp |
| `drive_013`  | Xe đang di chuyển nên bị nhòe nhẹ               | Vẫn gán bình thường  | Nhòe nhẹ vẫn nhận ra hình dáng và lớp, không phải trường hợp quá mơ hồ |
| `drive_007`  | Xe đứng phía sau hàng rào / bị bóng cây che một phần | Gán box quanh phần thân xe còn nhìn thấy | Bị che khuất một phần nhưng vẫn xác định được là car/truck nên vẫn giữ lại |
